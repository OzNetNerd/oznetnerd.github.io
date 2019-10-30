---
title: Getting to know InfluxDB
sub_heading: "So, tell me about yourself..."
redirect_from: /getting-know-influxdb/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Tools
- Monitoring

tags:
- InfluxDB
- Tools
- DevOps
---
I touched on InfluxDB in the [**My Monitoring Journey: Cacti, Graphite, Grafana & Chronograf**](/monitoring-journey-cacti-graphite-grafana-chronograf/) post and then covered its installation and setup in the [**Installing & Setting up InfluxDB, Telegraf & Grafana**](/installing-setting-up-influxdb-telegraf-grafana/) post. Now it's time to look at how the database actually works and commands we can use to integrate it.

## InfluxDB Structure

In the latter mentioned post above we saw that Telegraf had created a telegraf  database in InfluxDB. Let's now jump into InfluxDB and take a look at this database:

$ influx --username influx --password influx_pass

To view a list of all of the databases, issue the following show databases  command:

```
> show databases
name: databases
name
{% raw %}----{% endraw %}
telegraf
_internal
```

As the name suggests, _internal  is an internal InfluxDB database, therefore let's take a look at what's happening in the telegraf  database. To do this we must first execute the use telegraf  command:

```
> use telegraf
Using database telegraf
```

Now that we're inside of the telegraf database, let's dig a little deeper using the show measurements  command:

```
> show measurements
name: measurements
name
{% raw %}----{% endraw %}
cpu
disk
diskio
kernel
mem
processes
swap
system
```

As the [**InfluxDB documentation**](https://docs.influxdata.com/influxdb/v1.2/introduction/getting_started/) explains, SQL **measurements** can be thought of as SQL tables. As the **measurement** names above suggest, each one contains information which pertains to a specific entity (e.g cpu  contains CPU utilisation data).

Each record stored inside of a **measurement** is known as a point . Points are made up of the following:

*   time : Timestamp that represents the time in which the data was recorded.
*   field : Contain the actual measurement data, e.g 5% CPU utilisation. Each point  must contain one or more fields .
*   tags : Metadata about the data being recorded, e.g the hostname of the device whose CPU is being monitored. Each point  can contain zero or more tags .

_(Note that both the fields and tags can be thought of as columns in the database table. We'll see why in a moment.)_

Let's now see what fields and tags are in the telegraf  database.

**Field Keys:**

```
> show field keys
name: cpu
fieldKey         fieldType
{% raw %}--------         ---------{% endraw %}
usage_guest      float
usage_guest_nice float
usage_idle       float
usage_iowait     float
usage_irq        float
usage_nice       float
usage_softirq    float
usage_steal      float
usage_system     float
usage_user       float

name: disk
fieldKey     fieldType
{% raw %}--------     ---------{% endraw %}
free         integer
inodes_free  integer
inodes_total integer
inodes_used  integer
total        integer
used         integer
used_percent float

name: diskio
fieldKey         fieldType
{% raw %}--------         ---------{% endraw %}
io_time          integer
iops_in_progress integer
read_bytes       integer
read_time        integer
reads            integer
write_bytes      integer
write_time       integer
writes           integer

name: kernel
fieldKey         fieldType
{% raw %}--------         ---------{% endraw %}
boot_time        integer
context_switches integer
interrupts       integer
processes_forked integer

name: mem
fieldKey          fieldType
{% raw %}--------          ---------{% endraw %}
active            integer
available         integer
available_percent float
buffered          integer
cached            integer
free              integer
inactive          integer
total             integer
used              integer
used_percent      float

name: processes
fieldKey      fieldType
{% raw %}--------      ---------{% endraw %}
blocked       integer
dead          integer
paging        integer
running       integer
sleeping      integer
stopped       integer
total         integer
total_threads integer
unknown       integer
zombies       integer

name: swap
fieldKey     fieldType
{% raw %}--------     ---------{% endraw %}
free         integer
in           integer
out          integer
total        integer
used         integer
used_percent float

name: system
fieldKey      fieldType
{% raw %}--------      ---------{% endraw %}
load1         float
load15        float
load5         float
n_cpus        integer
n_users       integer
uptime        integer
uptime_format string
```

**Tag Keys:**

```
> show tag keys
name: cpu
tagKey
{% raw %}------{% endraw %}
cpu
host

name: disk
tagKey
{% raw %}------{% endraw %}
device
fstype
host
path

name: diskio
tagKey
{% raw %}------{% endraw %}
host
name

name: kernel
tagKey
{% raw %}------{% endraw %}
host

name: mem
tagKey
{% raw %}------{% endraw %}
host

name: processes
tagKey
{% raw %}------{% endraw %}
host

name: swap
tagKey
{% raw %}------{% endraw %}
host

name: system
tagKey
{% raw %}------{% endraw %}
host
```

## InfluxDB Queries

Let's now see how fields and tags work together. Note that for the remainder of this post we'll focus on the cpu  **measurement**:

```
> select * from cpu where time > now() - 30s
name: cpu
time                cpu       host   usage_guest usage_guest_nice usage_idle        usage_iowait        usage_irq usage_nice usage_softirq        usage_steal usage_system        usage_user
{% raw %}----                ---       ----   ----------- ---------------- ----------        ------------        --------- ---------- -------------        ----------- ------------        ----------{% endraw %}
1497018760000000000 cpu-total ubuntu 0           0                99.297893681046   0                   0         0          0                    0           0.35105315947842414 0.35105315947842414
1497018760000000000 cpu1      ubuntu 0           0                99.69909729188728 0                   0         0          0                    0           0.20060180541622202 0.10030090270811101
1497018760000000000 cpu0      ubuntu 0           0                98.99497487436828 0                   0         0          0                    0           0.5025125628140764  0.502512562814005
1497018770000000000 cpu1      ubuntu 0           0                99.39879759517734 0.1002004008015872  0         0          0                    0           0.5010020040079893  0
1497018770000000000 cpu0      ubuntu 0           0                98.99497487436828 0                   0         0          0                    0           0.40201005025121833 0.6030150753768632
1497018770000000000 cpu-total ubuntu 0           0                99.19719016558027 0.05017561465127768 0         0          0                    0           0.4515805318615258  0.3010536879076839
1497018780000000000 cpu0      ubuntu 0           0                98.79396984925634 0                   0         0          0                    0           0.502512562814097   0.7035175879397785
1497018780000000000 cpu-total ubuntu 0           0                99.04761904762564 0                   0         0          0.050125313283205124 0           0.5513784461152652  0.3508771929824804
1497018780000000000 cpu1      ubuntu 0           0                99.59879638917697 0                   0         0          0                    0           0.40120361083251527 0
```

With any luck this output should show you the difference between fields  and tags .  Given that the field  columns have varying data (e.g usage_idle  and usage_system), we know that they're storing data. On the other hand, we can see that the tag  columns (e.g cpu and host ) have static information (e.g cpu0 , cpu1 , ubuntu ).

To really hit the point home, let's look at an example. Let's say we want to see only the ubuntu  host's cpu-total  information. We'll need to utilise those two tags  in order to see the fields  (data), like so:

```
> select * from cpu where cpu='cpu-total' and host='ubuntu' and time > now() - 30s
name: cpu
time                cpu       host   usage_guest usage_guest_nice usage_idle        usage_iowait         usage_irq usage_nice usage_softirq       usage_steal usage_system        usage_user
{% raw %}----                ---       ----   ----------- ---------------- ----------        ------------         --------- ---------- -------------       ----------- ------------        ----------{% endraw %}
1497019920000000000 cpu-total ubuntu 0           0                99.24623115578979 0                    0         0          0                   0           0.40201005025129183 0.35175879396985354
1497019930000000000 cpu-total ubuntu 0           0                99.29577464789028 0                    0         0          0.0503018108651875  0           0.40241448692157145 0.25150905432592857
1497019940000000000 cpu-total ubuntu 0           0                99.04809619237324 0.050100200400798045 0         0          0.05010020040079582 0           0.4008016032063488  0.4509018036071691

Given that the fields are variable, it would be impossible to use them to obtain the above information which is why we use the static tags  instead.

Now that you know the difference between the two, let's filter all output but the usage_user  fields :

\> select usage_user,cpu,host from cpu where cpu='cpu-total' and host='ubuntu' and time > now() - 30s
name: cpu
time                usage_user         cpu       host
{% raw %}----                ----------         ---       ----{% endraw %}
1497020190000000000 0.5032712632108679 cpu-total ubuntu
1497020200000000000 0.3514056224899564 cpu-total ubuntu
1497020210000000000 0.3016591251885572 cpu-total ubuntu
```

Note that I also included the cpu and host tags . The reason I did this is because without them, we wouldn't know which cpu and host the usage_user data pertained to.

One other thing to note is that you must always specify at least one field , while tags are optional. This is because fields  contain data and tags  are metadata. Therefore the former can provide value when used in isolation whereas the latter does not.

## Keys & Values

Now that you've got an understanding of  fields and tags, it's time to point out the difference between field keys and field values and between tag keys and tag values.

If you were to think of the above databases as spreadsheets, the top row consists of field keys and tag keys. Every other "cell" contains field values and tag values .

For example, in the output above usage_user is a field key while cpu and host are tag keys. Further to this, every value in those columns are field values and tag values respectively.

## Key Set

A collection of field key and field value pairs on a point is known as a field set.

## Tag Set

A collection of tag keys and tag value pairs on a point is known as a tag set.

## Series

As per the [**InfluxDB documentation**](https://docs.influxdata.com/influxdb/v1.2/concepts/glossary/#series), a Series is a _"collection of data in InfluxDB’s data structure that share a measurement, tag set, and retention policy."_

## Indexing

While on the topic of tags and keys, something you should keep at the front of your mind is that the former is indexed while the latter is not. As a result, queries run against tags perform much better than those which are performed against keys. As described in the [**InfluxDB documentation**](https://docs.influxdata.com/influxdb/v1.2/concepts/key_concepts/), it can be worthwhile redesigning your database to ensure that it runs optimally.

## Retention & Shard Groups

I was going include Retention Policies and Shard Groups in this post but I've got so much to say about them I ended up [**putting them in their own post**](/influxdb-retention-policies-shard-groups/).

## Glossary

If you see a term you're unfamiliar with, it's highly like you will find its definition in the  [**Glossary**](https://docs.influxdata.com/influxdb/v1.2/concepts/glossary/).

## Schema Design

General Recommendations, Encouraged Schema Design, Discouraged Schema Design, and Shard Group Duration Management are all covered in the [**Schema Design section of the InfluxDB documentation**](https://docs.influxdata.com/influxdb/v1.2/concepts/schema_and_data_layout/) and is well worth a read before you start using InfluxDB in a production environment.