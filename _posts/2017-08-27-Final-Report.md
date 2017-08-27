---
layout: post
title: "Creating a broad crawl benchmark and increasing speed"
description: "Broad crawl using twisted server"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---

# Google Summer of Code — Final Report

* Project: Scrapy Benchmarking Suite
* Organisation: Python Software Foundation (Scrapinghub)
* Mentors: Konstantin Lopuhin and Paul Tremberth

>Firstly, I would like to thank my mentors Konstantin and Paul for guiding me through the project, reviewing my code, pointing out ways for improvement and, above all, maintaining the incredibly motivating atmosphere.
Scrapy is a fun organisation to work with — the developers are very responsive and helpful. Working with them will be an invaluable experience.
## About Scrapy

Scrapy is a free and open source web crawling framework, written in Python. It has a lot of helpful features, and is very beginner friendly. It’s one of the few repositories on Github with 20k+ stars.

A scrapy spider is a class which defines how a certain site (or a group of sites) will be scraped, including how to perform the crawl (i.e. follow links) and how to extract structured data from their pages (i.e. scraping items).

## Project Overview

My main aim in this project was to create a benchmark suite for Scrapy, preferably a command line tool, which would imitate real-life scenarios and test various components of a scrapy spider. The main repository is here : Scrapy-Bench.

My secondary target was to create a platform where the Scrapy-Bench would test the scrapy code at regular intervals, whenever a new PR is merged, and for all versions of scrapy and python. This site would be hosted at speed.scrapy.org (in the future), and the current repository is here : Scrapy-Bench-Speedcenter.

Another aspect of this project was to use the benchmark results to identify bottlenecks and try to improve them.


## Scrapy-Bench CLI
>A command line tool for benchmarking Scrapy : that reflects real-world usage.

Before the project, Scrapy had scrapy bench option which spawns a spider which aggressively crawls randomly generated links at a very high speed. This isn’t reflective of a real-world usage, and in addition, the actual speed is dependant on the hardware, as well as the python and scrapy versions.

Hence, I started working on creating the command line tool. 

The following are the various benchmarks implemented :
* bookworm : This is a CPU-intensive spider (uses 100% CPU) that follows a locally hosted site (on nginx). I chose the site to be Books to Scrape, since it is very similar to a major use case for the scrapy spider. The spider extracts various aspects for a book store, i.e. it’s title, price and number of books available etc. This information is stored in a csv file as well. The spider stops when it has extracted 1000 items. 
The speed is then calculated by dividing 1000 by the total time taken by the spider.
* broadworm : This spider tests the spider performance in a broad crawl.
A broad crawl is where the spider is not limited by the speed of a single website, as it has many different sites to crawl. While the spider still has to download and parse pages, there are additional challenges: different sites take different time to answer queries, some might be down, and the spider needs to make a lot of requests concurrently to maintain good throughput and be smart at choosing while domains to load data from.
To accomplish this, I used twisted to create a mock-server, which generates the local copy of Books to Scrape on the go, adding random delays to it. These delays imitate the web, the values are mostly small (within 1 second) but they sometimes shoot up to 6–10 seconds. After making changes to /etc/hosts , 1000 domains point to the same site, which is then crawled by the spider.
* cssbench : This benchmark loads 200 html pages of the site Books to Scrape, and extracts the various elements using response.css().
* xpathbench : The same 200 pages are extracted using response.xpath().
* linkextractor : This extractor extracts links using LinkExtractor() from html pages of top 130 Alexa sites.

These benchmarks can be run several times, and the results can be uploaded to a locally hosted scrapy-bench-speedcenter.

Through tox integration, the results for various scrapy and python versions can be obtained and uploaded.

Reliability of the benchmarks : The results of each benchmark follow a specified format : the mean, median and the standard deviation of the results is calculated.

I had spent a lot of time tuning the settings of the bookspider in order to achieve a standard deviation under 1 item/second so that the results are more reproducible and be easily compared across various machines.
Scrapy-Bench-Speedcenter

## Speedcenter

Scrapy-Bench-Speedcenter is inspired from PyPy’s speedcenter : speed.pypy.org. They used codespeed , which is a Django-based web application for monitoring the performance. Hence, after a talk with my mentor Konstantin, I decided that it would be great to store the results for the previous scrapy versions, along with checking the performance after every new PR. The application is going to be hosted on speed.scrapy.org.

## Impoving performance

After profiling the bookworm spider using vmprof, there was a difference in response.xpath() , response.root.selector.xpath() and response.css() . The function css_to_xpath() seemed to be taking around 5% of the total time when response.css() was used. I opened an issue in parsel , and after a discussion, we concluded that we could use LRU caching for the function.

An LRU (least recently used) cache works best when the most recent calls are the best predictors of upcoming calls (for example, the most popular articles on a news server tend to change each day).

After implementing Python3’s functools.lru_cache() , I observed a lot of speed improved with the cssbench benchmark. For python 2.7, I used a backport for the functools.lru_cache().The results are as follows :

* Python 3.5
  * Without caching : 280 pages/second
  * With caching : 378 pages/second
* Python 2.7
  * Without caching : 287 pages/second
  * With caching : 379 pages/second

I made a [PR](https://github.com/scrapy/cssselect/pull/79) in cssselect where the actual function to be cached was present, but it couldn’t be merged due to absence of lru_cache() in python2 which results in adding an extra dependency. This would cause problems to other organisations which use cssselect , hence the PR needs to be shifted to parsel .

## Future Work

Following are the areas where improvement could be done :-
* There are several issues opened for scrapy-bench [here](https://github.com/scrapy/scrapy-bench/issues).
* Adding profilers (for example vmprof ) for scrapy, so that it can be invoked via command-line for any general spider. The issue is opened [here](https://github.com/scrapy/scrapy/issues/2792).
* Profiling scrapy with PyPy to obtain performance problems so that they can be reported to PyPy’s issue tracker. One such [profiling](http://vmprof.com/#/dd665457-68b1-476d-9f49-6ff69c7eef72) done by me shows that xpath() and _iter_links() take up a lot of time.
* Identifying more functions that could be improved in order to increase the code performance.
* Shift the [PR](https://github.com/scrapy/cssselect/pull/79) to parsel so that it can be merged without affecting other organisations who rely on cssselect .

## Important Links
* Scrapy-Bench : https://github.com/scrapy/scrapy-bench/
* Scrapy-Bench-Speedcenter : https://github.com/scrapy/scrapy-bench-speedcenter/
* css_to_xpath() caching PR : https://github.com/scrapy/cssselect/pull/79 (open)
* My blog : https://parth-vader.github.io/
