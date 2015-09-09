# CKEditor image upload with paperclip

After working on a few projects, I realise most project just use `ckeditor` gem and just change their respective backend gem like `rails_admin` or `activeadmin` to use `ckeditor` for their text area.

However, when we enable `ckeditor` view, this also mean that admin will be able to upload images accordingly, which this will cause a routing errors if the admin try to upload one.

Assuming all the configuration is done for paperclip.

We just need to run the following CLI:

```
$ rails generate ckeditor:install --orm=active_record --backend=paperclip
```

This will generate the following necessary items
```
create  config/initializers/ckeditor.rb
route  mount Ckeditor::Engine => '/ckeditor'
create  app/models/ckeditor/asset.rb
create  app/models/ckeditor/picture.rb
create  app/models/ckeditor/attachment_file.rb
create  db/migrate/20140204111641_create_ckeditor_assets.rb
```

It actually create an migration file for us, so lets run it

```
$ rake db:migrate
```

That all, this will allow the admin to be able to upload image via the ckeditor accordingly.

All information is available in the [Github](https://github.com/galetahub/ckeditor)
