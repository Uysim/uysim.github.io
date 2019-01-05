---
layout: post
title: "Rails performance indexing DB column"
description: "Improve your Ruby On Rails queries by index your database columns"
img: rails-performance-indexing-db-column.jpeg
tags: [Ruby On Rails, Database, Performance]
---

With my latest few projects, I practice on how to improve rails performance. With this blog, I would like to introduce a basic practice to improve query performance in rails by index table column.

### What is database indexing?

Indexing is the way that database store data structure by stored and associated with the actual record, which will make records easier and faster to find in the table.

### Why need Indexing?

We need to index a column when we think that column will use the most for a query or complex query in a database. That will decrease the query time and increase query performance for a database. Indexing is really important for performance when records grow. Also while a number of request by to server increase, indexing is the key for  the site to load faster.

### How to index column in rails?

Add below code pattern to your migration file in situation you need

When create new table :

{% highlight ruby %}
def change
  create_table :table_name do |t|
    t.string :column_name, index: true
  end
end
{% endhighlight %}

When add new column to table :

{% highlight ruby %}
def change
  add_column :table, :column_name, index: true
end
{% endhighlight %}

When index the existing column in table:

{% highlight ruby %}
def change
  add_index :table, :column_name
end
{% endhighlight %}

### When to index?

**Index column performance query the most :**

{% highlight ruby %}
class Product < ActiveRecord::Base
  def search(name)
    where('name ilike ?', "%#{name}%")
  end
end
{% endhighlight %}

The method search in class Product above will often use to searching for products. So when product records grow to more than thousands. And many users do some search at the same time. So app performance will start to slow down a bit as some query will queue. Surely this name column needs to index to decrease search and queue time for each query.

**Index reference column :**

{% highlight ruby %}
class Category < ActiveRecord::Base
  has_many :products
end
{% endhighlight %}


{% highlight ruby %}
class Product < ActiveRecord::Base
  belongs_to :category
end
{% endhighlight %}

To get all product belong to category:

{% highlight ruby %}
category.products
{% endhighlight %}

Rails will produce sql query:

{% highlight ruby %}
SELECT "products".* FROM "products" WHERE "products"."category_id" = $1  [["category_id", 1]]
{% endhighlight %}
As category_id in table products will use for to find products that belong to a category. This column needs to index for better performance each time get products of the category.

**Index column that some gem use the most :**

For my late project my team use geocoder gem to find near user by lat long of the user

{% highlight ruby %}
class User < ActiveRecord::Base
  reverse_geocoded_by :lat, :long
end
{% endhighlight %}

To find nearby user in 25 km we have to do:

{% highlight ruby %}
User.near([51.5090970805918, -0.127683585920525], 25, unit: :km)
{% endhighlight %}

Geocoder will produce sql query:

{% highlight ruby %}
SELECT users.*, 3958.755864232 * 2 * ASIN(SQRT(POWER(SIN((51.5090970805918 - users.lat) * PI() / 180 / 2), 2) + COS(51.5090970805918 * PI() / 180) * COS(users.lat * PI() / 180) * POWER(SIN((-0.127683585920525 - users.long) * PI() / 180 / 2), 2))) AS distance, MOD(CAST((ATAN2( ((users.long - -0.127683585920525) / 57.2957795), ((users.lat - 51.5090970805918) / 57.2957795)) * 57.2957795) + 360 AS decimal), 360) AS bearing FROM "users" WHERE (users.lat BETWEEN 51.14726762281468 AND 51.87092653836892 AND users.long BETWEEN -0.7090381098032911 AND 0.4536709379622411 AND (3958.755864232 * 2 * ASIN(SQRT(POWER(SIN((51.5090970805918 - users.lat) * PI() / 180 / 2), 2) + COS(51.5090970805918 * PI() / 180) * COS(users.lat * PI() / 180) * POWER(SIN((-0.127683585920525 - users.long) * PI() / 180 / 2), 2)))) BETWEEN 0.0 AND 25)  ORDER BY distance ASC
{% endhighlight %}
We can see SQL statement above that geocoder use column lat and long for its complex query which will lead this query to be slower when record up to too many to handle. So the better way is to index this lat and long columns that use by this gem.
### Bad of indexing
Index table’s column will increase the size of record because it will copy that record for sorting it.


{% include rails_advices.html %}
