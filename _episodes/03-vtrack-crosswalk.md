---
title: "Glatos to VTrack Crosswalk"
teaching: 15
exercises: 0
questions:
- "How do I use my GLATOS data in VTrack?"
objectives:
- "Demonstrate GLATOS' crosswalk to VTrack function"
keypoints:
- "GLATOS provides a method that outputs VTrack's ATT data format"
---

As of version 0.4.0 of the `glatos` package, we can crosswalk the glatos object to the [VTrack](https://github.com/RossDwyer/VTrack)'s ATT format. We can use the glatos detection and receiver data to output the ATT format.


~~~
library(glatos)
library(VTrack)

wal_det_file <- system.file("extdata", "walleye_detections.csv",
      package = "glatos")
walleye_detections <- read_glatos_detections(wal_det_file) # load walleye data

rec_file <- system.file("extdata", "sample_receivers.csv",
      package = "glatos")
rcv <- read_glatos_receivers(rec_file) # load receiver data

ATTdata <- convert_glatos_to_att(walleye_detections, rcv)

~~~
{:.language-r}

With our new `ATTdata` object, we can use the `VTrack` package's functions.


~~~
abacusPlot(ATTdata)

abacusPlot(ATTdata, 
           id = c(22, 23),
           facet=TRUE)

dispSum<-dispersalSummary(ATTdata)
~~~
{:.language-r}
