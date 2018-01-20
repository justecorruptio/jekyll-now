---
layout: post
title: The Most Useful Letters
tags: Code Golf Perl
---

I was posed a fun question the other day: If you only have 10 letters on your keyboard, which ten letters allow you to type the most number of different words. As this was posted on Twitter, I wanted to give the answer, and include the generating code.

My solution was to remove take a dictionary, and remove the least necessary letter one by one, until I had ten left.

Here is my code golfed solution:

```perl
@W=<>;@L=A..Z;for(A..P){@m=0;for$l(@L){$b=$l,@m=@w if(@w=grep!/$l/,@W)>@m}@L=grep!/$b/,@L;@W=@m}print@L
```

As for the letters, they are: `ACEILNORST` which can make 8896 different words (according to the TWL dictionary).