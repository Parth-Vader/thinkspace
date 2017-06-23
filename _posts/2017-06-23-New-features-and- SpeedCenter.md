---
layout: post
title: "Adding more features and creating a Speedcenter"
description: "Describing a new benchmark and the speedcenter created"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---

The `scrapy-bench` benchmark has been moved to the official [scrapy repo](https://github.com/scrapy/scrapy-bench). The development now takes place there. In the previous post, I had described that there were 2 benchmarks present, namely the `book spider (bookworm) benchmark` and `linkextractor benchmark`.

## Adding a new benchmark

I had profiled the `bookworm benchmark` using `vmprof` (as mentioned in the previous post), and one of the bottlenecks present in each case was xpath extraction. Hence, I discussed it with my mentors and worked on creating the benchmark for it.
The new benchmark ` [xpathbench](https://github.com/scrapy/scrapy-bench/blob/master/xpathbench.py) `  tests extraction using xpath from a collection of html pages, downloaded using a scrapy spider.

After testing it in different environments, there was a notable difference in the results.

![Xpath results](https://raw.githubusercontent.com/Parth-Vader/parth-vader.github.io/master/assets/images/xpath.png)

## Creating a web app for collecting results

After creating the benchmarks, there was a need for something where the results of the benchmark could be saved. Taking a look at [PyPy SpeedCenter](http://speed.pypy.org), I found that they use [codespeed](https://github.com/tobami/codespeed). Hence, I set-up a codespeed app for scrapy [here](https://github.com/scrapy/scrapy-bench-speedcenter/). This app will (in future) will be hosted online, and like pypy speedcenter, would track the benchmark results for scrapy commit by commit, as well as store the results for various combinations of scrapy versions and python versions. I set up ` [tox](https://github.com/tox-dev/tox) ` which helps in running the benchmark and uploading the result to the app. I also added `upload_result` option for uploading the result to the codespeed app. Currently the app is running on a local server.

![Screenshot](https://raw.githubusercontent.com/Parth-Vader/parth-vader.github.io/master/assets/images/Page.png)

More work would be done in improving the web app as well as taking it online.