---
layout: post
title: "Fun Bugs I've Fixed #1: Odd Clusters"
tags: Python Bugs
---

I once worked on a highly distributed system, written in Python, to handle telemetry data reported back from thousands of network sensors.  This was before the days of ready-made firehose systems like Kafka or Storm, so this thing was built in-house and we had pulled all the stops and used all the tricks to get it running as bleeding fast as possible.  It sat there chugging along, processing anonymized reporting data and aggregated it for a clustering system downstream.

Now, it was running somewhat smoothly for months after initial deployment.  As data came in, clusters started forming in the downstream system -- as it should... But something wasn't quite right.  The clusters were too regular, the dimensions aligned too well, the efficacy of the final output was terrible.

It was a few more weeks of intense auditing the code, and the data, and the output, before I finally figured it out. To my shock and horror, there was a little gem in the code like this:

```python
def process_data(packet, processing_dict={}):
    for field in FIELDS:
        if packet.has_field(field):
            processing_dict[field] = packet.decode(field)
    handle_data(processing_dict)
```
        
A text-book gotcha in Python -- never use a mutable as a kwargs default! Most of time everything was fine because our telemetry packets had all the fields. But in the 10% of cases when a field was missing, we defaulted to the previous value.  That's why all of the clusters kind of looked correct, but not really.

So the old adage still holds: "Make it work, make it correct, make it fast, ..." We had just left out a step.