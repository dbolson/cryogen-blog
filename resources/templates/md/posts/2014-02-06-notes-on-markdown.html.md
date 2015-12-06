{:title "Notes on Markdown"
:layout :post
:tags ["markdown" "tools"]}

Here's a quick primer on my [Markdown][1] workflow:

I like to write everything in vim (of course) and save files with the `.md` extension. Then, in my
`.vimrc`, I have these settings:

```
au BufRead,BufNewFile *.md set ft=markdown " set syntax to markdown
au BufRead,BufNewFile *.md setlocal spell " set spelling
au BufRead,BufNewFile *.md setlocal wrap " set line wrapping
```

I use the [Marked app][2] as a preview for the Markdown files. Add this to `.vimrc` to open the
current file in Marked with `leader m`:

```
nnoremap <leader>m :silent !open -a Marked.app '%:p'<cr>
```

This works for personal documents, but it's not perfect for pasting into Gmail. For that I use
[markdown-it.github.io][3]. Simply paste (or write) Markdown in the left and copy from the right.
You can paste this directly into Gmail while preserving the formatting.

Another tool to try is [Mou][4] for writing Markdown (I still prefer vim).

It's to the point where I write everything that's more than a few sentences in Markdown. Even if I
don't convert it to rich text, plain Markdown still looks pretty good.

[1]: https://daringfireball.net/projects/markdown/
[2]: http://marked2app.com/
[3]: http://markdown-it.github.io
[4]: http://mouapp.com/
