---
layout: post
title: "Scrapy Bookworm Benchmark"
description: "Details about the working and results of the benchmark"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---
I'm currently working on the command line tool for benchmarking scrapy : [Scrapy-BenchCLI](https://github.com/Parth-Vader/Scrapy-BenchCLI).
It has two benchmarks as of now: 

*	Bookworm : Runs a CPU-intesive scrapy spider to follow all links and scrape different features from a static snapshot of [Books To Scrape](http://books.toscrape.com/).
*   Link Extractor : Uses LinkExtractor() to extract links from a collection of HTML pages downloaded using a scrapy spider from `Alexa Top Sites` list.

In this blog post, I will describe the `scrapy-bench bookworm` benchmark. Firstly I'll describe the methodology, which is followed by the comparison of the results in different environments and their summary.

## Methodology

The spider in the bookworm is based on [FollowAll.py](https://github.com/scrapinghub/testspiders/blob/master/testspiders/spiders/followall.py). I used this spider because it only requires a starting url, and then crawls every link present. Another important feature was that it utilised 100% CPU, something that my [previous benchmark](https://github.com/Parth-Vader/bookscraper) did not do.

The spider scrapes a locally hosted vesion of [Books To Scrape](http://books.toscrape.com/). This site was used because, well, it is designed to scraped, and it is static. So I used `wget` to download it, and hosted it on a nginx server. The instructions to do the same are present [here](https://github.com/Parth-Vader/Scrapy-BenchCLI#for-ubuntu).

In my previous blog posts, I have mentioned the various effects of different settings on the benchmark stability. The final settings used in the spider are :

	CLOSESPIDER_ITEMCOUNT = 1000
	RETRY_ENABLED = False
	COOKIES_ENABLED = True

	LOGSTATS_INTERVAL = 3
	LOG_LEVEL = 'INFO'
	MEMDEBUG_ENABLED = True
	CONCURRENT_REQUESTS = 120

>The main inspiration of creating this benchmark was to reflect a real-world usage scenario. 

Now, if I am scraping a site that contains book information, I would like to extract some typical information about the books. Hence, there are some fields that are defined in `items.py` to extract :

	class Page(scrapy.Item):
	    url = scrapy.Field()
	    title = scrapy.Field()
	    size = scrapy.Field()
	    referer = scrapy.Field()
	    newcookies = scrapy.Field()
	    body = scrapy.Field()
	    rating = scrapy.Field()
	    price = scrapy.Field()
	    category = scrapy.Field()
	    stock = scrapy.Field()

The base url is `'http://localhost/books.toscrape.com/index.html'`, and the scraping begins here.
In the `parse()` method of the scrapy spider, the starting time for the spider, the number of pages crawled in `self.items`, and the current time using `datetime.datetime.utcnow()` are stored. Using the two values, the time difference is calculated. When the spider closes, the ratio of total items scraped and the total time (in seconds) is written to the file `Benchmark.txt`, as well as printed. This is the average speed of the spider in items/sec.

In the `bench.py` file, when `bookworm` is selected, the command `scrapy crawl followall -o items.csv` is run, because again, in a real world scenario one would like to save the information into a .csv file.

Following are the results of the benchmark in different environments.

## Benchmark Results

# Different Environments

* Python 2.7 Scrapy 1.4

		The results of the benchmark are (all speeds in items/sec) :


		Test = 'Book Spider' Iterations = '20'


		Mean : 60.1438075176 Median : 60.1091579591 Std Dev : 0.720698025263

The flamegraph for `vmprof` profiling is present [here](http://vmprof.com/#/30d2ce4b-3d2f-492a-b470-64a5ffab41e6).

![flamegraph](https://raw.githubusercontent.com/Parth-Vader/parth-vader.github.io/master/assets/images/21.4.png)

* Python 2.7 Scrapy 1.3

 Note that `twisted : 16.6` was used from here on.

		The results of the benchmark are (all speeds in items/sec) : 


		Test = 'Book Spider' Iterations = '20'


		Mean : 66.7639932167 Median : 66.9154480459 Std Dev : 0.953131520366

The flamegraph for `vmprof` profiling is present [here](http://vmprof.com/#/932297de-8d0e-4893-9ccd-05e17a6a4d76).



* Python 2.7 Scrapy 1.2

		The results of the benchmark are (all speeds in items/sec) : 


		Test = 'Book Spider' Iterations = '20'


		Mean : 65.9508252449 Median : 66.1012409785 Std Dev : 1.29111415588

The flamegraph for `vmprof` profiling is present [here](http://vmprof.com/#/5f8ff5fd-8c4a-42fa-a7e8-e1c3fcfb3913).



* Python 2.7 Scrapy 1.1

		The results of the benchmark are (all speeds in items/sec) : 


		Test = 'Book Spider' Iterations = '20'


		Mean : 65.9597930817 Median : 66.0463768027 Std Dev : 0.719872919193

The flamegraph for `vmprof` profiling is present [here](http://vmprof.com/#/55b58b44-0b3f-4407-972e-4d89237ae217).



* Python 2.7 Scrapy 1.0

		The results of the benchmark are (all speeds in items/sec) : 


		Test = 'Book Spider' Iterations = '20'


		Mean : 69.242276661 Median : 69.6641860445 Std Dev : 0.804613826623

The flamegraph for `vmprof` profiling is present [here](http://vmprof.com/#/97ea79ee-a374-4a09-9b11-5e1b93e45203).



* Python 3.5 Scrapy 1.4

		The results of the benchmark are (all speeds in items/sec) : 


		Test = 'Book Spider' Iterations = '20'


		Mean : 52.842243412731385 Median : 53.022207332934386 Std Dev : 1.0496274427385723

The flamegraph for `vmprof` profiling is present [here](http://vmprof.com/#/7074fdf8-12e5-4043-ad6b-f9a8014cffd7).



* Python 3.5 Scrapy 1.3

  Note that `twisted : 16.6` was used from here on.
		
		The results of the benchmark are (all speeds in items/sec) : 


		Test = 'Book Spider' Iterations = '20'


		Mean : 59.73166463841136 Median : 59.73609164034931 Std Dev : 0.8273136793364986

The flamegraph for `vmprof` profiling is present [here](http://vmprof.com/#/7965b4e2-bf08-45ba-9102-4088b56ea395).


* Python 3.5 Scrapy 1.2

		The results of the benchmark are (all speeds in items/sec) : 


		Test = 'Book Spider' Iterations = '20'


		Mean : 59.67545483270658 Median : 59.7188908945411 Std Dev : 0.6111999538927791

The flamegraph for `vmprof` profiling is present [here](http://vmprof.com/#/e6fec801-e3e8-4fb9-96d2-05b1feb45a8d).



* Python 3.5 Scrapy 1.1
		
		The results of the benchmark are (all speeds in items/sec) : 


		Test = 'Book Spider' Iterations = '20'


		Mean : 58.91004618342678 Median : 59.18484816356225 Std Dev : 0.8001899605637971

The flamegraph for `vmprof` profiling is present [here](http://vmprof.com/#/6d2526d3-3698-4a41-b1c8-45635df607ce).



## Results Summary :

* Results table

![Table](https://raw.githubusercontent.com/Parth-Vader/parth-vader.github.io/master/assets/images/tablenew.png)

* Profiling results list

	* Python 2.7 Scrapy 1.4	- [Result](http://vmprof.com/#/30d2ce4b-3d2f-492a-b470-64a5ffab41e6)		

	* Python 2.7 Scrapy 1.3	- [Result](http://vmprof.com/#/932297de-8d0e-4893-9ccd-05e17a6a4d76)

	* Python 2.7 Scrapy 1.2	- [Result](http://vmprof.com/#/5f8ff5fd-8c4a-42fa-a7e8-e1c3fcfb3913)

	* Python 2.7 Scrapy 1.1	- [Result](http://vmprof.com/#/55b58b44-0b3f-4407-972e-4d89237ae217)

	* Python 2.7 Scrapy 1.0	- [Result](http://vmprof.com/#/97ea79ee-a374-4a09-9b11-5e1b93e45203)

	* Python 3.5 Scrapy 1.4	- [Result](http://vmprof.com/#/7074fdf8-12e5-4043-ad6b-f9a8014cffd7)	

	* Python 3.5 Scrapy 1.3	- [Result](http://vmprof.com/#/7965b4e2-bf08-45ba-9102-4088b56ea395)

	* Python 3.5 Scrapy 1.2	- [Result](http://vmprof.com/#/e6fec801-e3e8-4fb9-96d2-05b1feb45a8d)

	* Python 3.5 Scrapy 1.1	- [Result](http://vmprof.com/#/6d2526d3-3698-4a41-b1c8-45635df607ce)


* Mean Speed vs Environment
<iframe width="1000" height="600" frameborder="0" scrolling="no" src="//plot.ly/~vermaparth97/3.embed"></iframe>
