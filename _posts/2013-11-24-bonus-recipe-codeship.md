---
title: Bonus Recipe&#x3a; Cucumber + Codeship for Continuous Integration
layout: post
---
{% include JB/setup %}
<!-- -*- coding: utf-8 -*- -->

In Recipe 10 of [the book][book], we show you how to set up your own
Jenkins continuous integration server to run Cucumber every time you
push your latest code changes.  Jenkins is of course not the only CI
option out there.  The [CruiseControl][cruise] family, just to name
one, can do a lot of the same things.

You also have the option of using a hosted service such as
[Travis CI][travis] or [Codeship][codeship], where the vendor takes
care of maintaining the server for you.  These are a nice, low-hassle
way to get started with CI.

In this recipe, we're going to show you how to get started with
Cucumber on the Codeship hosted Continuous Integration service.

[book]:http://pragprog.com/titles/dhwcr
[cruise]:http://cruisecontrol.sourceforge.net
[travis]:https://travis-ci.org
[codeship]:https://www.codeship.io

## The App

We'll use a toy time-tracking app called [Out the Door][otd] as the
guinea pig for this experiment.  OTD is a simple web service that
helps you track your punctuality on the [Beeminder][bee] goal-tracking
website.

Here's how the app works: when you leave the house in the morning, you
ping OTD from your smartphone (preferably automatically), and OTD in
turn notifies Beeminder how many minutes early or late you are.  If
you start to miss your target repeatedly, Beeminder's incentives kick
in.

[otd]:https://bitbucket.org/undees/otd
[bee]:https://www.beeminder.com

## The Feature

With such a simple app, the Cucumber test practically writes itself.
In your project directory, create a file called `features/otd.feature`
with the following contents:

    Feature: Time logging

      Scenario: Leaving
        Given I want to leave at 8:00
        When I leave at 8:17
        Then I am 17 minutes late

Eventually, we'll test this app through its web interface.  But just
for the purposes of getting started with CodeShip, let's start with
simple math on the departure times.

## The Step Definitions

Create `features/step_definitions/otd_steps.rb`, and add the following
step definitions to it:

    require 'time'

    Given(/^I want to leave at (\d+:\d+)$/) do |time|
      @desired = Time.parse time
    end

    When(/^I leave at (\d+:\d+)$/) do |time|
      @achieved = Time.parse time
    end

    Then(/^I am (\d+) minutes late$/) do |minutes|
      late = (@achieved - @desired) / 60.0
      late.should be_within(0.001).of(minutes.to_f)
    end

This code is Ruby-agnostic, but we'll be giving the examples here
using [JRuby][jruby].  Install Cucumber and make sure the feature
passes:

    $ jruby -S gem install cucumber rspec-expectations
    $ jruby -S cucumber
    ....
    1 scenario (1 passed)
    3 steps (3 passed)
    0m2.350s

Once that's working, it's time to create your project on Codeship.

[jruby]:http://jruby.org

## Getting Started with Codeship

You can create an account on Codeship with your existing GithHub or
Bitbucket credentials, or with an e-mail address and password.  Once
you've done so, they'll lead you to the new project page:

![New project](/assets/codeship-new.png)

Codeship needs to know where to get the source code for your app.
Head over to GitHub or Bitbucket and create a blank repository.  Then,
click the corresponding button on the Codeship new project page and
choose the repo you just created.

Once you've created your project, you need to tell Codeship how to
test it.  Codeship uses the familiar [Ruby Version Manager][rvm] to
choose Ruby implementations, so all you need to do is tell their
server to make sure JRuby is installed.

In the Test tab of the user interface, leave the default technology
stack set to "I want to create my own custom commands."  Then, enter
the following text into the Setup Commands window:

    rvm install jruby-1.7.8
    rvm use jruby-1.7.8
    jruby -S gem install cucumber rspec-expectations

Further down the page, in the Test Commands, window, type:

    jruby -S cucumber

Now, when you save your project, Codeship will prompt you to complete
the setup by adding a hook to your project:

![Hook](/assets/codeship-hook.png)

Follow the on-screen instructions to configure Github or Bitbucket to
use the Codeship hook.

On your local machine, commit your project to Git and push it up to
the server, so that Codeship can see it:

    git init
    git add features
    git commit -m "Initial commit"
    git remote add origin git@...:username/otd.git

As soon as you push the project, Codeship will grab the latest version
and run your Cucumber features:

![New build](/assets/codeship-build.png)

Congratulations!  You've created your first passing Codeship
integration.

[otdbb]:https://bitbucket.org/undees/otd
[rvm]:http://rvm.io

## What's Next?

Now that we have a running project, what do we do?  There are several
ways we could improve this project:

* Switch to [Bundler][bundler] for managing our gems
* Add the actual app code to the project
* Replace the pure-math step definitions with real ones that drive our
  app over HTTP
* Use Heroku to deploy the app

In a future post, we'll tackle a few of these tasks.

[bundler]:http://bundler.io
