---
layout: post
title: "Modifying the benchmark and creating a CLI"
description: "Stabilised the benchmark and made a basic command line tool"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---

In the previous [post](https://parth-vader.github.io/2017/A-New-Bench/) I had mentioned that I had created a new base spider for the benchmark. The [spider](https://github.com/Parth-Vader/benchmarknew) had previously shown better stability but was not stable enough to detect small defects. 

Hence, I started with changing the settings to 

	CLOSESPIDER_ITEMCOUNT = 1000
	RETRY_ENABLED = False
	COOKIES_ENABLED = False
	LOGSTATS_INTERVAL = 3
	LOG_LEVEL = 'INFO'
	MEMDEBUG_ENABLED = True
	CONCURRENT_REQUESTS = 100

By increasing the `CONCURENT_REQUESTS` to 100 from 16, the speed of the spider increased and it became more stable. In fact, I got the best results (at that point) :

	Population size:50

	Mean (μ): 63.88
	Median: 64
	Mode: 65
	Lowest value: 59
	Highest value: 65
	Range: 6
	Interquartile range: 2
	First quartile: 63
	Third quartile: 65
	Variance (σ2): 1.9456
	Standard deviation (σ): 1.394847661933
	Quartile deviation: 1
	Mean absolute deviation (MAD): 1.0576

The next step I chose was to see how the scraping speed of the spider varies. For that purpose, I noted the `number of items scraped` and divided it by the `time taken` at each instant. Then, I plotted it using an online tool. This was repeated several times until I stared getting a pattern.

Some of the plots are as follows :

![Spider 1](https://raw.githubusercontent.com/Parth-Vader/benchmarknew/master/stability/Page-Shot-2017-5-17%20Online%20Graph%20Maker%20%C2%B7%20Plotly(1).png?token=APhACHm3y1vw0l7QEmUcyWkbuWbydBJoks5ZJXoRwA%3D%3D)
![Spider 2](https://raw.githubusercontent.com/Parth-Vader/benchmarknew/master/stability/Page-Shot-2017-5-17%20Online%20Graph%20Maker%20%C2%B7%20Plotly.png?token=APhACMS4wmXftozNxlzluo9Dkm8hvuv_ks5ZJXoUwA%3D%3D)
![Spider 3](https://raw.githubusercontent.com/Parth-Vader/benchmarknew/master/stability/Screenshot-2017-5-17%20Online%20Graph%20Maker%20%C2%B7%20Plotly.png?token=APhACP8kfEr5K7ZXxC9Ri5v6pzUJLQsVks5ZJXoXwA%3D%3D)

We can observe that the speed stabilises after around 300 items, so I discarded the first 300 items for the calculation of the average speed. I now started measuring time after 300th item was scraped for calculating the speed. The results were a little more stable than before.

The next setting I wanted to see the effect was `CONCURRENT_REQUESTS`. For that I varied it and took readings. Some of the results are :

When CONCURRENT_REQUESTS = 10 :

	Population size:50

	Mean (μ): 57.914509901472
	Median: 57.49577374755
	Mode: No
	Lowest value: 48.3399842982
	Highest value: 66.6062245625
	Range: 18.2662402643
	Interquartile range: 7.20653028805
	First quartile: 54.612051058325
	Third quartile: 61.818581346375
	Variance (σ2): 18.473481862359
	Standard deviation (σ): 4.2980788571592
	Quartile deviation: 3.603265144025
	Mean absolute deviation (MAD): 3.6365766199846

When CONCURRENT_REQUESTS = 100 :

	Population size:20

	Mean (μ): 58.068316935185
	Median: 58.89404859485
	Mode: No
	Lowest value: 50.2820789954
	Highest value: 66.6083017011
	Range: 16.3262227057
	Interquartile range: 4.901280544625
	First quartile: 55.180792304725
	Third quartile: 60.08207284935
	Variance (σ2): 13.726370736525
	Standard deviation (σ): 3.7049117042819
	Quartile deviation: 2.4506402723125
	Mean absolute deviation (MAD): 2.898659479338

Histogram : 

![Histogram](https://raw.githubusercontent.com/Parth-Vader/benchmarknew/master/stability/Page-Shot-2017-5-19%20Histogram%20Maker%20%C2%B7%20Plotly%20Online%20Chart%20Editor.png?token=APhACN1g6il9tOz1iImwk2ZHTiigI9bZks5ZJ9xkwA%3D%3D)

When CONCURRENT_REQUESTS = 120 :

	
	Population size:20

	Mean (μ): 64.629335444995
	Median: 65.1046648778
	Mode: No
	Lowest value: 58.6573798789
	Highest value: 67.5569293799
	Range: 8.899549501
	Interquartile range: 2.640101009925
	First quartile: 63.9208027457
	Third quartile: 66.560903755625
	Variance (σ2): 5.91760829821
	Standard deviation (σ): 2.4326134707779
	Quartile deviation: 1.3200505049625
	Mean absolute deviation (MAD): 1.849888751116

Histogram : 

![Histogram](https://raw.githubusercontent.com/Parth-Vader/benchmarknew/master/stability/Page-Shot-2017-5-19%20Histogram%20Maker%20%C2%B7%20Plotly%20Online%20Chart%20Editor(1.png?token=APhACNojA35QcteSKEHBSNJFfMWGag0aks5ZJ91TwA%3D%3D)

Since the stability (as well as the speed) increased with `CONCURRENT_REQUESTS`, I chose the settings `CONCURRENT_REQUESTS = 120`.

After stabilising the benchmark reasonably, the next step was to see it's usability by checking it with different versions of Scrapy and Python.

You can have a look at the results here : [Comparisons](https://github.com/Parth-Vader/benchmarknew/tree/master/comparisons#different-environments).

The stability achieved is pretty great, and the results show how speed varies with each Scrapy and Python version. 

* The speed was better with Python2.
* The results with Python3 were a little more stable.
* Older versions (1.2,1.3) seem faster than the latest version. 

After getting them checked by my mentor [Konstantin Lopukhin](https://github.com/lopuhin), I started with creating the command line tool for benchmarking Scrapy. I had tried the library [`Click`](https://github.com/pallets/click) and I really liked it. So I started the project [Scrapy-BenchmarkCLI](https://github.com/Parth-Vader/Scrapy-BenchCLI).

My next tasks would be to work on improving the CLI and creating more useful benchmarks. 



