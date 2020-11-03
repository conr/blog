---
title: Twitter bots with AWS Lambda
date: "2020-10-12T11:00:00.169Z"
description: A Twitter bot with AWS Lambda
---

## Intro

Hello. It's been quite some time since the last post on this blog. In the interim [a lot](https://www2.hse.ie/coronavirus/) has happened 🦠!
I was in the process of writing a step-by-step tutorial on how to create this Twitter bot but it was tedious to write and pretty boring to read.

Instead let's just link the [source code](https://github.com/conr/covid19-twitter-bot/) so you can have a look for yourself and talk about why I bothered making it and why  I believe Lambda is a handy tool for replacing the cron jobs in your life.

## What is it exactly?

It's a bot that queries [Ireland's COVID-19 Data Hub](https://covid19ireland-geohive.hub.arcgis.com/) each day at 18:30 and tweets out the latest stats on the number of cases, deaths, hospitizations, and cases in ICU. Here's its [Twitter account](https://twitter.com/ireland_covid19).

![twitter](./1.png "Twitter")

## Why?

I made this bot largely because I wanted to be pinged ("hit that bell icon!") when the new daily stats for Covid19 in Ireland were released. I found myself Googling for them each day and this made it slightly less annoying to find them. I know there's probably a much quicker way to do this that doesn't involve writing any code but where's the fun in that?

## Who cares?

I do! Do you have a box deployed that runs a few cron jobs once a day or so? Well I certainly have and I believe Lambda makes it far easier to schedule, maintain, and monitor these jobs. Instead of using crontab and some hairy scripts - take some time to refactor these hairy scripts into Lambda functions, use Eventridge to easily schedule them, let Lambda manage any environment variables, and monitor with CloudWatch.

The main win in my eyes here is convinience. Rather than having scripts scattered around the place that don't leave much of a trace when they fail, you now have everything nicely organised on Lambda. After you've deployed your function, you're a few clicks away from the scheduling and monitoring too as well as having a history of previous runs and their logs. It's also essentially free. You get 1 million free function invocations per month with Lambda.

There are a few catches, namely the time it takes your script to run. Lambda functions will timeout after 15 minutes so if it's a long running task you may want to look elsewhere.
