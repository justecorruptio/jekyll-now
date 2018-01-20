---
layout: post
title: Detecting Korean in MySQL
tags: Code Language MySQL
---

An interesting question popped up at work the other day: how to detect if a column in MySQL contains Korean characters?

After dealing with languages that support Unicode strings, the first thought is to try detecting a character within the range of Korean code points. Normally that would work, say in Python:

```python
>>> bool(re.search(ur'[가-힝]', u'안녕하세요'))
True
```

The issue is that out-of-the-box MySQL doesn't support Unicode operations. Instead, Unicode strings are stored as UTF-8, and all operations are done against the UTF-8 byte values. So, the following two `RLIKE` statements are equivalent (and incorrect).

```sql
SELECT 'Здравствуйте' RLIKE '[가-힝]';
SELECT 'Здравствуйте' RLIKE CONCAT('[', X'EAB080', '-', X'ED9E9D', ']');
```
    
Both will trigger because that Russian phrase in UTF-8 contains a character in the `0x80` to `0xED` range. What we really need is to be able to detect a 3 byte sequence between ```'EAB080'``` and ```'ED9E9D'```. Luckily, this is doable in regular expressions, albeit very ugly:

```python
>>> bool(re.search(
...     '\xEA\xB0[\x80-\xFF]|\xEA[\xB1-\xFF].|'
...     '[\xEB-\xEC]..|\xED[\x00-\x9D].|\xED\x9E[\x00-\x9D]',
...     u'안녕하세요'.encode('utf-8'),
... ))
True
```

One more kink in our solution is that MySQL strings are NUL (`'\0'`) terminated, so the `[\x00-\x9D]` character class would actually terminate the string literal. This is easily solved by recognizing that we can always use re-encode these with inverted character classes. So, the previous example is equivalent to [^\x9E-\xFF`.

So now we have all our pieces to create a Korean character detector. The last bit of polish we want to add is that sticking HEX coded literals in CONCATs are very unwieldy. ie., the previous regular expression would have to be written as:

```sql
CONCAT(X'EAB0', '[', X'80', '-', X'FF', ']|', ...
```

I'm not even going to bother finish typing that. Instead we can encode the regex command characters into HEX also. So `'['` becomes `X'5B'`, `'|'` becomes `X'7C'`, etc. In the end, our Korean detector looks like this:

```sql
mysql> SELECT '안녕하세요' RLIKE CONCAT(
    ->     X'EA28B05B802DFF5D7C5BB12DFF5D2E297C5BEB2DEC',
    ->     X'5D2E2E7CED285B5E9E2DFF5D2E7C9E5B5E9E2DFF5D29'
    -> ) as has_korean;
+------------+
| has_korean |
+------------+
|          1 |
+------------+
```
    
Of course, I didn't actually write that out by hand. Check out the (very rough) [generator script](https://gist.github.com/justecorruptio/9b0fd15860eee90493e2).