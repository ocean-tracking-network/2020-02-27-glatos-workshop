---
title: "The GLATOS Package"
teaching: 30
exercises: 0
questions:
- "What is the GLATOS package?"
- "What are false detections?"
- "How can I filter false detections out of my data?"
- "What are some ways I can summarise my data?"
objectives:
- "Introduce the GLATOS package."
- "Explain how false detections are produced."
- "Introduce the Pincock algorithm."
- "Demonstrate how the Pincock algorithm can filter false detections."
- "Demonstrate methods for summarising data using the GLATOS package"
keypoints:
- "The GLATOS package is a collection of data analysis and visualization tools."
- "False detections are produced by tag collisions."
- "The Pincock algorithm filters out false detections by comparing pings across nearby stations."
- "The GLATOS package includes functions for summerise data"
---

## GLATOS
There's an ongoing effort to combine the work done by many researchers worldwide on the creation of these and other analysis and visualization tools so that work is not duplicated, and so that researchers don't have to start from scratch when implementing analysis techniques.

The Great Lakes Acoustic Telemetry Observing System group has gathered a few of their more programming-minded researchers and authored an R package, and invited OTN and some technical people at Innovasea (Vemco) to help them maintain and extend this package to ensure that it's useful for telemeters all over the world. There are a few very common methods of looking at acoustic detection data codified in glatos, and it serves as a great jumping off point for the design of new methods of analysis and visualization. The Pincock calculation above exists as a prebuilt function in the glatos toolbox, and there are a few others we'll peek at now to help us with the visualization of these datasets.

The notebook concept is a bit new to the glatos package, so be aware that its functions save most of their output to files. Those files will be in your project folder.

## False Detection Filtering using the Pincock algorithm.

Doug Pincock defined a temporal threshhold algorithm to determine whether detections in a set of detections from a station could be considered real ([Source](https://www.vemco.com/pdf/false_detections.pdf "Link to Pincock Paper")). The thrust of the filter is that a single detection at a station could very well be false, and it would require multiple detections of a tag by a receiver within a certain time frame to confirm that tag actually existed and was pinging at that station, and its ID was not the result of a collision event between two other tags.

Tag collision resulting in no detection:

Tag collision resulting in false detection:
(figs from False Detections: What They Are and How to Remove Them from Detection Data, Pincock 2012)

~~~
library(glatos)

dets_file <- system.file("extdata", "blue_shark_detections.csv",
                        package = "glatos")

dets <- read_otn_detections(dets_file)
dets <- min_lag(dets)
dets <- false_detections(dets, tf = 3600)
~~~
{:.language-r}

The result of false_detections() adds a passed_filter column to the table. We can use this column to get only the detections that passed the filter.

~~~
library(tidyverse)

dets_f <- dets %>% filter(passed_filter != FALSE)
~~~
{:.language-r}


## Summarising Detections

We can use the glatos function `summarize_detections` to summarise where each animal was seen. We can use `detection_events` to group the detections in the events and make a summary from that. (An event is when an animal is detected at the same station multiple times within a time period, without being detected on another station) 

~~~
det_summary <- summarize_detections(dets, location_col = 'station')

events <- detection_events(dets_f, location_col = 'station')

event_summary <- events %>% 
  group_by(animal_id, location) %>% 
  summarise(
    number_of_events=n_distinct(first_detection),
    total_detections=sum(num_detections),
    total_time=sum(res_time_sec)
  )
~~~
{:.language-r}

GLATOS can also get the residency index of the animals. Each station/location will be given a percentage of time an animal has been at that location.
Ideally, the sum of all REI for an animal should be 100% but it can be higher depending on the time interval used, and the size of the locations.

~~~
rei <- residence_index(events)
rei <- residence_index(events, calculation_method = 'time_interval', time_interval_size = '1 hour')
~~~
{:.language-r}

And here's a fun plot if we have time.

~~~
library(ggplot2)

p <- ggplot() + 
  geom_point(data=event_summary, aes(x = location, y = total_detections, colour = animal_id), size = 2) + 
  theme(axis.text.x = element_text(angle = -45))
p
~~~
{:.language-r}

