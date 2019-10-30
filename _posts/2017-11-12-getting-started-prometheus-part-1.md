---
title: Getting Started with Prometheus - Part 1
sub_heading: "Once you go with Prometheus, you won't go back!"
redirect_from: /getting-started-prometheus-part-1/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Monitoring

tags:
- Grafana
- InfluxDB
- DevOps
- Prometheus
---
If you've used [**Grafana**](/tags/#grafana), or even heard of it, chances are you've also heard of [**InfluxDB**](/getting-know-influxdb/) and Prometheus too. As I haven't touched on the latter yet, I figured now is a good time to start. In case you haven't heard of some, or all of these applications, let's start off with a quick description on what they can do for us.

_Note: You might also want to have a read of the [**My Monitoring Journey: Cacti, Graphite, Grafana & Chronograf**](/monitoring-journey-cacti-graphite-grafana-chronograf/) post too._

Grafana is a frontend web app that is used to create beautiful dashboards. It does this by retrieving metrics which are stored on backend database servers such as InfluxDB, Prometheus MySQL, PostgreSQL and Graphite (to name just a few). It then uses metrics to create graphs which are displayed on the aforementioned dashboards.

## Prometheus Vs InfluxDB

These two are known as time series databases (TSDB). What is a TSDB you ask? InfluxDB does a great job of [**answering that question**](https://www.influxdata.com/time-series-database/):

_A Time Series Database (TSDB) is a database optimized for time-stamped or time series data. Time series are simply measurements or events that are tracked, monitored, downsampled, and aggregated over time. This could be server metrics, application performance monitoring, network data, sensor data, events, clicks, trades in a market, and many other types of analytics data. The key difference with time series data from regular data is that you’re always asking questions about it over time._

So, which database should you choose? I recommend reading Prometheus' [**write up on the similarities and differences**](https://prometheus.io/docs/introduction/comparison/#prometheus-vs.-influxdb) between the two. The article also discusses how Prometheus stacks up against other databases too.

Further to this, it's worth noting that Prometheus' _"focus right now is on **operational monitoring**, i.e. the "here and now""_, [**as per this discussion.**](https://github.com/prometheus/prometheus/issues/1381) This is why Prometheus stores only [**15 days of data**](https://prometheus.io/docs/prometheus/latest/storage/) by default. InfluxDB on the other hand has methods which can be employed to allow the efficient storage of years worth of data. Therefore if you're more interested in monitoring/alerting than you are long term data retention, Prometheus might be your tool of choice.

_**Side Note:** I say "might be" because InfluxDB is part of the [**"TICK stack"**](https://www.influxdata.com/time-series-platform/) which uses InfluxDB for storage and [**Kapacitor**](https://www.influxdata.com/time-series-platform/kapacitor/) for monitoring/alerting. Therefore you can still have a monitoring, alerting and graphing solution even if InfluxDB is your database of choice. One other thing to note is that the TICK stack also uses [**Chronograf**](https://www.influxdata.com/time-series-platform/chronograf/) instead of Grafana for graphing._

If you're still having trouble choosing between Prometheus and InfluxDB, the good news is that you can use both! Using [**Remote Storage Endpoints**](https://prometheus.io/docs/operating/integrations/#remote-endpoints-and-storage) allows you to forward data from Promethus to InfluxDB.

## Metrics & Labels

If you're new to TSDBs, you might find Prometheus' [**Data Model**](https://prometheus.io/docs/concepts/data_model/) page a little intimidating. The good news is that it's really not that difficult to wrap your head around it.

Let's start with Metrics. As the [**OpenTSDB documentation**](http://opentsdb.net/docs/build/html/user_guide/query/timeseries.html) points out:

_A time series is a series of numeric **data points** of some particular **metric** over time. A **metric** is any particular piece of data **(e.g. hits to an Apache hosted file)** that you wish to track over time._

Another example would be a metric which has been set up to store [**git**](/tags/#git) commits. Following the Prometheus Best Practices documentation on [**Metric and Label Naming**](https://prometheus.io/docs/practices/naming/), such a metric may be called `git_user_commits_total`.

Now, instead of creating one metric per user and/or repository, we can specify labels within the metric. For example, `author` and `repo`. Given that the [**metric and label notation**](https://prometheus.io/docs/concepts/data_model/) is as follows:

```
{=, ...}
``` 

It means that our Git example will look like this:

```
git_user_commits_total{author=,repo=}
``` 

Before we continue, it's worth [**noting the following caution**](https://prometheus.io/docs/practices/naming/):

_**CAUTION:** Remember that every unique combination of key-value label pairs represents a new time series, which can dramatically increase the amount of data stored. Do not use labels to store dimensions with high cardinality (many different label values), such as user IDs, email addresses, or other unbounded sets of values._

Though somewhat unrelated, it is also [**worth noting that**](https://prometheus.io/docs/concepts/data_model/):

_Changing any label value, including adding or removing a label, will create a new time series._

Keeping the above two points in mind can save you a lot of head scratching and troubleshooting down the track. But I digress...

If you are wondering why the ``  were left  in our `git_user_commits_total` our example, it's because we need to update them programmatically, which we'll discuss next.

## Scraping

Scraping is Prometheus' primary method of collecting data from hosts. [**As Wikipedia puts it:**](https://en.wikipedia.org/wiki/Web_scraping)

_Web scraping (web harvesting or web **data extraction**) is data scraping used for extracting data from websites. Web scraping software may access the World Wide Web directly using the **Hypertext Transfer Protocol**, or through a web browser. While web scraping can be done manually by a software user, the term typically refers to **automated** processes implemented using a bot or web crawler. It is a form of copying, in which **specific data** is gathered and copied from the web, typically into a central local **database** or spreadsheet, for later retrieval or analysis._

This means that in order enable Prometheus to scrape your hosts' datapoints, you'll need to set up a web server on each of them which provides data. The good news is that doing it isn't so hard, if you're using a language which has a Prometheus [**Client Library**](https://prometheus.io/docs/instrumenting/clientlibs/) written for it already. If there isn't, then you might need to [**write your own**](https://prometheus.io/docs/instrumenting/writing_clientlibs/).

## Scraping Alternatives

If you've got hosts which cannot be scraped, you can [**push data instead**](https://prometheus.io/docs/instrumenting/pushing/). Or, if you're using *NIX hosts, you can use [**Node Exporter**](https://github.com/prometheus/node_exporter) to expose the node's metrics. Finally, there are also a whole host of [**Third Party Exporters and Integrations**](https://prometheus.io/docs/instrumenting/exporters/) which may come in handy.