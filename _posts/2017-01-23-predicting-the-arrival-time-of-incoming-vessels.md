---
layout: post
title: DashPort - Predicting the arrival times of incoming vessels
categories: [hackathon]
tags: [dashport, hackathon, hackshipping]
description: Real data, real algorithms, built in a weekend
---

![dashport pic]({{ site.url }}/assets/media/dashport1.jpg)  

## NYC Ports and Logistics Hackathon
Team Atlas built DashPort – a berth management tool which allows Port Planners to optimise their berth plan ensuring no berths are missed. Wait wait wait, what’s a berth plan!? A berth plan is an Excel spreadsheet used by the port planners to specify where and when vessels will dock at the port to load and unload.  

Mike, our user persona and Port Planner has to manually update this spreadsheet if vessels are delayed. Not only is this annoying for Mike, 65% of planned berths are missed and missed berths cost $10,000 per hour!  

Mike is aware of this and tries to predict when each vessel will arrive. He is constantly phone calling and emailing captains, monitoring tidal and weather conditions, looking at the vessel’s AIS data (which is usually inaccurate) – all in all, a complete nightmare!

![dashport pitch]({{ site.url }}/assets/media/dashport2.jpg) 

Enter DashPort. DashPort uses historical journey data to and from specific ports to predict whether a vessel is expected to make its planned berthing slot. The key here is that DashPort knows how the vessel is performing at T-10, T-9, T-8… days before the vessel is due to arrive at the port. With an accurate prediction model, DashPort then provides a set of actions to Mike to account for certain delays. Mike can update the berth plan directly in the app, which allows him to easily account for delayed vessels.

![dashport screenshot]({{ site.url }}/assets/media/dashport3.jpg) 

For more details on the hackathon, please read our [DevPost submission](https://devpost.com/software/dashport)

## Prediction Tool

For our training set, we generated a dataset from historical AIS shipping data consisting of time, current location, destination location, distance to destination, estimated time to destination(AIS), speed and actual time of arrival.

![dashport shipdata]({{ site.url }}/assets/media/dashport4.jpg) 

![dashport shipdatacsv]({{ site.url }}/assets/media/dashport5.jpg)

Using this, we created a linear regression model in Python which aims to predict the actual time of arrival of a vessel. We then wrapped this in a flask application on Bluemix which can be queried as follows:

[http://dashport-predict.mybluemix.net/shipArrival/1462790000000/28.846888/30.749433/32.280201/33.334607/454.9419641/23/19.78](http://dashport-predict.mybluemix.net/shipArrival/1462790000000/28.846888/30.749433/32.280201/33.334607/454.9419641/23/19.78)

The parameters are structured like so:
```
base_url/shipArrival/time/source_lat/source_lng/dest_lat/dest_lng/distance_to_dest/speed/time_to_dest
```

A JSON response is returned from the model which indicates the expected time of arrival:

```(json)
{
    result: {
        datetime: "2016-05-09 16:35:56",
        epochTime: 1462811756559
    }
}
```
Note that this model has a very small and specific training set (we built it during the hackathon), so it won't work for all latitude/longitudes.


