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

When you leave the house in the morning, you ping OTD from your
smartphone (preferably automatically), and OTD in turn notifies
Beeminder how many minutes early or late you are.  If you start to
miss your target repeatedly, Beeminder's incentives kick in.

## The Feature

With such a simple app, the Cucumber test practically writes itself.
In your project directory, create a file called `features/otd.feature`
with the following contents:

    Feature: Time logging

      Scenario: Leaving
        Given I want to leave at 8:00
        When I leave at 8:17
        Then I am 17 minutes late

For the real project, we'd use something like [Capybara][capybara] to
drive the app through its HTTP interface.

## The Step Definitions

## Getting Started with Codeship

## Putting it all Together

[otd]:https://bitbucket.org/undees/otd
[bee]:https://www.beeminder.com
[capybara]:foo
[vcr]:https://github.com/vcr/vcr
