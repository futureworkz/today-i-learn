# One Liner Command to Create Gemset and Ruby Version for RVM

If you have a new project called projectname using ruby 2.3.0,
this is a quick one liner command you can execute to create
your `.ruby-gemset` and `.ruby-version`:

```shell
$ cd ~/Workspace/projectname
$ rvm use ruby-2.3.0@projectname --ruby-version --create
```
