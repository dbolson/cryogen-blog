{:title "Putting Sass files in a global namespace"
:layout :post
:tags ["sass"]}

Rails makes it easy to package our CSS files using the asset pipeline – just put all the `.scss`
files in the `application.css` manifest file or set `require_tree .` to automatically pull in all
the appropriate files.

### If only it were that easy...

Using the asset pipeline keeps each `.scss` file in its own namespace, so you will need to import
any files that have variables we want to use in the current file.

```scss
# layouts.scss
@import "variables";

#my-selector {
  background-color: @background-color; # imported from layouts.scss
}
```

`layouts.scss` doesn't know about the contents of `variables.scss`, so it can't access
`@background-color` that's set within `variables.scss`. Now we need to import that file that's
probably included in the manifest file, so we're going to have two declarations of the contents of
`variables.scss` and the dependency of importing it within any file that needs its content.

[twitter-bootstrap-rails][1] generates a `bootstrap_and_overrides.css.scss` file that imports the
Bootstrap files, so we can use the variables and mixins from Bootstrap in that file, but it won't
work for any other files we have. It would be much nicer to organize our `.scss` files
semantically, eg.,

```
forms.scss
layout.scss
header.scss
footer.scss
notification.scss
# ... etc.
```

Each of these files will probably want to have access to Bootstrap variables and site-wide
variables, but we will need to import everything we want to use.

### A DRYer way

h/t to [Pivotal Labs][2] for the implementation and the [link][3] to the docs:

> If you want to use multiple Sass files, you should generally use the Sass @import rule instead
of these Sprockets directives. Using Sprockets directives all Sass files exist within their own
scope, making variables or mixins only available within the document they were defined in.

Here's what the results look like:

```javascript
# application.scss
// *= require bootstrap-datepicker

@import "twitter/bootstrap/bootstrap";
@import "twitter/bootstrap/sprites";

// Site files
@import "custom-variables";
@import "custom-other";

@import "forms";
@import "header";
@import "footer";
@import "content";
# ... etc.
```

A few notes about this:

* We renamed application.css to application.scss. This will allow us to use include a manifest and
  use `.scss` syntax.
* We are still using the manifest for third-party CSS files. Since `bootstrap-datepicker` comes in
  through a [gem][4], we can just reference the CSS and let it do its thing automatically.
* We could load each Bootstrap file individually if we want to reduce its footprint, but loading
  the entire library is easier to illustrate here.

### Results

We can make our code more modular by easily reusing variables and mixins. Maybe we can even work
toward more [semantic naming][5]...

### One caveat

We had to remove the `spec/javascripts/spec.css` file that loaded the manifest file since that is
now `.scss` and not `.css`. This upset [Jasmine][6], and we don't like it when Jasmine is upset.

[1]: https://github.com/seyhunak/twitter-bootstrap-rails
[2]: https://blog.pivotal.io/pivotal-labs/labs/structure-your-sass-files-with-import
[3]: http://guides.rubyonrails.org/asset_pipeline.html
[4]: https://github.com/Nerian/bootstrap-datepicker-rails/
[5]: http://ruby.bvision.com/blog/please-stop-embedding-bootstrap-classes-in-your-html
[6]: http://jasmine.github.io/
