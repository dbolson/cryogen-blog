{:title "Working with Sass and web fonts"
:layout :post
:tags ["sass"]}

We found a few issues with web fonts and the Bootstrap sprites with the above solution. It turns
out we need to explicitly include the sprites:

```
@import "twitter/bootstrap/bootstrap"; # unchanged
@import "twitter/bootstrap/sprites"; # added
```

As for the web fonts, we were importing from Google like this:

```
@import url(http://fonts.googleapis.com/css?family=Open+Sans:400italic,700italic,400,700);
```

This file looked like this:

```scss
@font-face {
  font-family: 'Open Sans';
  font-style: normal;
  font-weight: 400;
  src: local('Open Sans'), local('OpenSans'), url(http://themes.googleusercontent.com/static/fonts/opensans/v6/cJZKeOuBrn4kERxqtaUH3bO3LdcAZYWl9Si6vvxL-qU.woff) format('woff');
}

@font-face {
  font-family: 'Open Sans';
  font-style: normal;
  font-weight: 700;
  src: local('Open Sans Bold'), local('OpenSans-Bold'), url(http://themes.googleusercontent.com/static/fonts/opensans/v6/k3k702ZOKiLJc3WVjuplzKRDOzjiPcYnFooOUGCOsRk.woff) format('woff');
}
@font-face {
  font-family: 'Open Sans';
  font-style: italic;
  font-weight: 400;
  src: local('Open Sans Italic'), local('OpenSans-Italic'), url(http://themes.googleusercontent.com/static/fonts/opensans/v6/xjAJXh38I15wypJXxuGMBrrIa-7acMAeDBVuclsi6Gc.woff) format('woff');
}
@font-face {
  font-family: 'Open Sans';
  font-style: italic;
  font-weight: 700;
  src: local('Open Sans Bold Italic'), local('OpenSans-BoldItalic'), url(http://themes.googleusercontent.com/static/fonts/opensans/v6/PRmiXeptR36kaC0GEAetxhbnBKKEOwRKgsHDreGcocg.woff) format('woff');
```

Which stopped working once we precompiled the assets. By adding

```
src: font-url('OpenSans-Regular.ttf');
```

for each @font-face declaration, it found the fonts and everything worked.
