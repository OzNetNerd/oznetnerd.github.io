---
title: Getting to know Telegraf
sub_heading: "Metrics gathering made easy"
redirect_from: /getting-know-telegraf/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Tools
- Monitoring

tags:
- InfluxDB
- Tools
- Telegraf
- DevOps
---
I first mentioned Telegraf in the [**My Monitoring Journey: Cacti, Graphite, Grafana & Chronograf**](/monitoring-journey-cacti-graphite-grafana-chronograf/) post and then covered its installation and setup in the [**Installing & Setting up InfluxDB, Telegraf & Grafana**](http://www.oznetnerd.com/installing-setting-up-influxdb-telegraf-grafana/) post. Let's now delve a little deeper, shall we?

The good news is that there's a lot less to Telegraf's configuration than what there is to InfluxDB so you'll likely find this post easier to follow than the [**Getting to know InfluxDB**](/getting-know-influxdb/) and article.

## What is it?

Before diving into configurations, it would be best to first cover off what Telegraf actually is. To quote the [**Telegraf GitHub page**](https://github.com/influxdata/telegraf):

_Telegraf is an agent written in Go for collecting, processing, aggregating, and writing metrics._

_Design goals are to have a minimal memory footprint with a plugin system so that developers in the community can easily add support for collecting metrics from well known services (like Hadoop, Postgres, or Redis) and third party APIs (like Mailchimp, AWS CloudWatch, or Google Analytics)._

_Telegraf is plugin-driven and has the concept of 4 distinct plugins:_

*   _**Input Plugins** collect metrics from the system, services, or 3rd party APIs_
*   _**Processor Plugins** transform, decorate, and/or filter metrics_
*   _**Aggregator Plugins** create aggregate metrics (e.g. mean, min, max, quantiles, etc.)_
*   _**Output Plugins** write metrics to various destinations_

A list of plugins can be found on the GitHub page linked to above.

## Default Configuration File

Telegraf comes with quite a large [**configuration file**](https://github.com/influxdata/telegraf/blob/master/etc/telegraf.conf), however only the following are enabled by default:

*   influxdb (output)
*   cpu (input)
*   disk (input)
*   diskio (input)
*   kernel (input)
*   mem (input)
*   processes (input)
*   swap (input)
*   system (input)

If you do not want these plugins enabled and/or if you want to greatly reduce the size of your configuration file, you can create a new configuration file that contains only the plugins your interested in using the following command:

```
telegraf --config telegraf.conf -input-filter cpu:disk:diskio:kernel:mem:processes:swap:system:snmp -output-filter influxdb config > telegraf.conf
```

## More Information

That wasn't too painful now was it? :) See the [**Telegraf: SNMP Input Plugin**](/telegraf-snmp-input-plugin/) post for information on how to use the SNMP plugin.

You can also find a lot of useful information on both the [**Telegraf GitHub**](https://github.com/influxdata/telegraf) page, as well as the [**Telegraf website**](https://docs.influxdata.com/telegraf/v1.3/introduction/getting_started/).