# See all changes to a file

Given a file eg `app/models/user.rb`, you can easily see all the commits made in this file:

```shell
$ git log app/models/user.rb
```

If you want to who wrote which line of code in the current file, you can:

```shell
$ git blame app/models/user.rb
```

If you want to see all the lines that are written in the file (including deleted and modified lines), you can:

```shell
$ git log -L ,:app/models/user.rb
```

Now you can really find out who write what code when. :)
