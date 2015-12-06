{:title "Vimprovements"
:layout :post
:tags ["tools" "vim"]}

After the [improvements][1] I made to my Vim setup, I realized I could get that setup in a
repeatable state without too many problems. Here's what I did.

The first decision was to not use [Janus][2]. I enjoyed my time with it, but I wanted to have full
control over Vim and have a reason for each new piece of functionality. I went through the
`.vimrc` and `.gvimrc` files and the list of plugins and took only the ones I used for day-to-day
development. If I have a pain point and want more functionality, I'll find a solution organically,
but I didn't want to have a kitchen sink setup where I didn't know if the functionality is Vim's
or Janus'.

Don't feel that this means I dislike Janus, as I think it's a great environment, especially for
those new to Vim, but it's time to move on.

I then installed [pathogen][3] for easy plugin management. I used submodules to install all the
[plugins][4] I wanted so I'm able to easily update them through git. The two non-trivial
installations were Command-T and javaScriptLint. I included instructions in the [README][5] for
getting those installed. For the latter plugin, it's a nice thing to add the following line to
your `.vimrc` file to get rid of the rather strong highlighting color:

```
let g:jslint_highlight_color=""
```

To get the `.vimrc` and `.gvimrc` files in the same folder, I renamed them without the dots and
created symlinks with the original names in the root directory that pointed to them. Now
everything was under version control and can go on whatever machine I want.

You can find my setup on [GitHub][6]. Feel free to take what you like.

[1]: /blog/2011/12/22/can-we-have-too-many-tools/
[2]: https://github.com/carlhuda/janus
[3]: https://github.com/tpope/vim-pathogen
[4]: https://github.com/dbolson/dotvim/tree/master/bundle
[5]: https://github.com/dbolson/dotvim/blob/master/README
[6]: https://github.com/dbolson/dotvim/
