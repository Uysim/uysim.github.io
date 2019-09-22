---
layout: post
title: "Rails Automate Deployment With Capistrano"
description: "Guide to rollout your Rails app to production with automate deployment"
img: rails-automate-deployment-with-capistrano.jpg
tags: [Ruby On Rails, DevOps]
---

Now most of the projects hosted with [Kubenetes](https://kubernetes.io/), even so I still recommend the automate deployment with Capistrano for small or young start up in state of testing, fresh, or with low budget. Which can rollout your Rails App to production easily with less budget. that is why I start to craft this guide.

<!-- ad -->

What you may need
- Linux Server (Remmend Ubuntu)
- [Rails 5+ with RVM](http://uysim.com/install-ruby-on-rails-with-rvm/)
- Nginx
- NodeJS
- Yarn
- Add Your SSH to the server
- Add Your server SSH to git deploy key
- Create your production database
- Secret Key Base in ENV

---
Add To ***Gemfile***
{% highlight ruby %}
group :development do
    gem 'capistrano', require: false
    gem 'capistrano-rvm', require: false
    gem 'capistrano-rails', require: false
    gem 'capistrano-bundler', require: false
    gem 'capistrano3-puma', require: false
end
{% endhighlight %}

Then run commands
```
bundle install
bundle exec cap install
```
---
Add To ***Capfile***.<br>
<small>Capfile located at the root folder of rails project</small>

{% highlight ruby %}
# Setup and deploy may already have in your Capfile
require 'capistrano/setup'
require 'capistrano/deploy'

require 'capistrano/scm/git'

require 'capistrano/rails'
require 'capistrano/bundler'
require 'capistrano/rvm'
require 'capistrano/puma'

install_plugin Capistrano::Puma
install_plugin Capistrano::SCM::Git
install_plugin Capistrano::Puma::Nginx

Dir.glob("lib/capistrano/tasks/*.rake").each { |r| import r } # this may already included in your Capfile
{% endhighlight %}
---
Add To Deploy Production
Production deploy file located at ```config/deploy/production.rb```

{% highlight ruby %}
# change server host to server ip or domain
# change deploy-user to server user that use for deployment
server 'server-host', user: 'deploy-user', roles: [:web, :app, :db], primary: true
set :stage, :production
{% endhighlight %}

---
Add To deploy file
Deploy file located at ```config/deploy.rb```
{% highlight ruby %}
set :repo_url, 'git@your-repo-url.com' # change this to your ssh git url
set :application, 'yourappname' # change this your app name
set :puma_threads, [4, 16]
set :puma_workers, 0
ask :branch, proc { `git rev-parse --abbrev-ref HEAD`.chomp }

# Don't change these unless you know what you're doing
set :pty, true
set :use_sudo, false
set :deploy_via, :remote_cache
set :deploy_to, "/var/www/#{fetch(:application)}"
set :puma_bind, "unix://#{shared_path}/tmp/sockets/#{fetch(:application)}-puma.sock"
set :puma_state, "#{shared_path}/tmp/pids/puma.state"
set :puma_pid, "#{shared_path}/tmp/pids/puma.pid"
set :puma_access_log, "#{release_path}/log/puma.error.log"
set :puma_error_log, "#{release_path}/log/puma.access.log"
set :ssh_options, { forward_agent: true, keys: %w(~/.ssh/id_rsa) }
set :puma_preload_app, true
set :puma_worker_timeout, nil
set :puma_init_active_record, false # Change to true if using ActiveRecord

## Linked Files & Directories (Default None):
set :linked_files, %w{config/master.key} #files that will remove for each deployment
set :linked_dirs, %w{log tmp/pids tmp/cache tmp/sockets vendor/bundle}#folder that will remove for each deployment

namespace :puma do
     desc 'Create Directories for Puma Pids and Socket'
     task :make_dirs do
         on roles(:app) do
             execute "mkdir #{shared_path}/tmp/sockets -p"
             execute "mkdir #{shared_path}/tmp/pids -p"
         end
     end

     before :start, :make_dirs
end

namespace :deploy do
    desc "Make sure local git is in sync with remote."
    task :check_revision do
        on roles(:app) do
            unless `git rev-parse HEAD` == `git rev-parse origin/master`
                puts "WARNING: HEAD is not the same as origin/master"
                puts "Run `git push` to sync changes."
            end
        end
    end

    desc 'Initial Deploy'
    task :initial do
        on roles(:app) do
            before 'deploy:restart', 'puma:start'
            invoke 'deploy'
        end
    end

    desc 'Restart application'
    task :restart do
        on roles(:app), in: :sequence, wait: 5 do
            invoke 'puma:restart'
        end
    end

    before :starting, :check_revision
    after :finishing, :compile_assets
    after :finishing, :cleanup
    after :finishing, :restart
end
{% endhighlight %}
---

Test Your Deployment
Now we make sure that our configuration is valid before we start deploy by run command:
```
cap production deploy:check
```
---
Start Deploy
We will do 3 steps
1. Upload puma config file
2. Upload nginx config file
3. Let capistrano deploy our code

```
cap staging puma:config
cap staging puma:nginx_config
cap production deploy
```
---
Then Restart Nginx

Enjoy your deployment! (:


{% include rails_advices.html %}
