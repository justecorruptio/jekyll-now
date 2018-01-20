---
layout: post
title: Thelen's Device
tags: Code Golf Perl
---

The other night, I learned about Thelen's Device.  Apparently, invented in 2002, eight years after Perl 5 was first released, someone typed in the correct sequence of characters to make interesting things happen that Larry Wall never dreamed of.

So without further ado, here it is in all its glory:

```perl
\@x[@x]
```
    
Annnd, that's it. So let's actually look at a use for this:

```perl
$ perl -le '@x = (2, 3, 5, 1); \@x[@x]; print $#x;'
5
```
    
What happened here? We found the maximum value for the array `@x`.  But how did it do that?

`\@x[@x]` is equivalent to `\@x[2, 3, 5, 1]`. When we try to index 5 from the `@x` array, it's undefined, so Perl auto vivifies `@x` up to the 5th index. `$#x` then returns the last index of `@x` which is now 5. And there you have it.