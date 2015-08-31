#Changes to timestamp in migration for Rails 5
Now we have to add a default option to timestamps, when using migration.

```ruby
class CreateProducts < ActiveRecord::Migration
  def change
    create_table :products do |t|
      t.timestamps null: true
    end
  end
end
```

With the `null:true` or `null:false`, it will show the following warning

```ruby
`timestamp` was called without specifying an option for `null`. 
In Rails 5, this behavior will change to `null: false`. 
You should manually specify `null:true` to prevent the behavior of your existing migrations from changing.
```

`null` options:

`null:true` means you can give a NULL values for `create_at` and `updated_at` in Rails 5

`null:false` means you cannot give NULL values for `create_at` and `updated_at` in Rails 5
