---
layout: post
title: "A new benchmark"
description: "Changed the base benchmark"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---

The previous [benchmarking spider](https://github.com/Parth-Vader/bookscraper) was not up to the mark because of the following reasons :
	* It did not use around `100%` of my CPU.
	* I had already defined the urls which were to be extracted in `urls.txt` file. Thus, this spider did not reflect a real case scenario.
	* The [statistics](https://github.com/Parth-Vader/bookscraper/tree/master/Stability#testing-the-stability-of-the-benchmark) for the results of the benchmark showed the value of `MAD` was rather big.
	* There were instances where the instantaneous scraping speed would be `zero` for some seconds.

Because of the given reasons, I had to make a lot of changes in the spider. Fortunately, I found a `CPU-intensive` sample spider [`Followall`](https://github.com/scrapinghub/testspiders/blob/master/testspiders/spiders/followall.py).

When I ran the spider, I saw it was using almost `full CPU` during the run, and it was actually visiting all the links present in the give site. Hence, I decided to use it as the basis for my benchmark.

The new benchmark (currently private) was tested with the regular settings :

	CLOSESPIDER_TIMEOUT = 20
	RETRY_ENABLED = False
	COOKIES_ENABLED = False
	LOGSTATS_INTERVAL = 3
	LOG_LEVEL = 'INFO'

Since the spider was able to perform all the requests within `30 seconds`, I fixed the time to be `20 seconds`. I calculated the estimate for the average speed by taking the total number of items scraped and multiplying it by `60/20=3`.

I ran the spider for 50 times and collected the average speed data. The statistics for the results are as follows : 

### per 20 seconds

	Population size:50

	Mean (μ): 975.26
	Median: 971
	Mode: 1006
	Lowest value: 867
	Highest value: 1071
	Range: 204
	Interquartile range: 79.5
	First quartile: 933
	Third quartile: 1012.5
	Variance (σ2): 2470.8323999999
	Standard deviation (σ): 49.707468251762
	Quartile deviation: 39.75
	Mean absolute deviation (MAD): 41.9504

### Per minute

Multiplying this by `3`, the statistics are :

	Population size:50

	Mean (μ): 2925.78
	Median: 2913
	Mode: 3018
	Lowest value: 2601
	Highest value: 3213
	Range: 612
	Interquartile range: 238.5
	First quartile: 2799
	Third quartile: 3037.5
	Variance (σ2): 22237.4916
	Standard deviation (σ): 149.12240475529
	Quartile deviation: 119.25
	Mean absolute deviation (MAD): 125.8512

As can be seen from the previous [blog post](https://parth-vader.github.io/2017/Testing-Benchmark/), this is a great improvement in the results.

## Changing the speed estimate

After playing with the spider for sometime, I realised that a better estimate for the `average speed` could be obtained by making `CLOSESPIDER_ITEMCOUNT = 1000`, and calculating the actual time the spider ran. Then the ratio of total items scraped and the total time could be taken as an estimate.

So I changed the settings to :
	
	CLOSESPIDER_ITEMCOUNT = 1000
	RETRY_ENABLED = False
	COOKIES_ENABLED = False
	LOGSTATS_INTERVAL = 3
	LOG_LEVEL = 'INFO'

So, I made the changes in my main spider file, and obtained the results. I obtained the results for 50 iterations of the spider and here is the statistics for the same :

	Population size:50

	Mean (μ): 3654.34
	Median: 3681.5
	Mode: No
	Lowest value: 3104
	Highest value: 4008
	Range: 904
	Interquartile range: 294.5
	First quartile: 3558.75
	Third quartile: 3853.25
	Variance (σ2): 54810.584400001
	Standard deviation (σ): 234.11660428086
	Quartile deviation: 147.25
	Mean absolute deviation (MAD): 187.9392

The results have actually worsened with respect to the stability of the spider. I actually took the total number of items scraped and multiplied it with `60/Total_time` so that I get a value in `per minute` form. I think that if I use a `per second` speed for the same, the result would be more accurate.

I would take more results into account and check for outliers. I would also see if the `per second` value is a better estimate.

## Important Links found :

Collection of different spiders

* https://github.com/scrapinghub/testspiders
* https://github.com/ThaWeatherman/scrapers

Great tool for plotting graphs online

* http://plot.ly/

Online statistics calculator

* http://www.alcula.com/calculators/statistics/dispersion/