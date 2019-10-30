---
title: 'InfluxDB: Retention Policies & Shard Groups'
sub_heading: "Diving deep with Influx"
redirect_from: /influxdb-retention-policies-shard-groups/

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
_Note: This is a continuation of the [**Getting to know InfluxDB**](/getting-know-influxdb/) post. If you haven't read it yet, I suggest you do before reading this post._

I found InfluxDB's documentation around **Retention Policies (RP)** and **Shard Groups** quite unclear in parts and am therefore writing this post to assist others who find themselves feeling the same way.

## What is a Retention Policy?

As the [**documentation**](https://docs.influxdata.com/influxdb/v1.2/concepts/glossary/#retention-policy-rp) says:

_**The part of InfluxDB’s data structure that describes for how long InfluxDB keeps data (duration**), how many copies of those data are stored in the cluster (replication factor), and the time range covered by **shard groups** (**shard group duration**). **RPs** are unique per database and along with the measurement and tag set define a series._

_When you create a database, InfluxDB automatically creates a retention policy called_ autogen_ with an **infinite duration**, a replication factor set to one, and a **shard group duration set to seven days.** See Database Management for retention policy management._

**Summary**

**RPs** define for how long data is kept. The default  autogen **RP** is set to **infinite** while the default **shard group duration** (which is part of the autogen  **RP**), is set to **seven days**.

It was at this point I found myself getting confused. What is the difference between a **RP** **duration** and a **Shard Group** **duration**? And how can you have an expiry date on data which is configured to be kept infinitely? More on this later.

## Shards, Shard Duration & Shard Groups

Below is what the [**documentation**](https://docs.influxdata.com/influxdb/v1.2/concepts/glossary/#shard) has to say about **Shards**:

### Shard

_A **shard** contains the actual encoded and compressed data, and is represented by a TSM file on disk. Every shard belongs to one and only one **shard group**. **Multiple shards** may exist in a single **shard group**. Each shard contains a specific set of series. All points falling on a given series in a given **shard group** will be stored in the same shard (TSM file) on disk._

### Shard Groups

_**Shard groups** are logical containers for **shards**. **Shard groups** are organized by time and **retention policy**. Every **retention policy** that contains data has at least one associated **shard group**. A given **shard group** contains all **shards** with data for the interval covered by the **shard group**. The interval spanned by each shard group is the **shard duration**._

### Shard Duration

_The **shard duration** determines how much time each **shard group** spans. The specific interval is determined by the_ SHARD DURATION_ of the **retention policy**. See **Retention Policy** management for more information._

_For example, given a **retention policy** with_ SHARD DURATION_ set to_ 1w_, each **shard group** will span a single week and contain all points with timestamps in that week._

### Summary

If you find the above documentation a little unclear, I find [**Michael Desa's (InfluxDB) StackOverflow post**](https://stackoverflow.com/a/39900833/6233477) to be very succinct.

To summarise the documentation:

*   **RPs** specify how much data should be available to you at any one time. e.g if you set its **duration** to one year, you'll store one year's worth of data.
*   **Shards** contain the actual data.
*   **Shard Groups** are logical containers for **shards**.
*   **Shard duration** specifies interval at which a **Shard Group** will expire (drop) from the database.

Note that **Shard Groups** will only expire after there's enough data in the database to meet the **RP's Duration **needs, as we'll see later on in this post.

## autogen

The last sentence of the previous section is related to my confusion mentioned earlier in this post. autogen has a **RP duration** of infinity, therefore it should mean that the **Shard duration** of **seven days** never kicks in, but it does.

Let's jump onto the database and see what we can find out:

```
> show retention policies
name    duration shardGroupDuration replicaN default
{% raw %}----    -------- ------------------ -------- -------{% endraw %}
autogen 0s       168h0m0s           1        true
```

Here we can see that the **RP's **duration is set to 0 (infinite). We also see that the **Shard duration** of seven days (168h). This doesn't prove that the **Shard Group duration** is being used though, so let's dig a little further:

```
> show shard groups
name: shard groups
id database  retention_policy start_time           end_time             expiry_time
{% raw %}-- --------  ---------------- ----------           --------             -----------{% endraw %}
2  telegraf  autogen          2017-06-05T00:00:00Z 2017-06-12T00:00:00Z 2017-06-12T00:00:00Z
```

And there we have it! The data started recording on 2017-06-05 and is due to expire (drop from the database) on 2017-06-12, seven days later. Therefore it is my opinion that the **RP's** name **"infinite"** is misleading because the data is not being stored infinitely at all - it's simply matching the **Shard Group's duration**.

## Finite RPs

Earlier I mentioned that _"**Shard Groups** will only expire after there's enough data in the database to meet the **RP's duration **needs"._ Let's now explore what I was talking about.

Let's start by creating a new database and do the following:

1.  Change the default autogen **RP's duration** from INF  to 2w (two weeks).
2.  Specify the **Shard Group duration** as 1w (one week).
3.  Insert data into our new database.

_Note that if a Shard Group duration is not specified, one will be configured automatically as per [**this section of the InfluxDB documentation**](https://docs.influxdata.com/influxdb/v1.2/troubleshooting/frequently-asked-questions/#what-is-the-relationship-between-shard-group-durations-and-retention-policies)._

```
CREATE DATABASE test_db
USE test_db
ALTER RETENTION POLICY autogen ON test_db DURATION 2w REPLICATION 1 SHARD DURATION 1w
INSERT INTO autogen measure1 value=0
```

Now that our database is up and running, let's take a look at the **RP**:

```
> show retention policies
name    duration shardGroupDuration replicaN default
{% raw %}----    -------- ------------------ -------- -------{% endraw %}
autogen 336h0m0s 168h0m0s           1        true
```

We can see that the **RP** has a **duration** of 336h now, as opposed to 0 (INF) which we saw earlier.

Next let's take a look at the **Shard Groups**:

```
> show shard groups
name: shard groups
id database  retention_policy start_time           end_time             expiry_time
{% raw %}-- --------  ---------------- ----------           --------             -----------{% endraw %}
16 test_db   autogen          2017-06-05T00:00:00Z 2017-06-12T00:00:00Z 2017-06-26T00:00:00Z
```

What we see here is that:

*   The current **Shard Group** is storing data from 2017-06-05.
*   The **Shard Group** is due to stop storing data one week later (in line with the **Shard Duration**) on 2017-06-12.
*   The Shard Group is due to expire (be removed from the database) on 2017-06-26, which is two weeks after the **Shard Group** stopped collecting data (in line with the RP's **Duration**).

By keeping the **Shard Group** for two weeks after it has stopped collecting data, InfluxDB is able to meet the **RP Duration's** needs of two weeks. If the **Shard Group** were dropped any earlier, we'd have less than two weeks worth of data in the database.

_For **Shard Group Duration** recommendations, see [**this section of the InfluxDB documentation**](https://docs.influxdata.com/influxdb/v1.2/concepts/schema_and_data_layout/#shard-group-duration-management)._

## Other Bits & Pieces

If you're looking to play around with your databases **RPs** (in a lab environment of course!), take a look at [**this page**](https://docs.influxdata.com/influxdb/v1.2/query_language/database_management/#create-retention-policies-with-create-retention-policy).

If you're dissecting your InfluxDB database and believe you're seeing strange results in regards to your **Retention Policies**, take a look at [**this section**](https://docs.influxdata.com/influxdb/v1.2/troubleshooting/frequently-asked-questions/#why-aren-t-data-dropped-after-i-ve-altered-a-retention-policy) of the FAQ.

The [**Durations**](https://docs.influxdata.com/influxdb/v1.2/query_language/spec/#durations) section of the InfluxDB documentation is quite handy.

## Conclusion

Although the autogen **RP** has an **infinite duration**, it doesn't actually retain data infinitely. Instead the **RP** component is effectively disabled and the data's retention is determined by the **Shard Group duration**.

As per the **[InfluxDB documentation](https://docs.influxdata.com/influxdb/v1.2/concepts/schema_and_data_layout/#shard-group-duration-overview)**, anything with an **RP duration** of above 6 months has a **Shard Group duration** set to seven days by default. As autogen has an infinite **RP duration**, data is stored for seven days unless the user specifies a different **Shard Group duration**.

With this in mind, if you want to retain data for more than 7 days you have to either create a new **RP** or modify autogen.