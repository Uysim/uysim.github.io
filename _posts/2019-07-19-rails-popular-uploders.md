---
layout: post
title: Ruby On Rails Popular Uploaders
description: This post going to show you 3 popular uploader gems of Ruby On Rails.
img: rails-popular-uploders.jpg
tags: [Ruby On Rails, Gem]
---

It is common for web application that need to upload images, documents or other files. Implement your own uploader or pick the wrong libraries will hurt yourself in the future scale. So that why in this blog post in introduce 3 popular uploader gem.

You can found the full source code at [https://github.com/Uysim/rails-uploaders](https://github.com/Uysim/rails-uploaders).


# 1. Carrierwave

Carrierwave is a simple and extremely flexible. it is well documented and developer friendly. It also have a big community which you can find help on the internet.

Add to your Gemfile

```ruby
gem 'carrierwave'
```

Generate uploader by run command

```bash
rails generate uploader Image
```

Now you got a file under ```app/uploaders/avatar_uploader.rb```

```ruby
class ImageUploader < CarrierWave::Uploader::Base
  storage :file
end
```

Now you can mound uploader at model

```ruby
class CarrierwavePost < ActiveRecord::Base
  mount_uploader :image, ImageUploader
end
```

Working with form
{% highlight erb %}
<%= form_for @post do |f| %>
  <p>
    <label>Image</label>
    <%= f.file_field :image %>
  </p>
<% end %>
{% endhighlight %}

To get file url

```ruby
post.image_url
```

All you file will be stored under folder ```/public/uploads```

# 2. Active Storage
Active Storage come default with Rails version greater than 5 which make your project light weight and less dependencies. It is easy to use and integrate. But it is still too young from my point of view.

To Install it you must run install command on terminal
```bash
rails active_storage:install
```

Then migrate database with command
```bash
rails db:migrate
```
After migrate we will get two table in our database

Attached it to model with code
```ruby
class ActiveStoragePost < ApplicationRecord
  has_one_attached :image
end
```

Working with form
{% highlight erb %}
<%= form_for @post do |f| %>
  <p>
    <label>Image</label>
    <%= f.file_field :image %>
  </p>
<% end %>
{% endhighlight %}

Get file url by
```ruby
url_for(user.avatar)
```

All you file will be encoded and store under folder `/storage`

# 3. Shrine

Shrine is an advance uploader. You can perform a lot of complex task with this uploader. This is the gem I used the most. It is well documented but seem so confuse sometime as it has a lot of features. If you pick this gem make sure you are stick to the document.

Add to your gem file
```ruby
gem "shrine"
```

Create a file at `config/initializers/shrine.rb`
```ruby
require "shrine"
require "shrine/storage/file_system"

Shrine.storages = {
  cache: Shrine::Storage::FileSystem.new("public", prefix: "uploads/cache"), # temporary
  store: Shrine::Storage::FileSystem.new("public", prefix: "uploads"),       # permanent
}

Shrine.plugin :activerecord
```

At `app/uploaders/image_uploader.rb`
```ruby
class ImageUploader < Shrine
end
```

Use it in the model with
```ruby
class ShrinePost < ApplicationRecord
  # you must have a column name image_data with data type text
  include ImageUploader::Attachment.new(:image)
end
```

Use it at form
{% highlight erb %}
<%= form_for @post do |f| %>
  <p>
    <label>Image</label>
    <%= f.file_field :image %>
  </p>
<% end %>
{% endhighlight %}
Get file url
```ruby
post.image_url
```
All you file will be stored under folder `/public/uploads`

# Final Notice
This post is not a comparison pick it at your own choice. I just introduce the way we should use it.
