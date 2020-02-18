---
title: "Visualization Using GLATOS and a Sneak Peek"
teaching: 30
exercises: 0
questions:
- "How does the GLATOS package facilitate data visualization?"
- "What new functions are coming to GLATOS?"
objectives:
- "Demonstrate the different plots from the GLATOS package"
- "Demonstrate VTrack crosswalk pipeline"
keypoints:
- "Provides functionality for creating basic plots from GLATOS formatted data."
- "VTrack and GLATOS will be able to cross walk data to eachother in the near future"
---


## Basic Plots with GLATOS

We can do 2 simple plots to summarise your detections. We can make a abacus plot

~~~
abacus_plot(dets, location_col = 'station')

Canada <- getData('GADM', country="CAN", level=1)
NS <- Canada[Canada$NAME_1=="Nova Scotia",]

detection_bubble_plot(dets, location_col = 'station', map = NS,
    background_xlim = c(-66, -62),
    background_ylim = c(42, 46))

detection_bubble_plot(dets, location_col = 'station', map = NS,
    background_xlim = c(-63.5, -63),
    background_ylim = c(44, 44.5))
~~~
{:.language-r}

## Future Works

We are working on cross walking data from glatos to ATT/VTrack. Here's a demo of how it will work.

~~~
library(glatos)
dets_path <- system.file("extdata", "blue_shark_detections.csv",
                         package = "glatos")
deploy_path <- system.file("extdata", "hfx_deploy_simplified.xlsx",
                           package = "glatos")
tag_path <- system.file("extdata", "otn_nsbs_tag_metadata.xls",
                        package = "glatos")

dets <- read_otn_detections(dets_path)
tags <- prepare_tag_sheet(tag_path, 5, 2)
deploy <- prepare_deploy_sheet(deploy_path)

ATTdata <- convert_otn_to_att(dets, tags, deploymentSheet = deploy)

COAdata <- 
    COA(ATTdata, 
        timestep = 60) ## timestep used to estimate centers of activity (in minutes)

COAdata
~~~
{:.language-r}


