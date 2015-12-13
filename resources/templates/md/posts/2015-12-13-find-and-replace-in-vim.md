{:title "Find and Replace in Vim"
:layout :post
:tags ["productivity" "vim"]}

Find and replace in Vim has been something I've always struggled with, but here, at last, is the solution.

**[How I Find and Replace in Vim][1]**

This requires [ack][2].

```
:args `ack -l '\b{text to replace}\b' --ignore-dir=compiled --ignore-file=is:tags`
:argdo %s/\<{text to replace}\>/{replacing text}/gc | update
```

[1]: http://jezenthomas.com/how-i-find-and-replace-in-vim
[2]: http://beyondgrep.com/
