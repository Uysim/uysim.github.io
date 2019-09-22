---
layout: post
title: Build and run your first Ruby On Rails Docker
description: Guide for starter to build and run Ruby On Rails with Docker
img: build-and-run-your-first-ruby-on-rails-docker.jpg
tags: [Ruby On Rails, Docker, Gist, Tip, Sample]
---

In the recent year, the Ruby On Rails deployment has been shift from tranditional linux server to dockerization. Even myself has stop deploy Rails to production with linux server anymore. So in this blog I would like to help some starter to dockerize their Rails app with these simple step below.

Requirements

* Installed docker
* [Install Ruby On Rails](https://uysim.com/install-ruby-on-rails-with-rvm/)

Create your ROR app

```bash
rails new SampleRailsDocker --database=postgresql
```

Your `config/database.yml` should look like

```yml
default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
development:
  <<: *default
  database: dev_db_name
production:
  <<: *default
  database: <%= ENV['DATABASE_NAME'] %>
  username: <%= ENV['DATABASE_USER'] %>
  password: <%= ENV['DATABASE_PASSWORD'] %>
  password: <%= ENV['DATABASE_HOST'] %>
```

We will use Ruby alpine to create our docker image. So `Dockerfile` should look like
```
FROM ruby:2.5.3-alpine
RUN apk add --no-cache --update build-base \
                                linux-headers \
                                git \
                                postgresql-dev \
                                nodejs \
                                tzdata
RUN mkdir /myapp
WORKDIR /myapp
COPY Gemfile /myapp/Gemfile
COPY Gemfile.lock /myapp/Gemfile.lock
RUN bundle install
COPY . /myapp
EXPOSE 3000
```

We use docker compose to help run our docker image. In `docker-compose.yml` we have two services. One is database (postgres) and web is our Rails app.
```yml
version: '3'
services:
  db:
    image: postgres:11.1-alpine
    environment:
      - POSTGRES_PASSWORD=password
      - POSTGRES_USER=db_user
      - POSTGRES_DB=rails_dev
web:
    build: .
    image: uysimty/rails-sample
    command: bin/rails s -p 3000 -b '0.0.0.0'
    environment:
      - DATABASE_PASSWORD=password
      - DATABASE_USER=db_user
      - DATABASE_NAME=rails_dev
      - DATABASE_HOST=db
    ports:
      - "3000:3000"
    depends_on:
      - db
```

Build docker image run command
```bash
docker-compose build
```

Push docker image to cloud
```bash
docker-compose push web
```

Run migration
```bash
docker-compose run --rm web rails db:migrate
```


Our fresh new rails app didn't have any migration yet but this is useful if we have the migration pending

Run locally
```bash
docker-compose up
```

Now on browser go to http://localhost:3000 you will see your Ruby On Rails welcome page.




