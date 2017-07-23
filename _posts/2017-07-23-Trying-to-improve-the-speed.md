---
layout: post
title: "Trying to improve parsel"
description: "Caching for the function css_2xpath()"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---

The hardware for the [Speedcenter](https://github.com/scrapy/scrapy-bench-speedcenter) has been requested, and it is in process for approval. 

As for the next steps, there were a few points mentioned by my mentor.

* Improving the speed using the results obtained by profiling.
* Creating a benchmark for broad crawling.
* Adding a `--profile vmprof` option for scrapy.

The most exciting option was to try to get speed gains by targetting small function. Hence, I got started with it.

## Comparing xpath and css options for parsel

My mentor informed that there were some discussion regarding `response.xpath()` vs `response.selector.root.xpath()` speeds. So I tried to profile the bookworm spider using them along with the `response.css()` options. The profiling results are as follows :

* `response.selector.root.xpath()` : [VMprof profiling](http://vmprof.com/#/d1856ebe-3dbe-4908-ab5e-44e0ae0bbc9a)
* `response.xpath()` : [VMprof profling](http://vmprof.com/#/393e2093-8c16-4373-b006-e14aeb7933c9)
* `response.css()` : [VMprof profling](http://vmprof.com/#/30d2ce4b-3d2f-492a-b470-64a5ffab41e6?id=5,0,0,0,1,0,0,0&view=flames)

After seeing the results,  `_css_2xpath` seemed a great target for optimization. I have been working on [parsel](https://github.com/scrapy/parsel), and I intend to make the css to xpath conversion faster by caching. I will shortly be opening an issue with a proposed solution.