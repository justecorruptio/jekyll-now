---
layout: post
title: Slice in Store Context
tags: Code Python
---

After 7 years of working with Python, there's always something basica to learn... So I was playing around with the `ast` module the other day; investigating something completely unrelated, but then I got genuinely nerd-sniped.  I typed this into the REPL:

```python
>>> import ast
>>> tree = ast.parse('a[0:1]', mode='eval')
>>> ast.dump(tree.body[0].value)
"Subscript(value=Name(id='a', ctx=Load()), slice=Slice(lower=Num(n=0), upper=Num(n=1), step=None), ctx=Load())"
```
    
Ok, this is what I was expecting.  Wait, why does the `Subscript()` object have a `ctx` attribute with a `Slice()`?!? So `Load()` makes sense, but can we also have a slice in `Store()` context?!?

Then I smacked myself in the face, of course you can -- This is how assignments into lists work...

```python
>>> tree = ast.parse('a[0] = 4')
>>> ast.dump(tree.body[0].targets[0])
"Subscript(value=Name(id='a', ctx=Load()), slice=Index(value=Num(n=0)), ctx=Store())"
```
    
Nope, it looks like just a single element assignment is an `Index()` object.  That makes sense too.  So let's try an actual slice.

```python
>>> tree = ast.parse('a[0:5] = a')
>>> ast.dump(tree.body[0].targets[0])
"Subscript(value=Name(id='a', ctx=Load()), slice=Slice(lower=Num(n=0), upper=Num(n=5), step=None), ctx=Store())"
```
    
Yup, `Slice()` in a store context; A random corner of Python I didn't know about. It does pretty much what you'd expect: replaces a slice out of a list with the right hand side. For example (notice that the length of the slice doesn't have to match the right hand side):

```python
>>> a = [1, 2, 3, 4, 5]
>>> a[1:3] = 'abc'
>>> a
[1, 'a', 'b', 'c', 4, 5]
```
    
Ok, let's really have fun. We all know the recipe to reverse a list: `a = a[::-1]` This is taking a slice and using a negative step to walk backwards. Pretty straight forward. But, we can also do this the other way around, walk the list forward, and slice it in backwards!

```python
>>> a = [1, 2, 3, 4, 5]
>>> a[::-1] = a
>>> a
[5, 4, 3, 2, 1]
```
    
It works! Since we can assign to lists with arbitrary steps, I can think of one obvious use for this:

```python
>>> N = 50
>>> S = range(N + 1)
>>> for i in xrange(2, int(N ** .5)):
...     if S[i]:
...         S[i + i::i] = [0] * (N / i - 1)
...
>>> [x for x in S if x > 1]
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 49]
```
    
It's a horribly inefficient implementation of the Sieve of Eratosthenes! I'm sure there are legitimate uses for this feature, but it's fun none the less.