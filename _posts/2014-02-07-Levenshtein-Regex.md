---
layout: post
title: Levenshtein Regex
tags: Code Python Regex
---

In my day job, I sometimes come across little programming puzzles that are sort of related to, but have no real use in my current project.  They usually result in little snippets of code or random techniques that I'll file away in the "curios" section of my brain.

One such problem was: **Can we construct a regular expression that matches strings of N Levenshtien distance away from a given string?** As quick reminder, the Levenshtein (or edit) distance is the number of changes needed to turn one string into another.  The allowed changes are: add, remove, or replace a character; eg. `CHEAT` is one edit distance away from `CHEST` because we only have to make one change `A->S`.

Lets start by limiting ourselves to an edit distance of one; this is of course possible.  The naive method is just listing out every possible edit, and alternating.  Like so:

```python
def leven_regex(word):
    edits = set()
    for i in xrange(len(word)):
        # Add a char
        edits.add(word[:i] + '.' + word[i:])
        # Remove a char
        edits.add(word[:i] + word[i+1:])
        # Replace a char
        edits.add(word[:i] + '.' + word[i+1:])
    # Add a char to the end
    edits.add(word + '.')

    return '|'.join(edits)

print leven_regex('ABC')
# Results in:
# AB.C|AC|AB|A.C|A.BC|ABC.|BC|.ABC|AB.|.BC
```

Pretty straight forward, but kinda lacks elegance†.  It feels like we're duplicating a lof of logic in each part, let's think about this a bit more.

In the case of one edit distance, the edit can only occur in one place in a string; so if it's at the beginning, then it can't be at the middle nor the end.  Given this, we can build a little heuristic: eg. given the string `ABCDE`, we construct a regex that matches an edit occurring at the beginning: `.?A?BCDE`.  This handles additions, removals, and replacements.  Similarly, from `ABCDE`, we can match edits at the end with `ABCDE?.?`.

Good, now what about the middle? Well, if it's in the middle, the edit wouldn't have touched `A` nor `E`, so all we're left with is how to build a regex to handle an edit in `BCD`.  AHA! We're asking the exact question as before, so recursion to the rescue!


```python
def leven_regex(word):
    # Base cases
    if len(word) == 0:
        return '.?'
    elif len(word) == 1:
        return '.?%s?|%s?.?' % (word, word)

    # Inductive case
    else:
        begin = '.?%s?%s' % (word[0], word[1:])
        end = '%s?.?' % (word,)

        inner = leven_regex(word[1:-1])

        mid = '%s(%s)%s' % (
            word[0], inner, word[-1])

        return '|'.join([begin, mid, end])

print leven_regex('ABC')
# Results in:
# .?A?BC|A(.?B?|B?.?)C|ABC?.?
```

And there you have it, a sweet little regex that matches strings one edit distance away. Alas, this method doesn't extend to the two or more edits cases.  But yeah, time spent on interesting sorta useful stuff.

<sub>
†: Actually, a better reason why this method is lacking is that python 2.7 re engine doesn't compile this into a DFA, and will traverse the alternated strings one by one.  This becomes a problem for even medium lengthed input strings.
</sub>
