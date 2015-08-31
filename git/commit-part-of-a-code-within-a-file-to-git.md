# Commit part of a code within a file to git

You can do a `git add --patch [filename]` or `git add -p [filename]`

After which you will be propmt by the following

`Stage this hunk [y, n, q, a, d, /, j, J, g, s, e, ?]`

Most commonly use options:

`y` stage this hunk for the next commit.

`n` do not stage this hunk the next commit.

`s` split the current hunk into smaller hunks.

`e` manually edit the current hunk.

For more information, please visit [Stack Overflow](http://stackoverflow.com/questions/1085162/commit-only-part-of-a-file-in-git) or official site [Git](https://git-scm.com/book/en/v2/Git-Tools-Interactive-Staging)
