# Find and Replace Across Project with Vim agrdo

Currently Vim does not have a method for us to find and replace all in the quick list.

However we can still use the following to get the same effect:

At Vim editor

```vim
:args app/assets/javascript/**/*.js
```

This will tell the store all the file with .js in the javascript folder in the arguments list.

Follow by the below to search and replace

```vim
:argdo %s/find-key/replace-key/g | update
```

This will find all the 'find-key' in the arguments list and replace it with the 'replace-key' accordingly.

The `g` option specifies that all matches for search will return instead of just one per line.

The `update` option specifies that after it change the values it will save the file.
