---
layout: post
title: A Typical Golfing Session
tags: Code Golf JavaScript
---

My last post was about why I golf.  Now, I will explain the how and my personal thought process.  As a side note, golfing isn't for the impatient there are many times I will fruitlessly stare at 40 characters for an hour only to concede that there is not shorter representation.  But now for some code, the program I will be golfing the [Sieve of Eratosthenes](http://en.wikipedia.org/wiki/Sieve_of_Eratosthenes) up to 100,000 in javascript.

First of all, we need to actually write a working version of the program *(244 chars)*:

```javascript
var max = 100000;
var sieve = [];
for(var pos = 2; pos < max; pos++) {
    var is_prime = ! sieve[pos];
    if(is_prime) {
        console.log(pos);
        for(var i = pos + pos; i < max; i+= pos) {
            sieve[i] = 1;
        }
    }
}
```

That is clean code.  Now let's start butchering.  First let's remove everything unecessary like vars, parentheses, and spacing; and make variables one character *(138 chars)* .

```javascript
x=100000;
s=[];
for(p=2;p<x;p++){
    v=!s[p];
    if(v){
        console.log(p);
        for(i=p+p;i<x;i+=p)
            s[i]=1;
    }
}
```

Next, we notice that there's a shorter way to write 100000; stuff `console.log(p)` into the next for loop, and `i=!s[p]` into the if clause *(107 chars)*.

```javascript
x=1e5;
s=[];
for(p=2;p<x;p++)
    if(!s[p])
        for(console.log(p),i=p+p;i<x;i+=p)
            s[i]=1;
```

Now we can flat out remove all the whitespace *(76 chars)*.

```javascript
x=1e5;s=[];for(p=2;p<x;p++)if(!s[p])for(console.log(p),i=p+p;i<x;i+=p)s[i]=1
```
    
Things will then get complicated fast!  Next, we don't care about the first value of the sieve, and lets re-jigger the inner for loop *(72 chars)*.

```javascript
x=1e5;for(s=[p=2];p<x;p++)if(!s[p])for(console.log(p),i=p;i<x;)s[i+=p]=1
```

At this point, I will spend 2-3 minutes per character removed.  I then notice that defining `x` is longer than using the max explcitly, and `i` can be initialized earlier *(68 chars)*.

```javascript
for(s=[p=2];p<1e5;p++)if(!s[i=p])for(console.log(p);i<1e5;)s[i+=p]=1
```

Re-jigger the outer for loop, another 5 minutes here *(67 chars)*.

```javascript
for(s=[p=1];++p<1e5;)if(!s[i=p])for(console.log(p);i<1e5;)s[i+=p]=1
```

After a while, like a scupltor stepping back to look at his work, you conclude that to the best of your ability, nothing else can be removed, and so we finish.  This was a fairly short example, but I hope I conveyed the process and reasoning behind how I golf.

<sub>As I'm writing this post, I realize that it can be shortened still.  Exercise for the reader.</sub>