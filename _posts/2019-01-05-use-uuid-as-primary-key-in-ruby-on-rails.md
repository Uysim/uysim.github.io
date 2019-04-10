---
layout: post
title: Use UUID as primary key in Rails On Rails
description: Tutorial to config UUID as primary key and foreign key for high scale Ruby On Rails app
img: use-uuid-as-primary-key-in-ruby-on-rails.png
tags: [Ruby On Rails, Postgresql, psql, Database, Tutorial, Scale]
last_modified_at: 2019-04-10
---

Recently with expected high scale projects, I started it by use UUID as the primary and foreign key of the tables. The result comes with a perfect high scale result. This methodology comes with [bad](#bad-side-off-it) and [good](#why-should-we-use-uuid) effect. Even so, I recommend initing project with something like this if want to have easy and flexible scaling to your app in the future. Again, I really recommend this if you think of ↗️↗️↗️

> ***Note***: Tutorial below use specific between Ruby On Rails and Postgresql

### Why should we use UUID?

* ***Moving Data***: UUID mean in this world your id is unique. That means it is easier to merge, split, or move data across table or event database
* ***Multi location***: When you million of transaction that you can not handle in one single database. So you need your database to have a master and the child nodes or place in multi-location, this will be handled when the database is synced because the primary is already unique across the database.
* ***Talk to different app***: When we have multiple apps that need to communicate with each other. Our records will have a unique identity
* ***Security***: No one can guess your previous or next record by decrease or increase the number because record id now is a random string
* ***Able to generate your own id***: Without sequence, you able create your own id with any string
* ***No waiting insert***: When you multiple insert at the same time you record don't need to wait for next sequence. It will go directly into your database.


### Bad side of it
* ***Performance***: Imagine that the primary key with a number of `[1, 2, 3]` now is changed to the stupid crazy string like `831a4357-3ac3-47bf-998e-4e4fae8d9150` it effects much on query performance
* ***Ugly***: UUID is a random string. Which it won't look right if you include in URL or some user interactive

First what we need is enable ```pgcrypto``` plugin to Postgresql. this required as we to use gen_random_uuid to generate UUID primary when record is created.

We generate migration by run command

{% highlight bash %}
rails generate migration enable_pgcrypto
{% endhighlight %}

In your migration file

{% highlight ruby %}
class EnablePgcryptoExtension < ActiveRecord::Migration[5.2]
  def change
    enable_extension 'pgcrypto'
  end
end
{% endhighlight %}

<br>

Add config to ```config/application.rb```

{% highlight ruby %}
...
config.generators do |g|
  g.orm :active_record, primary_key_type: :uuid, foreign_key_type: :uuid
end
...
{% endhighlight %}

Now you able to use primary as UUID in your app. 👏👏👏


<br>

***When create new model***

Ruby on Rails Command to create new model

{% highlight bash %}
rails generate model product title:string
{% endhighlight %}


Your migration file will look like

{% highlight ruby %}
class CreateProducts < ActiveRecord::Migration[5.2]
  def change
    create_table :products, id: :uuid do |t|
      t.string :title
      t.timestamps
    end
  end
end
{% endhighlight %}

<br>

***Handle References***

When you add your foreign key it, your migration file should look like

{% highlight ruby %}
add_references :item, :user_id, :uuid
{% endhighlight %}

{% include rails_advices.html %}
