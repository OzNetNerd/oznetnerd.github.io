---
title: Navigating InfluxDB CLI
sub_heading: "The CLI is your friend"
redirect_from: /navigating-influxdb-cli/

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
I've demonstrated a few InfluxDB commands in my [**Getting to know InfluxDB**](/getting-know-influxdb/) and [**InfluxDB: Retention Policies & Shard Groups**](/influxdb-retention-policies-shard-groups/) posts but though it would be a good idea to write a post completely dedicated to useful CLI commands - so here it is.

## SHOW DATABASES

This command is self explanatory. It lists all of your InfluxDB databases:

```
{% raw %}> SHOW DATABASES
name: databases
name
----
telegraf
_internal{% endraw %}
```

## USE

Enters a database so that subsequent commands will be run against it:

```
{% raw %}> USE telegraf
Using database telegraf{% endraw %}
```

## SHOW MEASUREMENTS

Gets a list of `measurements` that reside in a database:

```
{% raw %}> SHOW MEASUREMENTS
name: measurements
name
----
cpu
disk
diskio
kernel
mem
processes
snmp
swap
system{% endraw %}
```

## CLAUSES

Clauses are extremely useful when you're looking for specific information. You can read more about them in InfluxDB's [**Data Exploration documentation**](https://docs.influxdata.com/influxdb/v1.2/query_language/data_exploration/#the-basic-select-statement). Some will be used in the subsequent sections of this post.

## SHOW FIELD KEYS

Lists the data that is being recorded in each `measurement`:

```
{% raw %}> SHOW TAG KEYS FROM snmp,cpu
name: cpu
tagKey
------
cpu
host

name: snmp
tagKey
------
agent_host
host
hostname
ifName{% endraw %}
```

## SHOW TAG KEYS

Lists the the tags which are being used in each `measurement`:

```
{% raw %}> SHOW TAG KEYS FROM snmp
name: snmp
tagKey
------
agent_host
host
hostname
ifName{% endraw %}
```

## SHOW TAG VALUES

View tags values:

```
{% raw %}> SHOW TAG VALUES FROM snmp WITH KEY = "hostname"
name: snmp
key      value
---      -----
hostname R1
hostname R2{% endraw %}
```

## SHOW SERIES

Lists all series in the database (`measurement`, `tag keys` and `tag values`):

```
{% raw %}> SHOW SERIES FROM snmp,cpu
key
---
cpu,cpu=cpu-total,host=ubuntu
cpu,cpu=cpu0,host=ubuntu
cpu,cpu=cpu1,host=ubuntu
snmp,agent_host=192.168.0.242,host=ubuntu,hostname=R1,ifName=Fa0/0
snmp,agent_host=192.168.0.242,host=ubuntu,hostname=R1,ifName=Fa0/1
snmp,agent_host=192.168.0.242,host=ubuntu,hostname=R1,ifName=Nu0
snmp,agent_host=192.168.0.243,host=ubuntu,hostname=R2,ifName=Fa0/0
snmp,agent_host=192.168.0.243,host=ubuntu,hostname=R2,ifName=Fa0/1
snmp,agent_host=192.168.0.243,host=ubuntu,hostname=R2,ifName=Nu0{% endraw %}
```

For example, in the above output `snmp` is a **measurement**,`agent_host`, `host`, hostname and ifName are all **key tags** and `192.168.0.242`, `ubuntu`, `R2` and `Fa0/1` are all **key values**.

## SELECT

Displays information that matches your query. Note that both `Field Keys` and `Tag Keys` are used in the query below:

```
{% raw %}> select hostname,ifHCOutOctets,ifHCInOctets from snmp where ifName='Fa0/0' and time > now() - 30s
name: snmp
time                hostname ifHCOutOctets ifHCInOctets
----                -------- ------------- ------------
1497178820000000000 R1       12390993      6913565
1497178820000000000 R2       12015097      6785158
1497178830000000000 R1       12396313      6916122
1497178830000000000 R2       12020416      6787715
1497178840000000000 R1       12402045      6919251{% endraw %}
```

## Further Reading

The InfluxDB's [**Schema Exploration documentation**](https://docs.influxdata.com/influxdb/v1.2/query_language/schema_exploration/) has numerous commands and outputs which are worth taking a look at. The [**Influx Query Language documentation**](https://docs.influxdata.com/influxdb/v1.2/query_language/spec/#dates-times) is also well worth a read.