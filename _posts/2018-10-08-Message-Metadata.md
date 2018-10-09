---
layout: post
title: Message Metadata
ltitle: Message Metadata
stitle: Tracking Metadata on Messages
date:   2018-10-08
author: Jasdeep Jaitla
---

When creating a feed style chat or a chat similar to modern social media, the need for not just 
sending a message but also to add metadata at a later time to that message requires thinking 
about a new pattern. With PubNub, since we are fairly immutable in design, how do you add these features?

![Basic]({{ site.baseurl }}/images/metadata/metadata_001.jpeg)

In this diagram, the message (and media) are already published and already stored. How do you track the likes and comments
on these messages? The key is in using PubNub's serverless compute Functions platform. With the KV Store and Functions 
REST endpoints, we are able to capture metadata store it and maintain the state of the metadata in the clients.

## Channel Structure ##

![Basic]({{ site.baseurl }}/images/metadata/metadata_001b.jpeg)


## Adding Message-Click Metadata ##

The first thing is that every message must have a unique ID. This allows for tracking of the metadata for each message
published. When a user clicks on the like/heart, it fires off a REST request to the Functions REST endpoint
you have set up to either INCR or DECR the counter for that unique ID, in this case we call it msgID. 

{% highlight javascript linenos %}
{
   msgID: "b7c4fb4b-b510-4cfa-a757-3282cc100f04",
   author: "author-1234",
   message: "http://s3.mydomain.com/9228b1d8-71bc-499a-8654-0a3901a06eef.jpg",
   timestamp: 1539019415
}
{% endhighlight %}

In the onclick event, or in mobile it would be a tap event, you will fire a REST call to PubNub Functions REST
endpoint to track that added like/heart...

Payload sent to PubNub Functions REST endpoint: 

{% highlight javascript linenos %}
{
   msgID: "b7c4fb4b-b510-4cfa-a757-3282cc100f04",
   liked: 1 
}
{% endhighlight %}

![Basic]({{ site.baseurl }}/images/metadata/metadata_002.jpeg)

In turn after increasing the count for that metadata data point, it will publish out the results so that everyone
subscribed will receive the update, and when catching up through Storage the users can stay updated.

The publishing out can be controlled based on date/time of the original message or other mechanisms, for instance,
you may decide only to publish out new results if it's within a certain time period. 

## History, Scrolling & On Demand ##

When scrolling up/down through history, getting the latest metadata on demand is also valuable. This allows for 
subscribers to get the latest data on older messages and posts individually. Creating a Functions REST endpoint for 
metadata retrieval on a msgID allows for this type of scenario. 

![Basic]({{ site.baseurl }}/images/metadata/metadata_003.jpeg)


![Basic]({{ site.baseurl }}/images/metadata/metadata_004.jpeg)

