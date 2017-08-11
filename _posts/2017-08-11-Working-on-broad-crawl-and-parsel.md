---
layout: post
title: "Creating a broad crawl benchmark and increasing speed"
description: "Broad crawl using twisted server"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---

## Caching css_to_xpath()

In my previous post I had mentioned that I was aiming for increasing the speed for css to xpath conversion using LRU caching for a function in `parsel`. I had created [this](https://github.com/scrapy/parsel/issues/98) issue in parsel where I had mentioned my strategy. 

I used [cssbench](https://github.com/scrapy/scrapy-bench/blob/master/cssbench.py) which is a micro-benchmark that focuses just on the `response.css()` part to measure the speeds.

For python 3.5 : 
* Without using `functools.lru_cache` : Rate of link extraction : 280.16969651894124 pages/second.
[Profiling result](http://vmprof.com/#/938c3c83-566a-4852-9623-e0fc5736abe1?id=1,1&view=flames)
* With using `functools.lru_cache(maxsize=256)` : Rate of link extraction : 377.51599179531786 pages/second
[Profiling result](http://vmprof.com/#/2361411e-fc9e-4a8f-9f67-c24e2be9a040?id=1,2&view=flames)

I got similar results using [backport for functools](https://github.com/MiCHiLU/python-functools32) for python 2.7 too.

* Without using `functools32.lru_cache` : Rate of link extraction : 286.718460008 pages/second
[Profiling result](http://vmprof.com/#/fb7b26fd-6412-43d7-8711-b86fc80b636c)
* With using `functools32.lru_cache(maxsize=256)` :Rate of link extraction : 379.126075593 pages/second
[Profiling result](http://vmprof.com/#/88bbea7e-7a24-49ea-9879-965aece526fd)

As you can see the speed drastically improved using the caching. I had made the PR for the same in `cssselect`, but the maintainers informed me that this would create a little problem for others who use `cssselect` as I had used an extra dependency (functools32). Hence, I would be applying the same for `parsel`.

## Creating a broad crawl benchmark

I now intend to create a new benchmark - broad crawl for scrapy, where the spider is not limited by the speed of a single website, as it has many different sites to crawl. 
To create that, I will be using the same bookworm spider. While the spider still has to download and parse pages, there are additional challenges: different sites take different time to answer queries, some might be down, and the spider needs to make a lot of requests concurrently to maintain good throughput and be smart at choosing while domains to load data from.

I have started with writing a server using `twisted` which will read contents of the same book scraping site, and will add random delays and error.

The issue for the same is [here](https://github.com/scrapy/scrapy-bench/issues/10).