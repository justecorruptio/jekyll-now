---
layout: post
title: The Mystical Regular Expression
tags: Regex
---

For some time now, I've seen an interesting trend in some developer circles that have started to expound the amazing attributes of regular expressions.  As if these were magical creatures, once harnessed, can solve all of your validation/log processing/grep woes.  We see this is the new-fangled regex "checkers", "explainers", and "creators". This is probably a good thing for our community in terms of democratizing a tool that people can use without understanding DFAs, minimization techniques, backtracking performance, etc.; stuff you used to learn for a 4-year degree. 

One thing does irk me though, as it is a tool, there seems to be a lack of teaching the appropriateness of the tool itself.  One example of this pitfall I've seen in some python code is this:

```python
import re
PREFIX_REGEX = re.compile(r'^ba')
if PREFIX_REGEX.match('banana'):
    print "BANANAS!"
```
        
In many cases, there is a less "sexy" more performant way to rewrite these.  Of course, each language is different, some don't have a `strip()`, or a `chomp()`, or an `rsplit()`. For the above example, we could have simply done:

```python
if 'banana'.startswith('ba'):
    print "BANANAS!"
```
    
Let's make sure we're not hammering our screws.