---
layout: post
title: Matrix Spiral
tags: Code Golf Python Regex
---

There's a sort of running gag at work: "Hey, look at this cool thing I wrote."  "Yes, but can you do it in one line of python?"  This happened when we started talking about interview questions that my manager likes to use.  His current favorite is: **Given a matrix, list the elements in clock-wise spiral order.**  For example:

```python
[[1, 2, 3],
 [4, 5, 6],
 [7, 8, 9]]
```

 would be listed as `[1, 2, 3, 6, 9, 8, 7, 4, 5]`.

Seems simple enough, this is a question we'll use for junior engineers to test out their chops.  But, can we do this in one line of python?

The approach we'll use here is to strip off the top row of the matrix, then strip the right column, then the bottom row, then the left side, etc. until nothing is left.  Notice that this is equivalent to stripping off the top row, rotating the matrix 90° CCW and repeat.  So how do you rotate a matrix concisely?

An interesting property of matrix rotations is that they can be done via transpositions and flips; specifically, a 90° CCW rotation is equivalent to a transpose followed by a vertical flip.  Thankfully there are two very simple tricks in Python that achieve these operations.

First, to transpose a matrix, we can use the `zip()` function.

```python
>>> A = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> zip(*A)
[(1, 4, 7),
 (2, 5, 8),
 (3, 6, 9)]
```

Note the star in `zip()`'s argument, and that the rows are tuples.  I'll leave it as an exercise for the reader to figure out *why* that works.

Next to vertically flip a matrix we can use a negative step value in a list slice: ie. `[::-1]`.  So, the complete rotation can be written:

```python
>>> A = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> zip(*A)[::-1]
[(3, 6, 9),
 (2, 5, 8),
 (1, 4, 7)]
```

Now that we have all the pieces we can write our spiral function.

```python
def spiral(M):
    result = []

    while M:
        top_row = M.pop(0)
        result.extend(top_row)
        M = zip(*M)[::-1]

    return result

A = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
print spiral(A)
# Results in:
# [1, 2, 3, 6, 9, 8, 7, 4, 5]
```

But, I said at the beginning, we're gonna make this a one-liner.  After some drastic golfing, here is my 62 character version:

```python
spiral=lambda M:M and list(M[0])+spiral(zip(*M[1:])[::-1])or[]

A = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
print spiral(A)
# Results in:
# [1, 2, 3, 6, 9, 8, 7, 4, 5]
```

Once again I'll leave it to the reader to see if they can figure out how this works, and if they can get this any shorter.  Also as a side note, this is a horrible answer to the original interview question, as the runtime complexity is *much* worse than it should be.
