---
layout: post
title: Uneven Merge Shuffle
tags: Code Python Algorithm Snatch
---

This is will probably be the first installment in a series of posts about an online multiplayer word game I'm developing as a side project: [Snatch!](http://snatch.cc). The process has brought up a lot of interesting topics, and I would like to share some of them.

The central mechanic of the game is that letter tiles are periodically pulled from a bag and players must make words out of them. During testing, we quickly realized that long runs of vowels or consonants make for poor gameplay. We needed the tiles to be random, but not *too* random. Internally, Python uses a [Fisher-Yates Shuffle](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle) which guarantees a perfect shuffle, has a chance to clump vowels and consonants together. In this short example, we have a bag containing only the consonant `c` and vowel `A`:

```python
letters = list('ccccccccccccAAAAAAAA')
random.shuffle(letters)
print ''.join(letters)
# produces: cccccAAAcAcAcccAAccA
```

From this short example of 20 letters, we can already see that the first 5 letters are consonants and words can't be made until the next tile is drawn from the bag. We must decrease the chance that situations like this occurs. It is also worth noting that 60% of the letters are consonants, because English words tend to use them more frequently.

Another approach could be to evenly interleave the two, ie. `cAccAccAccAccAccAccA`, but being too predictable is also not good for gameplay.

A third naive approach of statistically pulling vowels or consonants also exhibits this problem long runs of characters:

```python
consonants = list('cccccccccccc')
vowels = list('AAAAAAAA')
output = []
while consonants and vowels:
    if random.random() < .4:
        output.append(vowels.pop())
    else:
        output.append(consonants.pop())
# append the leftovers
output.extend(vowels + consonants)
print ''.join(output)
# produces: cAccccccccccAAcAAAAA
```

---

So, this is the solution I've come up with, which I'm calling an "Uneven Merge Shuffle." The basic premise is to subdivide the list of consonants and vowels near *(but not at!)* the middle, and recursively merge the pieces. The base case is: if one list is empty, then the other list is returned. Here's an example of shuffling two short lists:

```
Inductive Case: Split each list near the middle.
(cccccc, AAAA)
(cccc, AA) + (cc, AA)
(ccc, A) + (c, A) + (, A) + (cc, A)
(c, A) + (cc, ) + (c, A) + (, A) + (, A) + (cc, )
(c, ) + (, A) + (cc, ) + (, A) + (c, ) + (, A) + (, A) + (cc, )

Base Case: If a list is empty, return the other
c + A + cc + A + c + A + A + cc

Output: Join the result
cAccAcAAcc
```

The main idea is to spread both lists out evenly, but not perfectly. And here it is a naive implementation in python:

```python
def random_pivot(x):
    # Choose a pivot near the middle
    # 45 / 100 and 55 / 100 are tunable parameters
    lx = len(x) + 1
    lower = lx * 45 / 100
    upper = lx * 55 / 100

    return random.randint(lower, upper)

def uneven_merge_shuffle(a, b):
    if not a:
        return b
    if not b:
        return a

    pa = random_pivot(a)
    pb = random_pivot(b)

    return (
        uneven_merge_shuffle(a[:pa], b[:pb]) +
        uneven_merge_shuffle(a[pa:], b[pb:])
    )

consonants = list('cccccccccccc')
vowels = list('AAAAAAAA')
output = uneven_merge_shuffle(consonants, vowels)
print ''.join(output)
# produces: AcAccAcAccAcccAcAcAc
```

The `random_pivot()` function chooses a splitting point in the list roughly between 45% and 55% of the length.

I'll leave the analysis as an exercise for the reader, but the time complexity of this function is `O(N)`; The space complexity is also `O(N)` with a large constant factor because every slice operation has to make a copy of that piece of the list. Realizing that the entire operation is depth-first, we can build the resulting list incrementally instead:

```python
def uneven_merge_shuffle(a, b):
    out = []

    def random_pivot(x, y):
        l = y - x + 1
        lower = x + l * 45 / 100
        upper = x + l * 55 / 100

        return random.randint(lower, upper)

    def recur(ax, ay, bx, by):
        if ax == ay:
            out.extend(b[bx:by])
            return
        if bx == by:
            out.extend(a[ax:ay])
            return

        ap = random_pivot(ax, ay)
        bp = random_pivot(bx, by)

        recur(ax, ap, bx, bp)
        recur(ap, ay, bp, by)
        return

    recur(0, len(a), 0, len(b))

    return out

consonants = list('cccccccccccc')
vowels = list('AAAAAAAA')
output = uneven_merge_shuffle(consonants, vowels)
print ''.join(output)
# produces: cAcAAcAcccAccAcAccAc
```

There are definitely improvements to be made to increase performance like: finding an in-place solution using only swap operations, use multiply-->bit-shifting instead of division, and handling common base cases... but this is good enough for now. 
