---
date: "2024-11-30T21:21:00.00Z"
published: true
slug: FactDataModelling
tags:
  - Programming
  - coding
  - data modelling
  - data expert
  - data engineering
time_to_read: 5
title: Fact Data Modelling
description: Fact data is the biggest data in data engineering, Fact data is every event an user can do. At Meta a user can do 2000-3000 events per day.
type: post
---

What is a fact ?

Think of fact as something that happened or occured.

- A user logs into an app
- A transaction is made
- You run a mile in fitbit

Facts are not slowly changing which makes them easier to model than dimensions in some respects

You cant change the past and the past data is usually factual.

Fact data is usually 10-100x the volume of the dimension data

- Facebook had 2B active users and 50B notifications every day

Fact data can need a lot of context for effective analysis

Duplicates in facts are way more common than in dimensional data

**Normalization vs Denormalization**

- Normalized facts dont have any dimensional attributes, just ids to join to get that information
- Denormalization facts bring in some dimensional attributes for quicker analysis at the cost of more storage.

- Both normalized and denormalized facts have a place in this world!

How does fact modelling work ?

`Raw logs`

- Ugly schemas designed for online systems that make data analysis sad
- Potentially contains duplicates and other quality errors
- Usually have shorter retention

`Fact data`

- Nice column names
- Quality guarantees like uniqueness , not null etc
- longer retention

Think of this as who what where when and how

- `who` fields are usually pushed out as IDs (this user clicked this button, we only hold the user_id not the entire user object)
- `where` fields
  - Most likely modeled out like who with "IDs" to join, but more likely to bring in dimensions, especially if ther are high cardinality like "device_id"
- `How` fields
  - How fields are very similar to "Where" fields. "He used an iphone to make this click"
- `What` fileds are fundamentally part of the nature of the fact.
  - In notifications world - "Generated", "Sent", "Clicked", "Delivered"
- `When` fields are fundamentally part of the nature of the fact

  - Mostly an "event_timestamp" field or "event_date"

  Fact datasets should have quality guarantees

  - if it isnt , analysis would just go to the raw logs

  - Fact data should be generally smaller then raw logs

  - Fact data should parse out hard-to-understand columns

**When should you model in dimensions ?**

- Example

  - Network logs pipeline
  - The largest pipleline , over 100 TBs/hr
  - We wanted to see which microservice app each network request came from and went to
  - Modelling this as a traditional fact data
  - IP address would be the identifier for the app
  - This worked for IPv4 domains because the cardinality was small enough
  - This pipeline design couldnt be used to support IPV6 domains though because that search space was too large to be broadcasted

  - The slam dunk solution here was to log to the "app" field with each network request and getting rid of the join entirely. Denormalization saves the day.
  - The required each microservice to adopt a "sidecar proxy" that enabled logging of which app they are
  - A large organizational effort to solve this issue

  How does logging fit into fact data ?

  - Logging brings in all critical context for your fact data
    - Usually done in colloboration with online system engineeers
  - Dont log everything
    - Log only what you really need
  - Logging should conform to values specified by the online teams
    - Thrift is what is used Airbnb and Netflix for this

**Potential options when working with high volume fact data**

- Sampling
  - Doesnt work for all use cases, works best for metric-driven use-cases where imprecision isn't an issue
- Bucketing
  - Fact data can be bucketed by one of the import dimensions(usually user)
  - Bucket joins can be much faster than shuffle joins
  - Sorted-merge bucket (SMB) joins can do joins without shuffle at all

**How long should you hold onto fact data ?**

- High volumes make fact data much more costly to hold onto for a long time
- Big tech had an interesting approach here
  - Any fact tables < 10TBs, Retention didnt matter much
  - Anonymization of facts usually happened after 60-90 days though and the data would be moved to a new table with PII striped
  - Any fact tables > 100 TBs , very short retention (~14 days or less)

**Deduplication of fact data**

- Facts can often be duplicated
  - You can click a notification multiple times
- How do you pick the right window for deduplication
  - No duplicates in a day ? An hour ? A week ?
  - Looking at distributions of duplicates here is a good idea
- Intraday deduping options

  - Streaming
  - Microbatch

  **Streaming to deduplicate facts**

  Streaming allows you to capture most duplicates in a very efficient manner

  - Windowing matters here
  - Entire day duplicates can be harder for streaming because it needs to hold onto such a big window of memory
  - A large memory of duplicates usually happen within a short time of first event
  - 15 minture to hourly windows are a sweet spot

  **Hourly Microbatch dedupe**

  - Used to reduce landing time of daily tables that dedupe slowly
  - Worked at facebook using this pattern

    - Deduplicated 50 billion notification evetns every day
    - Reduced landing time from 9 hours after UTC to 1 hour after UTC
    - Dedupe hourly and then dedupe between hours

**The blury line between fact and dimension**

Is it a fact or a dimension

- Did a user log in today
  - The log in event would be a fact that informs the `dim_is_active` dimension
  - VS the state `dim_is_activated` which is something that is state-driven, not activity driven
- You can aggregate facts and turn them into dimensions
  - Is this person a 'high engager' ? A 'low engager'
    - Think scoring class from cumultative table design
  - Case when to bucketize aggregated facts can be very useful to reduce the cardinality

**Properties of facts vs dimensions**

- Dimensions
  - Usually show up in Group by when doing analytics
  - Can be "high cardinality" or "low cardinality" depending
  - Generally come from a snapshot of state
- Facts
  - Usually aggregated when doing analytics by things like SUM, AVG, COUNT
  - Almost always higher volumn than dimensions , although some fact sources are low-volume, think "rare events"
  - Generally come from events and logs

**Airbnb Example**

Is the price of a night on Airbnb a fact or a dimension ?

The host can set the prices which sounds like an event .

It can easily be SUM, AVG, COUNt'd like regular facts

Prices on Airbnb are doubles, therefore extremely high cardinality

The fact in this case would be host changing the setting that impacted the price

Think a fact hast to be logged, a dimension comes from the state of things

Price being derived from settings is a dimension

**Boolean / Existence-based Fact/Dimensions**

- dim_active, dim_bought_something etc
  - these are usually on the daily/hour grain too
- dim_has_ever_booked, dim_ever_active, dim_ever_labeled_Fake
  - These 'ever' dimensions look to see if there has ever been a log and once it flips one way, it never goes back
  - Interesting , simple and powerful features for maching learning
    - An Airbnb host with active listings who has never been booked
      - Looks sketchier and sketchier over time
- "Days since" dimensions (e.g days_since_last_active,days_sinces_signup , etc)
  - Very common in retention analytical patterns
  - Look up J curves

**Categorical Fact/Dimensions**

- Scoring class in week 1

  - A dimension that is derived from fact data

- Often calculated with chase when logic and bucketizing
  - Example : Airbnb Superhost

**Should you use dimensions or facts to analyze users ?**

- Is the "dim_is_activated" state or "dim_is_active" logs a better metrics ?
  - Great data science question because it depends
- Its the difference between "signups" and "growth" in some perspectives

**The Extremely efficient Date List data structure**

- Go and read Max Sungs write up.
- Extremely efficient way to manage user growth
- Imagine a cumulated schema like 'users_cumulated'
  - user_id
  - Date
  - Dates_active - an array of all the recent days that a user was active
- You can turn that into a structure like this
  - user_id, date, date_list_int
  - 32, 2023-01-01, 100000010000001
  - Where the 1's in the integer represent the activity for 2023-01-01 - bit_position (zero indexed)

**Why should shuffle be minimized**

- Big data leverages parallelism as much as it can
- Some steps are inherently less parallelizable than others

what types of queries are highly parallelizable

- Extremely parallel
  - Select, from , where
- Kinda parallel
  - group by, join, having
- painfully not parallel
  - order by

How do you make Group By more efficient ?

- Give GROUP BY some buckets and guarantees
- Reduct the data volume as much as you can

How reduced fact data modeling gives you superpowers

- Fact data often has this schema
  - user_id, event_time, action, date_partition
  - Very high volume, 1 row per event
- Daily aggregate often has this schema
  - user_id, action_cnt, date_partition
  - medium sized volume, 1 row per user per day
- Reduced fact take this one step further
  - user_id, action_cnt_array, month_start_position/year_start_partition
  - Low volume, 1 row per user per month/year

Example of fact data

<table>
    <tr>
            <th>user_id</th>
            <th>event_time</th>
            <th>action</th>
            <th>date</th>
            <th>other_properties</th>
    </tr>
    <tr>
        <td>3</td>
        <td>2023-07-08T11:00:31Z</td>
        <td>like</td>
        <td>2023-07-08</td>
        <td>{"os":"Android", "post":1414}</td>
    </tr>
    <tr>
        <td>3</td>
        <td>2023-07-09T09:33:31Z</td>
        <td>comment</td>
        <td>2023-07-09</td>
        <td>{"os":"iphone", "post":111}</td>
    </tr>

</table>

Example of Daily Aggregate

<table>
    <tr>
            <th>user_id</th>
            <th>metric_name</th>
            <th>date</th>
            <th>value</th>
    </tr>
    <tr>
        <td>3</td>
        <td>likes_given</td>
        <td>2023-07-08</td>
        <td>34</td>
    </tr>
    <tr>
        <td>3</td>
        <td>likes_given</td>
        <td>2023-07-09</td>
        <td>1</td>
    </tr>
    <tr>
        <td>3</td>
        <td>likes_given</td>
        <td>2023-07-10</td>
        <td>3</td>
    </tr>

</table>

Example of long array metrics

<table>
    <tr>
            <th>user_id</th>
            <th>metric_name</th>
            <th>month_start</th>
            <th>value_array</th>
    </tr>
    <tr>
        <td>3</td>
        <td>likes_given</td>
        <td>2023-07-01</td>
        <td>[34, 3, 3, 4, 5, 6, 7, 7, 3, 3, 4, 2, 1, 5 , 6, 3, 2, 1, 5, 2, 3, 3, 4, 5, 7, 8, 3, 4, 9]</td>
    </tr>
    <tr>
        <td>3</td>
        <td>likes_given</td>
        <td>2023-08-01</td>
        <td>[8, 3, 4, 5, 0, 7 , 0 , 3, 3, 4, 2, 1, 5, 6, 3, 2, 1, 5, 2, 3, 3, 4, 5, 6, 8, 3, 4, 9]</td>
    </tr>
    <tr>
        <td>3</td>
        <td>likes_given</td>
        <td>2023-09-01</td>
        <td>[17, 3, 3, 4, 5, 6, 7, 7, 3, 3, 4, 2, 1, 5, 6, 3, 2, 1, 5, 2, 3, 3, 4, 5, 7, 8, 3, 4]</td>
    </tr>

</table>

How reduced fact data modelling gives you superpowers

- The daily dates are stored as an offset of month_start/year_start

  - First index is for date month_start+zero days
  - Last index is for date month_start + array_length - 1

- Dimensional joins get weird if you things to stay peformant
  - Your scd accuracy becomes the same as month_start or year_star
    - You give up 100% accurate scd tracking for massively increased performance
  - You need to pick snapshots in time (month start or month end or both) and treat the dimensions as fixed

Impact

- Impact of analysis
  - Multi-year analyses took hours instead of weeks
  - Unlocked "decades-long slow burn" analyses at facebook
- Allowed for fast correlation analysis between user-level metrics and dimensions
