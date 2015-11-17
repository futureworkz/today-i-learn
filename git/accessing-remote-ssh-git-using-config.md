# Accessing Remote SSH Git using Config

If we have multiple remotes git repositories with different SSH access, we can easily set them in our `~/.ssh/config`.

Given an ssh file `~/Workspace/Gooroo/.ssh/gooroo.pem`, we can set the SSH file to be used whenever we push to the production server git by adding the following lines in `~/.ssh/config`:

```shell
Host gooroo.sg
  IdentityFile ~/Workspace/Gooroo/.ssh/gooroosg.pem
```

The above configuration assumes you have a git repository at `gooroo.sg` eg. `git remote add production ssh://gituser@gooroo.sg/gooroo.git`

With the above setup, you can now simply `git push production master`!
