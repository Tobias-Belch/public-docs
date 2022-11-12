---
tags: source/todo
---

# [Just store UTC? Not so fast! Handling Time zones is complicated.](https://codeopinion.com/just-store-utc-not-so-fast-handling-time-zones-is-complicated/)

> ***Sponsor:** Do you build complex software systems? See how NServiceBus makes it easier to design, build, and manage software systems that use message queues to achieve loose coupling. [Get started for free](https://go.particular.net/codeopinion).*

Should you store dates & times in your database as UTC? It’s pretty standard advice if you’re working in a system that needs to record dates and times from many different time zones. But this advice doesn’t hold true when dealing with dates and times in the future; here are some things you need to consider.

## YouTube

Check out my [YouTube channel](https://www.youtube.com/channel/UC3RKA4vunFAfrfxiJhPEplw), where I post all kinds of content accompanying my posts, including this video showing everything in this post.

You’ll hear/read pretty standard advice to store all dates/times as UTC when storing dates/times in a database. As users enter data into your system, you might convert their specific local date time to UTC and then save that in your database.

For example, say you have a web application where the user must enter a date/time value. They would be specifying it in their local time. This date/time in ISO format would be sent to your Server/API as 2022-08-02T18:00:00-400. This is their local date time with their current time zone offset. Your app would then convert this to UTC, which would be 2022-08-02T22:00:00Z

 [![](https://codeopinion.com/wp-content/uploads/2022/11/1-1024x190.png)](https://codeopinion.com/wp-content/uploads/2022/11/1.png)

Then when we need to return data to the client, we fetch it out of the database, which is in UTC, and send that UTC date/time to the client, where it can then convert it to its local date/time.

Sounds straightforward, right?

There are two reasons why I think this is the standard advice given on storing date/times. The first is because you’re standardizing your date/times in your database, which means you can query your data and compare it against the UTC. This allows you to sort, filter, etc., all on a standardized date/time. You won’t need any conversion at runtime to do any sorting and filtering.

The second reason I think this is standard advice is that people are only thinking about date/times that are in the past. And that’s where the most significant problem lies, date/times in the future.

## Time zones & Daylight Saving Time

A date/time in the future, converted to UTC as of right now, might change. How? Because Time Zone rules can change. Daylight Saving Time rules can also change.

 [![](https://codeopinion.com/wp-content/uploads/2022/11/image-1024x606.png)](https://codeopinion.com/wp-content/uploads/2022/11/image.png)

When you do a conversion from a local date/time as of right now, at this very moment, you’re applying the current timezone and daylight saving time rules. But that’s not to say these rules will always be like this. They change more than you think!

This means that when you convert a local date time that’s in the future to a UTC date/time and then save that to your database, you’re basing on the rules at the time of persistence, not what it would be in the future when that date/time is realized.

So what should you be saving in your database? Let’s walk through an example.

Here is an object with a future date/time with a time zone offset and a location in Toronto, Canada.

 [![Date Time offset](https://codeopinion.com/wp-content/uploads/2022/11/3.png)](https://codeopinion.com/wp-content/uploads/2022/11/3.png)

As mentioned, if the timezone rules or daylight saving time rules change, this could be incorrect as the time zone offset could be wrong. How would we correct or convert this date time to the correct value? How would we know what the correct value should be? You wouldn’t be able to, as you have no information about what time zone was used. All you have is the time zone offset.

If we were storing UTC instead, we still have the exact same problem. We cannot convert it to the correct time if there are any rule changes.

 [![UTC](https://codeopinion.com/wp-content/uploads/2022/11/4.png)](https://codeopinion.com/wp-content/uploads/2022/11/4.png)

## Time Zone Database

One solution is using the Time Zone Database. If you’re using a library for handling dates and times (eg, JodaTime, NodaTime), it is likely already using the Time Zone Database (tzdb) under the hood. The Time Zone Database contains all time zone boundaries, UTC offsets, and daylight-saving rules.

It also contains a time zone identifier (IANA) that you can use to reference any time you specify a local date. In your database, you can then persist this time zone identifier with your local date/time.

 [![IANA](https://codeopinion.com/wp-content/uploads/2022/11/5.png)](https://codeopinion.com/wp-content/uploads/2022/11/5.png)

You’ll notice the dateTime property doesn’t have the time zone offset anymore. It’s just the literal date/time the user specified. This is because now we have the IANA time zone name (America/Toronto) that relates to that literal date/time.

As mentioned earlier, the reason to standardize to UTC is so you can query your database effectively for sorting and filtering. We can still do this at the time of persistence in addition to the literal local date/time and time zone name.

 [![IANA with UTC](https://codeopinion.com/wp-content/uploads/2022/11/6.png)](https://codeopinion.com/wp-content/uploads/2022/11/6.png)

So as mentioned, time zone and daylight-saving time rules can change. So what happens if they do? Well, what we can also record is the version of the Time Zone Database. This way, we know what version and rules were used when we converted to UTC.

 [![TZDB Version](https://codeopinion.com/wp-content/uploads/2022/11/7.png)](https://codeopinion.com/wp-content/uploads/2022/11/7.png)

When rules change, the time zone database will get updated. We can query our database looking for future date/times where the tzdb is an older version and then redo the conversion of the dateTimeLocal using the IANA name to convert back to the correct UTC and update that value in our database.

## Just store UTC?

While the standard advice of “just store UTC” can work, it will only work if you’re storing date/time that is in the past. If you need to store date/time in the future, then you want to record the time zone name and the version of the tzdb. And for querying purposes, you’ll want to convert to UTC. And if there ever is a change to any rules, you can update the UTC date/times.

## Join!

Developer-level members of my [YouTube channel](https://www.youtube.com/channel/UC3RKA4vunFAfrfxiJhPEplw/join) or [Patreon](https://www.patreon.com/codeopinion) get access to a private Discord server to chat with other developers about Software Architecture and Design and access to source code for any working demo application I post on my blog or YouTube. Check out my [Patreon](https://www.patreon.com/codeopinion) or [YouTube Membership](https://www.youtube.com/channel/UC3RKA4vunFAfrfxiJhPEplw/join) for more info.

## You also might like

-   [Multi-tenant Architecture for SaaS](https://codeopinion.com/multi-tenant-architecture-for-saas/)
-   [SOLID? Nope, just Coupling and Cohesion](https://codeopinion.com/solid-nope-just-coupling-and-cohesion/)

[Follow @CodeOpinion on Twitter](https://twitter.com/codeopinion)