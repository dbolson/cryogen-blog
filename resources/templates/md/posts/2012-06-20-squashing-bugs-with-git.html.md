{:title "Squashing bugs with Git"
:layout :post
:tags ["tools"]}

We found a bug at work where a CSS style changed, but it wasn't immediately obvious where it had
been introduced. To fix this bug, I went back through the git history and checked out one in the
recent past to see if the bug had been introduced there. This only took a few tries, and then I
worked back toward the present to find when the styling had been modified.

I found the offending commit by checking out the sass file of that particular commit:

```
git checkout {commit_number} path/to/stylesheet.sass
```

Now I've got the most recent code plus the file that fixes the problem. To see what had been
changed, I did a diff on the two files: the older one without the bug and the next one with the
bug:

```
git diff {older_commit} {newer_commit} path/to/stylesheet.sass
```

There are a lot of changes, so I looked for anything that would be obvious (margins in this case).
After that, it was modifying the styling to fix the bug and done.

Git plays nice.
