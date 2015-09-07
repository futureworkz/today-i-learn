#### This guide setup Apache2/Passenger/Ruby/Rails on Red Hat Enterprise Linux 7.1 (HVM).

```ruby
# ssh
ssh -i in2ideas.pem ec2-user@52.74.81.212

# switch to root
sudo su -

# install the necessary libraries
yum clean all
yum -y update
yum -y install curl-devel git sqlite-devel libxml2-devel
yum -y install libxml2 libxml2-devel libxslt libxslt-devel
yum -y install gcc-c++ readline-devel libyaml-devel libffi-devel openssl-devel libtool bison
yum groupinstall 'Development Tools'
curl -sL https://rpm.nodesource.com/setup | bash -
yum -y install nodejs
```

#### install RVM
```ruby
curl -L https://get.rvm.io | bash -s stable
gpg2 --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
curl -L https://get.rvm.io | bash -s stable
source /etc/profile.d/rvm.sh
rvm autolibs disable
rvm requirements
```

#### Install Ruby & Rails
```ruby
rvm install 2.2.0
rvm --default use 2.2.0

# Install bundler
gem install bundler

# Install Rails 
gem install rails --version 4.2.0 --no-ri --no-rdoc

```

#### Install Apache
```ruby
yum -y install httpd httpd-devel

# Allow port 80 to server webpage
iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT

# starts the service
chkconfig httpd on
service httpd start

# Install passenger
# There will be more requirements to install
# Just follow the instructions
gem install passenger
passenger-install-apache2-module
# There will be instructions to load your passenger module via http conf
# Put the config in a new file at /etc/httpd/conf.d/passenger.conf, it should be look like:
```

/etc/httpd/conf.d/passenger.conf
```ruby
LoadModule passenger_module /usr/local/rvm/gems/ruby-2.2.0/gems/passenger-5.0.4/buildout/apache2/mod_passenger.so
<IfModule mod_passenger.c>
  PassengerRoot /usr/local/rvm/gems/ruby-2.2.0/gems/passenger-5.0.4
  PassengerDefaultRuby /usr/local/rvm/gems/ruby-2.2.0/wrappers/ruby
</IfModule>

<VirtualHost *:80>
    ServerName www.in2ideas.com
    # !!! Be sure to point DocumentRoot to 'public'!
    DocumentRoot /home/in2ideas/production/current/public
  <Directory /home/in2ideas/production/current/public>
    # This relaxes Apache security settings.
    AllowOverride all
    # MultiViews must be turned off.
    Options -MultiViews
    # Uncomment this if you're on Apache >= 2.4:
    Require all granted
  </Directory>
</VirtualHost>
```
```ruby
# Restart the service
service httpd restart
```

#### Setup Postgres in Server
```ruby
yum -y install postgresql-server postgresql-contrib postgresql-devel

service postgresql initdb
chkconfig postgresql on
service postgresql restart

# Create the user
sudo -u postgres psql
create user <username> with password '<password>';
create database myapp_production owner <username>;
ALTER ROLE <username> SUPERUSER; # Needed if your app needs to install extensions

# Type '\q'+Enter to exit postgres

# Change PG login
vi /var/lib/pgsql/data/pg_hba.conf
```
```ruby
# Change the bottom lines from Ident to md5
# TYPE  DATABASE    USER        CIDR-ADDRESS          METHOD

# "local" is for Unix domain socket connections only
local   all         all                               md5
# IPv4 local connections:
host    all         all         127.0.0.1/32          md5
# IPv6 local connections:
host    all         all         ::1/128               md5
```
```ruby
# Restart the service
service postgresql restart
```

#### Pre-deployment 
in2ideas is the assumed name of the project in this guide
```ruby
# Setup a user for the access (instead of root)
adduser in2ideas
passwd -l in2ideas
# This locks the password of in2ideas.
# Use public key to access SSH.
# Read section Access SSH via Public Key below

# add t rvm group so that bundling can work for capistrano
usermod -a -G rvm in2ideas

# set directory for the app
deploy_to=/home/in2ideas/production
mkdir -p ${deploy_to}
chown in2ideas:in2ideas ${deploy_to}
umask 0002
chmod g+s ${deploy_to}
mkdir -p ${deploy_to}/{releases,shared}

# Use your user (not root) to do the below:
rvm gemset create <gemset>
rvm gemset use <gemset>
```

#### Setup Git on Server
This setups the git server on the remote server
Developer will push to this folder (~/git/in2ideas.git) and Capistrano will pull from this folder and deploy it
```ruby
# switch to user mode
su in2ideas
mkdir -p ~/git/in2ideas.git
cd ~/git/in2ideas.git
git --bare init
```
Next, we need to setup the SSH for Capistrano to access the Git:
```ruby
test -e ~/.ssh/id_dsa.pub || ssh-keygen -t dsa # Enter 3 times
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys

# Need to do this for CentOS
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys 
restorecon -Rv ~/.ssh

# Accepting host authenticity for user
# Test it (you need to accept the host authenticity):
ssh localhost
# if you did not see any prompt for password, it is done!

# Test: accept host authenticity for git
git ls-remote ssh://user@domain/~/git/in2ideas.git master
```

#### Access SSH via authorized_keys
```ruby
# Your localhost iTerm:
vim ~/.ssh/id_rsa.pub # Copy a key
# ssh iTerm(user mode):
vim ~/.ssh/authorized_keys # Paste to here
```

#### install MINA
```ruby
# GemFile
gem 'mina'
bundle install
mina init
# Created config/deploy.rb.
```
config/deploy.rb
```ruby
require 'mina/bundler'
require 'mina/rails'
require 'mina/git'
# require 'mina/rbenv'  # for rbenv support. (http://rbenv.org)
require 'mina/rvm'    # for rvm support. (http://rvm.io)

# Basic settings:
#   domain       - The hostname to SSH to.
#   deploy_to    - Path to deploy into.
#   repository   - Git repo to clone from. (needed by mina/git)
#   branch       - Branch name to deploy. (needed by mina/git)

# Manually create these paths in shared/ (eg: shared/config/database.yml) in your server.
# They will be linked in the 'deploy:link_shared_paths' step.

set :term_mode, nil
set :shared_paths, ['config/database.yml', 'log']
set :app, 'in2ideas'
set :domain, '52.74.81.212'
set :repository, 'ssh://in2ideas@52.74.81.212/~/git/in2ideas.git'
set :user, 'in2ideas'
set :rvm_path, "/usr/local/rvm/scripts/rvm"



# Optional settings:
#   set :user, 'foobar'    # Username in the server to SSH to.
#   set :port, '30000'     # SSH port number.

# This task is the environment that is loaded for most commands, such as
# `mina deploy` or `mina rake`.

task :environment do
  # If you're using rbenv, use this to load the rbenv environment.
  # Be sure to commit your .rbenv-version to your repository.
  # invoke :'rbenv:load'
  stage = ENV['to'] || 'production'
  case stage
  when 'staging'
    set :branch, 'staging'
  when 'development'
    set :branch, 'development'
  when 'production'
    set :branch, 'master'
  else
    print_error "Please specify a stage. eg. mina deploy to=production"
    exit
  end

  set :rails_env, stage
  set :deploy_to, "/home/#{app}/#{stage}"

  invoke :"rvm:use[ruby-2.2.0@in2ideas]"
  # For those using RVM, use this to load an RVM version@gemset.
  # invoke :'rvm:use[ruby-1.9.3-p125@default]'
end



# Put any custom mkdir's in here for when `mina setup` is ran.
# For Rails apps, we'll make some of the shared paths that are shared between
# all releases.
task :setup => :environment do
  queue! %[mkdir -p "#{deploy_to}/shared/log"]
  queue! %[chmod g+rx,u+rwx "#{deploy_to}/shared/log"]

  queue! %[mkdir -p "#{deploy_to}/shared/config"]
  queue! %[chmod g+rx,u+rwx "#{deploy_to}/shared/config"]

  queue! %[touch "#{deploy_to}/shared/config/database.yml"]
  queue  %[echo "-----> Be sure to edit 'shared/config/database.yml'."]
end

desc "Deploys the current version to the server."
task :deploy => :environment do
  deploy do
    # Put things that will set up an empty directory into a fully set-up
    # instance of your project.
    invoke :'git:clone'
    invoke :'deploy:link_shared_paths'
    invoke :'bundle:install'
    invoke :'rails:db_migrate'
    invoke :'rails:assets_precompile'

    to :launch do
      queue "mkdir -p #{deploy_to}/#{current_path}/tmp"
      queue "touch #{deploy_to}/#{current_path}/tmp/restart.txt"
    end
  end
end

desc "Seed data to the database"
task :seed => :environment do
  queue "cd #{deploy_to}/#{current_path}/"
  queue "bundle exec rake db:seed RAILS_ENV=#{rails_env}"
  queue  %[echo "-----> Rake Seeding Completed."]
end

# For help in making your deploy script, see the Mina documentation:
#
#  - http://nadarei.co/mina
#  - http://nadarei.co/mina/tasks
#  - http://nadarei.co/mina/settings
#  - http://nadarei.co/mina/helpers
```
```ruby
# Set permission(as root)
chown -R in2ideas "/home/in2ideas/production"

# Your localhost iTerm
mina setup

# Setup database.yml(as user mode)
vim ~/production/shared/config/database.yml
```
~/production/shared/config/database.yml
```ruby
production:
  adapter: postgresql
  database: in2ideas_production
  host: localhost
  username: in2ideas
  password: in2ideas
```
```ruby
# your localhost iTerm:
git remote add aws ssh://in2ideas@52.74.23.212/~/git/in2ideas.git
git push aws master

# deploy
mina deploy
```

#### Set Environment
```ruby
# logout and login again:
ssh in2ideas@52.74.81.212

# Set environment
vim ~/.bash_profile
```
~/.bash_profile
```ruby
PATH=$PATH:$HOME/.local/bin:$HOME/bin
SECRET_KEY_BASE="1b6f401fe7511d30017117b4a74111baa6d0d4a9fcb43de86c901ae487975577a32b29d29fa26fcace631b9121ec93d83809ef7edc77169b8a11d6de4f723d4c"
HOST="in2ideas.com"
...
ADMIN_EMAIL="fwzjames@gmail.com"

export PATH SECRET_KEY_BASE HOST ADMIN_EMAIL
```

#### disable SELINUX
```ruby
# as root:
chmod 755 /home/in2ideas/
vim /etc/selinux/config
```
/etc/selinux/config
```ruby
SELINUX=disabled
```
```ruby
reboot
```

