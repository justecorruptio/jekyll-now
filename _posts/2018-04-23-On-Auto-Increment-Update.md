---
layout: post
title: On Auto Increment Update
tags: MySQL
---

Something happened recently that, in hindsight, I would consider as a frustrating milestone. Without spoiling anything, let me frame the setting.

There was a MySQL database that tracks widgets, mapping a its name to its value. (The real table was actually much wider and contained many other columns.)

```sql
CREATE TABLE `Widgets` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) DEFAULT '',
  `value` varchar(128) DEFAULT '',
  `modified_ts` timestamp NOT NULL
      DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  UNIQUE KEY `name_idx` (`name`),
  KEY `modified_idx` (`modified_ts`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

New widgets are created all the time; but sometimes, the widgets are updated so we would use a query like the one below to replace the value of the widget whenever the name collides.

```sql
INSERT INTO Widgets (`name`, `value`)
VALUES ('Cake', 'Key Lime')
ON DUPLICATE KEY UPDATE value = VALUES(value);
```

We would then use `MAX(modified_ts)` as a sanity check in monitoring to make sure that this table was kept fresh.

One day, without any deviation from the norm in our monitors, we noticed via other means that no new widgets were getting added into our table. In fact, it was very spectacularly deceptive in how the way it failed. For instance, looking at the data we saw something like this:

```sql
mysql> SELECT name, value, modified_ts FROM Widgets 
    -> ORDER BY modified_ts DESC LIMIT 10;
+-----------+-----------+---------------------+
| name      | value     | modified_ts         |
+-----------+-----------+---------------------+
| Sausage   | Vienna    | 2018-03-23 05:23:53 |
| Vegetable | Kale      | 2018-03-23 01:12:19 |
| Soda      | Diet Coke | 2018-03-23 01:11:29 |
| Cake      | Vanilla   | 2018-03-23 01:11:10 |
...
```

Notice the large jump in time between the most recent and penultimate. Looking at logs from the system generating these rows, everything seems to be functional. The behavior seems to be that the most recent row was getting updated over and over -- which in turn kept our `MAX(modified_ts)` monitor happy.

So what the heck happened? The first thing we investigated was to make sure that the `name` column wasn't somehow getting stuck; ie. it was correctly attempting to insert rows with different `name`s.

After a while, we ran a full `SELECT *` query (we had not done before because the table was so wide) and immediately saw the issue:

```sql
mysql> SELECT * FROM Widgets ORDER BY modified_ts DESC LIMIT 10;
+------------+-----------+-----------+---------------------+
| id         | name      | value     | modified_ts         |
+------------+-----------+-----------+---------------------+
| 2147483647 | Sausage   | Vienna    | 2018-03-23 05:23:53 |
| 2147483646 | Vegetable | Kale      | 2018-03-23 01:12:19 |
| 2147483645 | Soda      | Diet Coke | 2018-03-23 01:11:29 |
| 2147483644 | Cake      | Vanilla   | 2018-03-23 01:11:10 |
...
```

Those ids look awfully familiar... 

So, `2147483647` is 2<sup>31</sup> - 1 which is the maximum value in an unsigned `int` column in MySQL. Since the column is defined with `AUTO_INCREMENT`, the next insert would have tried to use id 2<sup>31</sup>, which is outside of the range of `int`.

MySQL, with the best of intentions, notices that the value is too large and uses the largest value it can: 2<sup>31</sup> - 1. But that id as already taken, so it triggers the `ON DUPLICATE KEY UPDATE` logic and merrily updates that most recent row.

It's not everyday that this kind of data issue come up. We never dreamed we would reach > **2 Billion** widgets 6 years ago, but here we are now.