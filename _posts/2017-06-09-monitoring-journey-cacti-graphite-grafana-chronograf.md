---
title: 'My Monitoring Journey: Cacti, Graphite, Grafana & Chronograf'
sub_heading: "Comparing a few of the big players in monitoring"
redirect_from: /monitoring-journey-cacti-graphite-grafana-chronograf/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Tools
- Monitoring

tags:
- Monitoring
- Grafana
- InfluxDB
- Tools
- DevOps
---
I remember using Cacti at my first job over a decade ago. I've revisited it more than a few times since but it hasn't been my go to monitoring tool for quite some time for a number of reasons, such as:

1.  It's not visually appealing when compared to Grafana and Chronograf.
2.  It's difficult to set up.
3.  It's difficult to maintain.

After Cacti I used Graphite for a short stint. My time with it was good while it lasted but I found it lacking in the following areas:

1.  By the developers' own admission, the Whisper database is slower than RRDtool (whch is used by Cacti).
2.  It's not visually appealing when compared to Grafana and Chronograf.

The first point made me feel like "upgrading" to Graphite is a two steps forward, one step back scenario. As a result of this, I went looking for a solution to this problem, and that is when I came across InfluxDB.

InfluxDB is a time series database. A TSDB _"is a software system that is optimized for handling time series data, arrays of numbers indexed by time (a datetime or a datetime range)"._ In other words, they lend themselves perfectly to monitoring tools such as the ones being discussed in this post.

Now that I had a solution to my gripe, it was time to find a solution to the second. It was at this time that I came across Grafana. When I found that I could use Graphite and InfluxDB as datasources for Grafana, I knew I was onto a winner.

However, as has been the theme of this post, it wasn't long before I found myself wanting more. While Grafana does provide some alerting functionality, it isn't to the depth that I'd like. It was at this point that I started reading about the TICK stack (Telegraf, InfluxDB, Chronograf and Kapacitor).

Given that these products were written by the InfluxDB developers, they had my vote from the start. Things only got better when I started using Telegraf (more on this in another post) and read about Kapacitor's capabilities. Unfortunately though Chronograf just doesn't cut the mustard at this point in time when compared to Grafana. It doesn't come as much of a surprise though given that it is still a relatively young project. I think it has massive potential and will definitely be keeping a close eye on its progress.

The great news is that you don't have to use the TICK stack in its entirety if you don't want to. Therefore, at the time of writing I'm using Telegraf for the data collection, InfluxDB for the data storage and Grafana for data visualisation and alerting. Once I get a little more time on my hands though I will begin using Kapacitor for alerting instead of Grafana.