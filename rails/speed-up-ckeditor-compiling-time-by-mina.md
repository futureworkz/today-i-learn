# Speed up ckedit compiling time by mina

### Step 1: Add tmp to shared_paths
```ruby
set :shared_paths, ['config/database.yml', 'log', 'tmp']
```

### Step 2: Create tmp directory in the setup task.
```ruby
task :setup => :environment do
  ...
  queue! %[mkdir -p "#{deploy_to}/shared/tmp"]
  queue! %[chmod g+rx,u+rwx "#{deploy_to}/shared/tmp"]
  ...
end
```
