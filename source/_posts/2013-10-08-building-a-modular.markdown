---
layout: post
title: "Building a modular, tested and documented API using Rails"
date: 2013-10-08 20:00
comments: true
categories: [Ruby On Rails, APIs, TDD]
---

### Overview

Nowadays writing API centric web and mobile applications as become a pretty common practice. Most high traffic websites use the services oriented architecture pattern for there back-end. Mobile applications also retrieve contents from web services. APIs are everywhere and tends to become the heart of some web busineses. Therefore, they need to be reliable, fast and easy to use for third party. Security is also a main concerne when building an API.

In that step by step tutorial we'll see how to build a robust API using the leaner rails-api gem. As per best practise we'll write test first and follow test diven development cycle. That is to say, writing test, watching it fail, then implement the tested feature and verify that it works as expected.

A simple enough example is a task manager backend API where each user has it own private list of tasks. I wont cover user sign up to the service, that's not the point of this tutorial.

### Create a new api as a Rails engine

An engine is basically a gem that can be mounted into any Rails appliction, improving reusability and modularity. To generate a new engine run:

`rails plugin new api-engine --full --mountable --dummy-path spec/dummy --skip-test-unit`

The *--full* option basically makes the plugin look like a standard Rails project and *--mountable* specifies that the engine must be isolated within his own namespace. Also we'll setup Rspec for testing later on so we must avoid generating useless test unit files with the *--skip-test-unit* option.

Then installing the rails-api gem is pretty straight forward. Add this line inside your Gemfile:

```ruby
gem 'rails-api'
```

The Gemfile is a list of all dependencies you want to include in the project. It is used with Bundler to install, remove and update your used gems. Run this command to install:

`bundle install` or just `bundle`

With rails-api installed you must then edit the file *app/controllers/application_controller.rb* like so

```ruby
# replace this line
class ApplicationController < ActionController::Base
end

# with
class ApplicationController < ActionController::API
end
```

### Setup testing environment

To add RSpec as a dependency to our new project we need to add this line inside the Gemfile.

```ruby
gem 'rspec-rails', '~> 2.14.0'
```

The Gemfile is a list of all dependencies you want to include in the project. It is used with Bundler to install, remove and update your used gems. Notice that rails 4, rails-api and sqlite3 gems are specified by default in the generated Gemfile so they will be installed along with RSpec. Run this command to install

`bundle install` or just `bundle`

Once installed, we must set up RSpec with this command

`rails g rspec:install`

RSpec will now be used as default test framework when using rails generators.

### Implementing first feature

The first thing we want our API to be able to do is returning user informations. The user should be restricted to his own informations. Run this command to generate our first controller.

`rails g controller users`

An empty test suite for our newly created controller has been automaticaly generated under *spec/controllers/users_controller_spec.rb*. Lets edit it and write some tests.

```ruby
require 'spec_helper'

describe UsersController do
  let!{User.new(firstname: eric, lastname: cartman)}

  describe "get personnal user informations" do
  end

  describe "unauthenticated request" do
  end
end
```

## Creating a user model

## Authentication with Doorkeeper

todo...






