---
layout: post
title: "Fun Bugs I've Fixed #3: It's a Wrap!"
tags: Linux Bugs
---

There was once an old system that did things. It did those things well for five years; but then one day, it stopped doing some of the things.

You see this system was in charge of doing a whole lot of things, so it forked 10 copies of itself, so that each copy could take care of a smaller portion. The system kept track of each forked child by tracking its PID. If it detected that a child was misbehaving, it forked new one, made sure that the old one was dead so that nothing was accidentally done twice, and everyone would carry along merrily.

There was also an ingenious way the system divided the labor. It assigned each of the children a number. Child #0 would do the things that ended in a 0, child #1 would take care of all the 1's, and so-forth.

So on one fateful day, thing number 73, and 83, and 93 didn't get processed, we knew exactly that one one of the children was misbehaving. When we looked at the running processes, and child #3 didn't exist! How can that be? The forking logic was so simple.

At this point, I'll give the reader an opportunity to solve this mystery. There should be enough evidence above to diagnose the issue.

---

So we must first look at how the operating system allocated PIDs. On Linuxes, PIDs are assigned incrementally; starting with 1 for the first process that is started after boot.

Now there is only a finite amount of resources on a machine, so the PID doesn't grow unbounded. When it's allocated PIDs up to a certain maximum number, Linux will wrap around and try to start from 1 again, but only giving new processes a PID that isn't currently being used. By default, Linux will set the maximum PID to be `2^15 = 32768`. You can check this value with `cat /proc/sys/kernel/pid_max`.

Back to issue at hand, child #3 had died at some point; but between the time that it spawned and died, the OS had allocated every single PID up to the maximum, and had wrapped all the way back around. So when it came time to allocate a new PID, it happened to be the exact same PID as the old child!

The logic in that system first forked a new copy of the child, but when it went to make sure that the old child was dead, it looked for the same PID and accidentally killed the new child instead.

Thus, the fix was easy: kill the old child first, *then* spawn a new one.