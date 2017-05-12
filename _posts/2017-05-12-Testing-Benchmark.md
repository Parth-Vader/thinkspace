---
layout: post
title: "Testing the stability"
description: "Checking and improving the stability of the spider"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---

After creating the spider, the next tast was to firstly test it with `nginx` instead of `apache`. I did that and results are recorded at [Nginx results](https://github.com/Parth-Vader/bookscraper#using-nginx-instead-of-apache).
The spider performed better with `nginx`, so I continued to use it for further research.

The second (and more important) step was to test the stability of the benchmark. Stability means that the average speed of the benchmark should be quite similar under different circumstances. If several settings of the spider is changed one-by-one, there should not be much difference in the results. Also, the standard deviation of the results should be small enough.

##Testing the stability

I made some changes in the spider file `books.py`: I made it to calculate the total number of requests sent in the whole timespan, and since the spider ran for `30 seconds`, I multiplied the value by `2 (=60/30)` to give a `per minute average speed` 

The result would be written into the file `AvSpeed.txt`.I started with the testing by making a folder `files` and using the command `for i in `seq 1 50`; do scrapy crawl books && mv AvSpeed.txt files/$i.txt; done` in the terminal, so that after running the spider, the result file was saved as ``1.txt,2.txt etc.`

I then took all the data and used it to create a graph and calculate several statistical values : [Stabilty](https://github.com/Parth-Vader/bookscraper/tree/master/Stability).

![Graph](https://raw.githubusercontent.com/Parth-Vader/bookscraper/master/Stability/20170511033241.jpg)
		
		Population size:50

		Mean (μ): 1590.8
		Median: 1672
		Modes: 1632 1718
		Lowest value: 1024
		Highest value: 1806
		Range: 782
		Interquartile range: 213.5
		First quartile: 1523.5
		Third quartile: 1737
		Variance (σ2): 40267.84
		Standard deviation (σ): 200.66848282678
		Quartile deviation: 106.75
		Mean absolute deviation (MAD): 154.368

		99% Confidence Interval: 1515 to 1667
		95% Confidence Interval: 1534 to 1648
		90% Confidence Interval: 1543 to 1638


As we can see that the `standard deviation value` is pretty high, and so is `MAD`. So the given spider is not really stable.

After having a long discussion with my mentor [Konstantin Lopukhin](https://github.com/lopuhin), we concluded that for the benchmark to be stable and natural, it should take a `START_URL` and keep scraping automatically. The `closespider_timeout` could be fixed so that the benchmark is uniform. He also suggested to tweak the settings such as `download_delay,concurrent_requests etc.` and check how it responds to the changes. 

So my next task will be creating the new spider, and testing it's stability. Then, I would have to `log` as much as possible and change it's settings to check how it's responding.

## Important Links found :

`Nginx` installation

* https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04
* https://www.cyberciti.biz/faq/ubuntu-linux-start-restart-stop-apache-web-server/

Learning about `Nginx` vs `Apache`

* https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations

Trying to speed up locally hosted page

* https://www.drupal.org/node/961012

Calculating confidence interevals

* http://www.wolframalpha.com/widget/widgetPopup.jsp?p=v&id=974e2945a18e0bfb8e3aa8becac3e65c&title=Confidence+Interval+Calculator&theme=blue&i0=0.99&i1=12&i2=260.1&i3=4263.1&podSelect&includepodid=Xx%25ConfidenceInterval&showAssumptions=1&showWarnings=1

`Perf` library
* https://github.com/haypo/perf/issues/1