---
layout: post
title: "Rails Scope VS Class Method"
description: "When you should use scope or class method"
img: scope-vs-class-method.jpeg
tags: [Ruby On Rails, Ruby, Comparation]
---
In rails to handle complex query, developers always chain query with scope and class methods. There are many online articles talk about fighting between scope and class method. Those articles talk about when you use them. For my personal experience I really like chain the scope and class method to handle complex query by split their own responsibility for each scope and class method.

<!-- ad -->

### What is scope?

Scope is a technique used in activerecord for query purpose to return activerecord relation.

{% highlight ruby %}
class User
  scope :sellers, -> { where(role: 'seller') }
end
{% endhighlight %}

### What is class method?

Class method is a method that define in the class that can call my class name.

{% highlight ruby %}
class User
  def self.name_like(name)
    where('name ilike ?', "%#{name}%")
  end
end
{% endhighlight %}

### When to use it?

**Scope**

We scope for simple query. Mostly it is really useful we split complex query by simple query and put it into scope.
Example:
{% highlight ruby %}
class Product
  scope :expensive, -> { where('price > ?', 50) }
  scope :available, -> { where('quantity > ?', 0) }
end
{% endhighlight %}

By this example when we want to find product expensive that available in stock we can chain multiple scope. This help a lot in complex selection which split each scope to it own responsible. Example: Product.expensive.available


Scope can also pass in argument too. But we see many article online they move to use class method instead. For my experience I think it is ok to have to parameters for scope.
Example:

{% highlight ruby %}
class Product
  scope :name_like, ->(name) { where('name ilike ?', "%#{name}%") }
end
{% endhighlight %}

You can see above that just pass in the parameter into the scope is not cause your code to look ugly at all.


**Class method**

We use class method in many purpose but in this article I will focus on the way of using class method as a scope. We just it in query purpose. So if one class method use for handle a query, when we know we must use scope or class method? According to my reference above scope can use for passing params in, so how we gonna use class method for query?


We use query class method when it need an algorithm. Example we want to get product that created for one month from a specific date
Example:
{% highlight ruby %}
class Product
  def self.one_month_before(date)
    one_month_before = date.last_month
    where('Date(created_at) BETWEEN Date(?) AND Date(?)',date, one_month_before)
  end
end
{% endhighlight %}

See the method above would be look bad if we handle it by scope. Imagine when need multiple lines of code then it would be so bad to handle by scope.

**Conclusion**

We use scope to handle database query (selection) but we must switch to use class method when we need algorithm before get into direct query.

{% include rails_advices.html %}
