# Using Whenever gem on Mina Deployment

Whenever gem is a popular gem to generate cron jobs easily on the server.

Using mina gem to deploy our app, we can easily add the whenever command into the deployment script of mina
so that on every deployment, mina will automatically run whenever to generate the whenever cron jobs.

```ruby
# Whenever tasks in deploy.rb
desc "Clear scheduler"
task :clear_scheduler => :environment do
  queue "bundle exec whenever --clear-crontab gooroo-app-cronjobs --set 'path=#{deploy_to}/current/'"
  queue  %[echo "-----> Clear Scheduler Completed."]
end

desc "Invoke scheduler"
task :exec_scheduler => :environment do
  queue "bundle exec whenever --update-crontab gooroo-app-cronjobs --set 'path=#{deploy_to}/current/'"
  queue  %[echo "-----> Add Scheduler Completed."]
end
```

There is big caveat when we add the mina task in the deploy script.  
Whenever will generate many cron jobs each time we deploy.  
This is because whenever does not recognize the previous crob jobs due to the changing release paths in mina.  
Therefore, we need to specify the _identifier_ in the command line eg. `gooroo-app-cronjobs`

Next, we just need to add the whenever tasks in the deploy block as below:

```ruby
desc "Deploys the current version to the server."
task :deploy => :environment do
  deploy do
    invoke :'git:clone'
    invoke :'deploy:link_shared_paths'
    invoke :'bundle:install'
    invoke :'rails:db_migrate'
    invoke :'rails:assets_precompile'
    
    invoke :clear_scheduler
    invoke :exec_scheduler

    to :launch do
      queue "mkdir -p #{deploy_to}/#{current_path}/tmp"
      queue "touch #{deploy_to}/#{current_path}/tmp/restart.txt"
    end
  end
end
```
