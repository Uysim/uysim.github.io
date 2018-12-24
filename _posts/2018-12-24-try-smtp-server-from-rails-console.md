---
layout: post
title: "Try SMTP Server From Rails Console"
description: "Tips to test if SMTP working fine before release"
img: try-smtp-server-from-rails-console.jpg
tags: [Rails, Tips]
---

When working with a none technical person, it is really difficult to identify the problem of SMTP config. It hard to define because the credentials or our config is wrong. Also, we don't want to config and release it then user spot it is not working. So I create a snippet that can test the SMTP from rails console before you release it.

Start your rails console by type ```rails c``` on your terminal.

Config your Mailer.

{% highlight ruby %}
ActionMailer::Base.smtp_settings = {
  address: 'your.host.domain',
  port: 465,
  authentication: 'plain',
  enable_starttls_auto: true,
  user_name: 'smtp_user',
  password: 'smtp_password'
}
{% endhighlight %}


Sending test email

{% highlight ruby %}
ActionMailer::Base.mail(
  from: "your_sender@email.com",
  to: "your_reciever@email.com",
  subject: "Test",
  body: "Test"
).deliver_now
{% endhighlight %}

If you received the test email it means everything is fine.


{% include rails_advices.html %}

