---
title: "Receiver Efficiency Index"
teaching: 20
exercises: 0
questions:
- "How do I calculate receiver efficiency with GLATOS?"
objectives:
- "Demonstrate how to calculate receiver efficiency"
keypoints:
- "GLATOS provides a method to calculate receiver efficiency using the GLATOS data objects."
---

The receiver efficiency index is number between `0` and `1`
indicating the amount of relative activity at each receiver compared to
the entire set of receivers, regardless of positioning. The function
takes a set detections and a deployment history of the receivers to
create a context for the detections. Both the amount of unique tags and
number of species are taken into consideration in the calculation.


The receiver efficiency index implement is implemented based on the
paper Acoustic telemetry array evolution: [From species- and
project-specific designs to large-scale, multispecies, cooperative
networks](https://www.sciencedirect.com/science/article/pii/S0165783618302509?via%3Dihub). 
Each receiver’s index is calculated on the formula of:

>\\(REI = \frac{T_r}{T_a} \times \frac{S_r}{S_a} \times \frac{DD_r}{DD_a} \times \frac{D_a}{D_r}\\)
>
>\\(REI\\) = Receiver Efficiency Index
>
>\\(T_r\\) = The number of tags detected on the receievr
>
>\\(T_a\\) = The number of tags detected across all receivers
>
>\\(S_r\\) = The number of species detected on the receiver
>
>\\(S_a\\) = The number of species detected across all receivers
>
>\\(DD_a\\) = The number of unique days with detections across all receivers
>
>\\(DD_r\\) = The number of unique days with detections on the receiver
>
>\\(D_a\\) = The number of days the array was active
>
>\\(D_r\\) = The number of days the receiver was active


## Importing Libraries
We will import `dplyr`,`glatos` and `plotly` to run then visualize the REI.
~~~
library(dplyr)
library(glatos)
library(plotly)
~~~
{:.language-r}


## Importing Data
We will import the sample data below using `glatos::read_glatos_detections()` and `glatos::read_glatos_receivers()`
~~~
detection_file <- system.file("extdata", "walleye_detections.csv", package = "glatos")
receiver_file <- system.file("extdata", "sample_receivers.csv", package = "glatos")

receivers <- read_glatos_receivers(receiver_file)
detections <- read_glatos_detections(detection_file)
~~~
{:.language-r}

## Cleaning Data
Below we use `dplyr::mutate()` to ensure that any recovery times that are
set as `NA` are set to the current date and time. You can replace `Sys.time()`
with the last known download time if you know it.
~~~
receivers <- receivers %>% 
  mutate( recover_date_time = replace(recover_date_time,
                                      is.na(recover_date_time), 
                                      Sys.time()))
~~~
{:.language-r}

## Running REI
`REI()` takes two arguments. The first is a dataframe of detections
the detection timstamp, the station identifier, the species, and the tag
identifier. The next is a dataframe of deployments for each station. The
station name should match the stations in the detections. The
deployments need to include a deployment date and recovery date or last
download date. Details on the columns metnioned see the preparing data
section.
~~~
rei <- glatos::REI(detections,receivers)
~~~
{:.language-r}

## Plotting with Plotly

Below is the code for plotting the RI using `plotly`. `plotly` allows us to 
interact with the map rather than haveing a static image. More about `plotly`
can be found [here](https://plot.ly/r/).

~~~
geo <- list(
  scope = 'north america',
  showland = TRUE,
  landcolor = toRGB("white"),
  showocean = TRUE,
  oceancolor = toRGB("gray"),
  showcountreies = TRUE,
  showlakes = TRUE,
  lakecolor = plotly::toRGB("gray"),
  
  resolution = 50,
  center = list(lat = median(rei$latitude),
                lon = median(rei$longitude)),
  lonaxis = list(range=c(min(rei$longitude)-1, max(rei$longitude)+1)),
  lataxis = list(range=c(min(rei$latitude)-1, max(rei$latitude)+1))
)


map <- rei %>% 
  plot_geo(lat = ~latitude, lon = ~longitude, color = ~rei,width=900 )%>%
  add_markers(
    text = ~paste(station, ': ', rei),
    hoverinfo = "text",
    size = ~c(rei * 5 +5)
  )%>%
  layout(title = "REI", geo = geo)

~~~
{:.language-r}

To show the map you can just type out the variable name.
~~~
map
~~~
{:.language-r}


## Plotting with Mapbox

Mapbox is a Live Location Platform that can serve up map tiles for use.
You can create a free account and get an access token [here](https://mapbox.com).
Below we set the access token as an environment variable that `plotly` can call.

**Please note that the provided token will be deleted after the end of the workshop, so if you want use mapbox later, you will need your own token.**

~~~
Sys.setenv('MAPBOX_TOKEN' = 'pk.eyJ1Ijoic29mdHdhcmVtb25rIiwiYSI6ImNrNzBubnVoazAwZjIzZW42c29ybGEzcXAifQ.pWqh1qxrRibjGMmHMThoiA')
~~~
{:.language-r}


 From there, we can just call the `plot_mapbox()` [function](https://plot.ly/r/scattermapbox/) and pass whatever arguments we need for the map.
~~~
rei_mapbox <- rei %>% 
  plot_mapbox(lat = ~latitude, lon = ~longitude, color = ~rei , width=900) %>%
  add_markers(
    text = ~paste(station, ':', rei), 
    hoverinfo = "text",
    size = ~c(rei*6 +10)
  )%>%
  layout(title = "REI Mapbox", 
    mapbox = list(zoom = 5,
                        center = list(lat = ~median(latitude),
                                      lon = ~median(longitude))
  ))

~~~
{:.language-r}

To show the map you can just type out the variable name.
~~~
rei_mapbox
~~~
{:.language-r}