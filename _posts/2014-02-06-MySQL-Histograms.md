---
layout: post
title: MySQL Histograms
tags: Code SQL
---

So the other day, I needed to make a histogram of data that lived in MySQL.  So here's a quick and dirty way.

```sql
SELECT value, RPAD("", value, "*") FROM `table`;
```

Thats it.  And here's what it looks like:

```sql
+-------+----------------------+
| value | RPAD("", value, "*") |
+-------+----------------------+
|     1 | *                    |
|     2 | **                   |
|     3 | ***                  |
|     5 | *****                |
|     7 | *******              |
|    11 | ***********          |
|    12 | ************         |
|    10 | **********           |
|     6 | ******               |
|     4 | ****                 |
|     2 | **                   |
|     1 | *                    |
+-------+----------------------+
```

Simple Huh.
