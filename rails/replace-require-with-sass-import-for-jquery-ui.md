# Replace Require With Sass Import for jQuery-UI

In order to load all of the jQuery UI css files, we need

```ruby
/*
 *= require jquery-ui
 */

@import 'jquery-ui'; # DOES NOT WORK!!!
```

`@import 'jquery-ui';` does not work because SASS import will look for a file called `jquery-ui.scss`.

To work around this, we can simply change it to:

```ruby
@import 'jquery-ui.css';
```

Now SASS `@import` will import the css file.
