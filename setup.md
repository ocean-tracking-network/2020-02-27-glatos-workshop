---
title: Setup
---

## Requirements

### R version: 3.6.x or newer and RStudio

Open RStudio and run this install script. It's best to run it line by line instead of all at once in case there are errors. 
<b>Note:</b> When running through the installs, you may encounter a prompt asking you to upgrade dependent packages. Choosing Option `3: None`, works in most situations and will prevent upgrades of packages you weren't explicitly looking to upgrade.
```r


install.packages("devtools")

# Tidyverse (data cleaning and arrangement)
install.packages('tidyverse')

# Mapping spatial data
install.packages('raster')
install.packages('mapdata')
install.packages('maptools')
install.packages('maps')
install.packages('ggplot2')
install.packages('ggmap')

# VTrack - Tools for Telemetry Analysis
devtools::install_github("rossdwyer/VTrack")

# GLATOS - acoustic telemetry package that does filtering, vis, array simulation, etc.
install.packages('remotes')
library(remotes)
install_url("https://gitlab.oceantrack.org/GreatLakes/glatos/repository/master/archive.zip",
            build_opts = c("--no-resave-data", "--no-manual"))  
```

This step is for the Instructors only.

```r
# Instructors only
# Setup for Vtrack crosswalk

library(remotes)
install_url("https://ocean-tracking-network.github.io/ideasotn-glatos-intro/glatos_0.4.0.1.tar.gz",
            build_opts = c("--no-resave-data", "--no-manual"))  

```

Once the packages are installed, change your working directory in RStudio to `acoustic-workshop` using the files menu, or the `setwd('~/Desktop/acoustic-workshop')`


