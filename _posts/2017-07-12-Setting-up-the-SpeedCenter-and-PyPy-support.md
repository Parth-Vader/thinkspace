---
layout: post
title: "Making changes to SpeedCenter and benchmarking with pypy"
description: "Describing a new benchmark and the speedcenter created"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---

## Speedcenter

In my previous post, I had mentioned [Speedcenter](https://github.com/scrapy/scrapy-bench-speedcenter) for storing of results of the benchmarks. There were some changes made to it, and it is almost ready to be deployed on the web.

My goal is to create something like [PyPy Speedcenter](http://speed.pypy.org/) for scrapy, so that we can track it's performance on the basis of various benchmarks of [scrapy-bench](https://github.com/scrapy/scrapy-bench/), with each commit as well in various environments. This will give a clear idea as to how scrapy is performing on various hardware.

Following are some screenshots of various tabs for the app : 

![home](https://raw.githubusercontent.com/Parth-Vader/parth-vader.github.io/master/assets/images/home.png)

![changes](https://raw.githubusercontent.com/Parth-Vader/parth-vader.github.io/master/assets/images/changes.png)

![timeline](https://raw.githubusercontent.com/Parth-Vader/parth-vader.github.io/master/assets/images/timeline.png)


## Pypy Support for scrapy

My mentor [Konstantin](https://github.com/lopuhin) had been working on adding pypy support for scrapy, and it was completed a few days ago. Our main aim was to enjoy pypy's speed gains for scrapy. After getting it running on my machine, I benchmarked and profiled it with `scrapy-bench`. The result for it's `vmprof` profiling is [here](http://vmprof.com/#/dd665457-68b1-476d-9f49-6ff69c7eef72). As we can see from the graph, `xpath` and `_iter_links` are taking up most of the time.

My next aim is to narrow down performance problems to some specific sections so that we can submit it to pypy's issue tracker.

## Tox support for scrapy-bench

Tox support was added for `scrapy-bench` [here](https://github.com/scrapy/scrapy-bench/blob/master/tox.ini). Now, the benchmarks will run in various environments via a single command and the results would be uploaded to the web app.


The web app would be online in a few days (most probably on speed.scrapy.org).