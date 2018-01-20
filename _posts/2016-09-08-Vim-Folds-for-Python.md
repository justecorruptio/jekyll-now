---
layout: post
title: Vim Folds for Python
tags: Vim Python
---

After spending the last couple of nights fiddling with my `.vimrc`, I think I've come up with a no-nonsense set of fold settings.

My philosophy for a `.vimrc` has always been a self-contained single file that doesn't depend on plugins. This way, I can copy and paste it into a new environment and be comfortable with light to heavy editing.

Here are my new fold settings in its full glory:

```vim
set foldmethod=expr
set foldlevelstart=99
set foldexpr=FoldFunc(v:lnum)
set foldtext=getline(v:foldstart)
hi Folded ctermbg=0 ctermfg=96
nnoremap zc :%foldc<cr>
nnoremap zv zR
nnoremap zx za

function! FoldFunc(lnum)
    if getline(a:lnum) =~? '\v^\s*$'
        return '-1'
    elseif getline(a:lnum) =~? '\v^\s*(def|class)\s'
        return '>' . (indent(a:lnum) / &shiftwidth + 1)
    else
        return '='
    endif
endfunction
```

I've limited myself to 3 patterns: close everything, open everything, and toggle current function / class. This will make it easier to add to my muscle memory and covers my main use case for folds which is "hide that huge function, because I don't need to see it right now."