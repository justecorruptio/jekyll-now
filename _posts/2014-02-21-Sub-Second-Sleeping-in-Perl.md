---
layout: post
title: Sub-Second Sleeping in Perl
tags: Code Golf Perl
---

In perl, we can't use the `sleep` function to do a sub-second sleep, because it takes an integer argument as the number of seconds.  Normally, the cleanest way to do this is via the `Time::HiRes` module.  For example if I wanted to sleep for a 100th of a second I would write:

```perl
use Time::HiRes qw(usleep);
usleep(10000);
```
     
Pretty straight forward, but in the land of golfing efficiency, this is a few too many keystrokes.  You may be tempted to shell out to use the unix `sleep` command, which will take a float of seconds, eg.

```perl
`sleep .01`;
```

But we've lost a bit of "purity."  In my travels, I've come across this little gem which will achieve such a sleep:

```perl
select$a,$a,$a,.01;
```

Here, `$a` is an undefined scalar.  What's happening is that `select` will block for .01 seconds waiting to see if any of the first three parameters is a file handle with data ready to be read.  Since all three are `undef`, we block for all .01 seconds.  Voila, a shorter way to express a sub-second sleep.
