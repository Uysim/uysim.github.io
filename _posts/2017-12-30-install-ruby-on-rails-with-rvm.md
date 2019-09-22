---
layout: post
title: "Install Ruby On Rails With RVM"
description: "Installation tutorial for fresh start Ruby On Rails"
img: install-ruby-on-rails-with-rvm.jpg
tags: [Ruby On Rails, Ruby, RVM, Beginner]
---


This is just a simple tutorial for fresh start Ruby On Rails developer to install it. For Ubuntu and MacOS.

<!-- ad -->


### Install RVM
```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
curl -sSL https://get.rvm.io | bash -s stab
```

See installed ruby version
```
rvm -v
```
<br>


### Install Lastest Ruby
```
rvm install ruby --latest
rvm use ruby --lastest
```

See installed ruby version
```
ruby -v
```
<br>


### Install JS Runtime

it is optional. Need to rake assets:precompile
```
curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt-get install nodejs
```

See installed node version
```
nodejs -v
```
<br>


### Install Rails
```
gem install rails
```

See installed rails version
```
rails -v
```
<br>
Now you can start with this great framework.

{% include rails_advices.html %}
