---
layout: post
title: Set Timezone by each user
description: Small gist snippet for working with timezone base on user, team, group, company, tenency, etc...
img: set-timezone-by-each-user.jpg
tags: [Ruby On Rails, Gist, Tip]
---

This is a gist to help some developers. It uses to set the timezone for each user, company or tenancy, etc...

In around each request we set timezone base on that scope. It is matched for both cases read and write time into the database.

Normally, when we set the timezone

```ruby
Time.zone = 'London'
```

To see all available timezone, we run command in the terminal

```bash
rails time:zones:all
```

Assume that we have a column time_zone in users table for store our user timezone. In the application controller, we set the timezone around the action. So for each request, there is a timezone base on the user.


```ruby
class ApplicationController < ActionController::Base
  around_action :set_time_zone, if: :user_signed_in?

  private

  def set_time_zone(&block)
    Time.use_zone(current_user.time_zone, &block)
  end

end
```

**Note:** This case can use in all scenarios such as base on user, company, team, group, scope, tenancy, etc...
