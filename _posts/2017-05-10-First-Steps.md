---
layout: post
title: "The first steps"
description: "About the first steps taken for the project"
comments: true
keywords: "GSOC,2017,tech,parth,verma,scrapy,IIT,Kharagpur,koss"
---

I got started with my project by creating a sample spider that scrapes 1000 links specified in the `urls.txt` file from the site [Books To Scrape](http://books.toscrape.com/). After having a discussion with my mentor, I tried running the spider with different settings. You can see the spider here : [BookScraper](https://github.com/Parth-Vader/bookscraper).

After getting the results, my mentor suggested to move the site to a local server and then scrape it and see the results. I used `wget` to download a static snapshot of the site and hosted it on a local server using `Apache`. The `urls.txt` was modified accordingly. The results were again recorded.

The spider was using around `35% CPU`, whereas the ideal value should be `100%` since we want the download process to be as fast as possible. We found out that since it was a static website, the spider would not take so much CPU.

My next task is to test whether the benchmarking spider is stable or not. That would be done by taking a lot of test cases (around 50) and plot the values to check the distribution.

## Important Links found :

Creating a local copy of the website
* https://askubuntu.com/questions/70478/can-i-copy-serve-a-website-locally-on-the-network
* https://www.guyrutenberg.com/2014/05/02/make-offline-mirror-of-a-site-using-wget/

Using command line to edit multiple files
* https://askubuntu.com/questions/84007/find-and-replace-text-within-multiple-files

Understanding `htop` command and `CPU` usage of various processes.
* https://www.cyberciti.biz/tips/how-do-i-find-out-linux-cpu-utilization.html
* https://askubuntu.com/questions/450045/show-cpu-usage-using-a-command
* https://codeahoy.com/2017/01/20/hhtop-explained-visually/
