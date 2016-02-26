---
layout: post
title: "Spatial Intro 04: Quick Intro to Coordinate Reference Systems & Spatial
Projections"
date: 2015-10-26
authors: [Leah A. Wasser, Megan A. Jones]
contributors: [ ]
dateCreated: 2015-10-23
lastModified: 2016-02-26
packagesLibraries: [ ]
category: [self-paced-tutorial] 
tags: [R, spatial-data-gis]
mainTag: spatial-data-management-series
workshopSeries: [spatial-data-management-series]
description: "This lesson covers the key spatial attributes that are needed to work with 
spatial data including: Coordinate Reference Systems (CRS), Extent and spatial resolution."
code1: key-spatial-metadata.R
image:
  feature: NEONCarpentryHeader_2.png
  credit: A collaboration between the National Ecological Observatory Network (NEON) and Data Carpentry
  creditlink: 
permalink: R/intro-to-coordinate-reference-systems
comments: true
---

{% include _toc.html %}

## About

This lesson covers the key spatial attributes that are needed to work with 
spatial data including: Coordinate Reference Systems (CRS), Extent and spatial resolution.

**R Skill Level:** Beginner - you've got the basics of `R` down.

<div id="objectives" markdown="1">

# Goals / Objectives

After completing this activity, you will:

* The basic concept of what a Coordinate Reference System (`CRS`) is and how it
impacts data processing, analysis and visualization.
* Understand the basic differences between a geographic and a projected `CRS`.
* Become familiar with the Universal Trans Mercator (UTM) and Geographic (WGS84) `CRS`s


## Things You’ll Need To Complete This Lesson

To complete this lesson you will need the most current version of `R`, and 
preferably, `RStudio` loaded on your computer.

### Install R Packages

* **NAME:** `install.packages("NAME")`

[More on Packages in R - Adapted from Software Carpentry.]({{site.baseurl}}R/Packages-In-R/)

### Download Data


****

{% include/_greyBox-wd-rscript.html %}

**Spatial-Temporal Data & Data Management Lesson Series:** This lesson is part
of a lesson series introducing
[spatial data and data management in `R` ]({{ site.baseurl }}tutorial/URL).
It is also part of a larger 
[spatio-temporal Data Carpentry Workshop ]({{ site.baseurl }}workshops/spatio-temporal-workshop)
that includes working with  
[raster data in `R` ]({{ site.baseurl }}tutorial/spatial-raster-series),
[vector data in `R` ]({{ site.baseurl }}tutorial/spatial-vector-series)
and  
[tabular time series in `R` ]({{ site.baseurl }}tutorial/tabular-time-series).

****

### Additional Resources
* Read more on coordinate systems in the
<a href="http://docs.qgis.org/2.0/en/docs/gentle_gis_introduction/coordinate_reference_systems.html" target="_blank">
QGIS documentation.</a>
* NEON Data Skills Lesson <a href="{{ site.baseurl }}/GIS-Spatial-Data/Working-With-Rasters/" target="_blank">The Relationship Between Raster Resolution, Spatial extent & Number of Pixels - in R</a>

</div>

## What is a Coordinate Reference System

To define the location of something we often use a coordinate system. This system
consists of an X and a Y value, located within a 2 (or more) -dimensional 
(as shown below) space.

<figure>
	<a href="http://open.senecac.on.ca/clea/label/projectImages/15_276_xy-grid.jpg">
	<img src="http://open.senecac.on.ca/clea/label/projectImages/15_276_xy-grid.jpg"></a>
	<figcaption> We use coordinate systems with X, Y (and sometimes Z axes) to
	define the location of objects in space. 
	Source: http://open.senecac.on.ca
	</figcaption>
</figure>

While the above coordinate system is 2-dimensional, we live on a 3-dimensional
earth that happens to be "round". To define the location of objects on the earth, which is round, we need
a coordinate system that adapts to the Earth's shape. When we make maps on paper 
or on a flat computer screen, we move from a 3-Dimensional space (the globe) to 
a 2-Dimensional space (our computer
screens or a piece of paper). The components of the CRS define how the
"flattening" of data that exists in a 3-D globe space. The CRS also defines the
the coordinate system itself. 

> A  coordinate reference system (CRS) is a 
coordinate-based local, regional or global system used to locate geographical 
entities. -- Wikipedia

## The Components of a CRS

The coordinate reference system is made up of several key components: 

* **Coodinate System:** the grid upon which our data is overlayed and how we define
where a point is located in space.
* **Horizontal and vertical units:** The units used to define the grid along the 
x, y (and z) axis.
* **Datum:** Defined by the shape of the origin used to place the coordinate 
system in 
space. We will explain this further, below.
* **Projection Information:** the mathematical equation used to flatten objects 
that are on a round surface (e.g. the earth) so we can view them on a flat surface
(e.g. our computer screens or a paper map).


## Why CRS is Important

It is important to understand the coordinate system that your data uses - 
particularly if you are working with different data stored in different coordinate
systems. If you have data from the same location that are stored in different 
coordinate reference systems, **they will not line up in any GIS or other program**. 

<i class="fa fa-star"></i> **Data Tip:** Spatialreference.org provides an
excellent <a href="http://spatialreference.org/ref/epsg/" target="_blank">online 
library of CRS information.</a>
{: .notice}

### Coordinate System & Units

We can define a spatial location, such as a plot location, using an x- and a
y-value - similar to our cartesian coordinate system displayed in the figure, 
above. 

For example, the map below, generated in `R` with `ggplot2` shows all of the 
continents in the world, in a `Geographic` Coordinate Reference System. The
units are Degrees and the coordinate system itself is **latitude** and
**longitude**. 

NOTE: you can <a href="http://www.naturalearthdata.com/downloads/110m-physical-vectors/110m-land/" target="_blank">download globe "land" data </a> to reproduce the
plot below.


    library(rgdal)
    library(ggplot2)
    library(rgeos)
    library(raster)
    setwd("~/Documents/data")
    
    # read shapefile
    worldBound <- readOGR(dsn="Global/Boundaries/ne_110m_land", 
                          layer="ne_110m_land")

    ## OGR data source with driver: ESRI Shapefile 
    ## Source: "Global/Boundaries/ne_110m_land", layer: "ne_110m_land"
    ## with 127 features
    ## It has 2 fields

    # convert to dataframe
    worldBound_df <- fortify(worldBound)  

    ## Regions defined for each Polygons

    # plot map
    worldMap <- ggplot(worldBound_df, aes(long,lat, group=group)) +
      geom_polygon() +
      xlab("Longitude (Degrees)") + ylab("Latitude (Degrees)") +
      coord_equal() +
      ggtitle("Global Map - Geographic Coordinate System - WGS84 Datum\n Units: Degrees - Latitude / Longitude")
    
    worldMap

![ ]({{ site.baseurl }}/images/rfigs/dc-spatio-temporal-intro/04-intro-CRS-metadata/lat-long-example-1.png) 

We can add three coordinate locations to our map. Note that the UNITS are
in decimal **degrees** (latitude, longitude):

* Boulder, Colorado:  40.0274, -105.2519
* Oslo, Norway: 50.9500, 10.7500
* Mallorca, Spain: 39.6167, 2.9833

Let's create a second map with the locations overlayed on top of the continental
boundary layer.


    # define locations of Boulder, CO and Oslo, Norway
    loc <- data.frame(lon=c(-105.2519, 10.7500, 2.9833),
                    lat=c(40.0274, 50.9500, 39.6167))
    
    # convert to dataframe
    loc.df <- fortify(loc)  
    
    # add a point to the map
    mapLocations <- worldMap + geom_point(data=loc.df, 
                          aes(x=lon, y=lat, group=NULL, colour = "purple"),
                          size=5)
    
    mapLocations + theme(legend.position="none")

![ ]({{ site.baseurl }}/images/rfigs/dc-spatio-temporal-intro/04-intro-CRS-metadata/add-lat-long-locations-1.png) 

## Geographic CRS - The Good & The Less Good
Geographic coordinate systems in decimal degrees do a good job of helping is 
identify the location of things on the Earth, however latitude and longitude
locations are not located using uniform measurement units. Thus, geographic 
CRSs are not ideal for measuring distance. This is why other projected `CRS` 
have beend developed. 

### Robinson Projection

We can view the same data above, in another CRS - `Robinson`. `Robinson` is a 
**projected** `CRS`. Notice that the features on the map - have a different shape 
compared to the map that we created above in the `CRS`:  **Geographic lat/long WGS84**.


    # reproject from longlat to robinson
    worldBound_robin <- spTransform(worldBound,
                                    CRS("+proj=robin"))
    
    worldBound_df_robin <- fortify(worldBound_robin)

    ## Regions defined for each Polygons

    robMap <- ggplot(worldBound_df_robin, aes(long,lat, group=group)) +
      geom_polygon() +
      labs(title="World map (robinson)") +
      coord_equal()
    
    robMap

![ ]({{ site.baseurl }}/images/rfigs/dc-spatio-temporal-intro/04-intro-CRS-metadata/global-map-robinson-1.png) 

Now what happens if you try to add the same Lat / Long coordinate locations that
we used above, to our map, with the `CRS` of `Robinsons`?


    # add a point to the map
    newMap <- robMap + geom_point(data=loc.df, 
                          aes(x=lon, y=lat, group=NULL, colour = "purple"),
                          size=5)
    
    newMap + theme(legend.position="none")

![ ]({{ site.baseurl }}/images/rfigs/dc-spatio-temporal-intro/04-intro-CRS-metadata/add-locations-robinson-1.png) 

Notice above that when we try to add lat/long coordinates to a map in a different
`CRS`, that the points are not in the correct location. We need to first convert
the points to the  new projection - a process often referred to as **reprojection**
but performed by the `spTransform()` function in `R`.


    # define locations of Boulder, CO and Oslo, Norway
    loc 

    ##         lon     lat
    ## 1 -105.2519 40.0274
    ## 2   10.7500 50.9500
    ## 3    2.9833 39.6167

    # convert to spatial Points data frame
    loc.spdf <- SpatialPointsDataFrame(coords = loc,data=loc,
                                proj4string=crs(worldBound))  
    
    loc.spdf

    ## class       : SpatialPointsDataFrame 
    ## features    : 3 
    ## extent      : -105.2519, 10.75, 39.6167, 50.95  (xmin, xmax, ymin, ymax)
    ## coord. ref. : +proj=longlat +datum=WGS84 +no_defs +ellps=WGS84 +towgs84=0,0,0 
    ## variables   : 2
    ## names       :       lon,     lat 
    ## min values  : -105.2519, 39.6167 
    ## max values  :     10.75,   50.95

    # reproject data to Robinson
    loc.spdf.rob <- spTransform(loc.spdf, CRSobj = CRS("+proj=robin"))
    
    loc.rob.df <- as.data.frame(cbind(loc.spdf.rob$lon,loc.spdf.rob$lat))
    names(loc.rob.df ) <- c("X","Y")
    loc.rob <- fortify(loc.rob.df)
    # notice the coordinate system in the Robinson projection (CRS) is DIFFERENT
    # from the coordinate values for the same locations in a geographic CRS.
    loc.rob

    ##            X       Y
    ## 1 -9162993.5 4279263
    ## 2   875481.0 5424980
    ## 3   260256.6 4235608

    # add a point to the map
    newMap <- robMap + geom_point(data=loc.rob, 
                          aes(x=X, y=Y, group=NULL, colour = "purple"),
                          size=5)
    
    newMap + theme(legend.position="none")

![ ]({{ site.baseurl }}/images/rfigs/dc-spatio-temporal-intro/04-intro-CRS-metadata/reproject-robinson-1.png) 

## Why Multiple CRS?

You may be wondering, why bother with different `CRS`s if it makes our
analysis more complicated? Well, each `CRS` is optimized to best represent:

* Shape
* Scale / distance
* Area 

of features in the data. And no one CRS is great at optimizing shape, distance AND
area. Some `CRS`s are optimized for shape, some distance, some area. Some 
`CRS`s are also optimized for particular regions - 
for instance the United States, or Europe. Discussing `CRS` as it optimizes shape,
distance and area is beyond the scope of this tutorial, but it's important to
understand that the `CRS` that you chose for your data, will impact working with
the data!

<div id="challenge" markdown="1">
## Challenge

1. Compare the maps of the globe above. What do you notice about the shape of the 
various countries. Are there any signs of distortion in certain areas on either
map? Which one is better?

2. look at the image below - which depicts maps of the United States in 4 different
`CRS`s. What visual differences do you notice in each map? Look up each projection
online, what elements (shape,area or distance) does each projection used in
the graphic below optimize?

</div>



***

<figure>
    <a href="https://source.opennews.org/media/cache/b9/4f/b94f663c79024f0048ae7b4f88060cb5.jpg">
    <img src="https://source.opennews.org/media/cache/b9/4f/b94f663c79024f0048ae7b4f88060cb5.jpg">
    </a>
    
    <figcaption>Maps of the United States in different CRS including Mercator
    (upper left), Albers equal area (lower left), UTM (Upper RIGHT) and 
    WGS84 Geographic (Lower RIGHT). 
    Notice the differences in shape and orientation associated with each
    CRS. These differences are a direct result of the
    calculations used to "flatten" the data onto a two dimensional map. 
    Source: opennews.org</figcaption>
</figure>


## Geographic vs Projected CRS


The above maps provide examples of the two main types of coordinate systems:

1. **Geographic coordinate systems:** coordinate systems that span the entire
globe (e.g. latitude / longitude). 
2. **Projected coordinate Systems:** coordinate systems that are localized to 
minimize visual distortion in a particular region (e.g. Robinson, UTM, State Plane)

Next, we will discuss the differences between these `CRS`s in more detail. Feel
free to skip over this section and come back to it with fresh eyes if the 
concept of a `CRS` is becoming too complex. It's easisest to take on in 
bite sized pieces!


#### How Map Projections Can Fool the Eye
Check out this short video highlighting how map projections can make continents 
look proportionally larger or smaller than they actually are!

<iframe width="560" height="315" src="https://www.youtube.com/embed/KUF_Ckv8HbE" frameborder="0" allowfullscreen></iframe>

* For more on types of projections, visit 
<a href="http://help.arcgis.com/en/arcgisdesktop/10.0/help/index.html#/Datums/003r00000008000000/" target="_blank"> ESRI's ArcGIS reference on projection types.</a>.  
* Read more about <a href="https://source.opennews.org/en-US/learning/choosing-right-map-projection/" target="_blank"> choosing a projection/datum.</a>


## Geographic Coordinate Systems

A geographic coordinate system uses a grid that wraps around the entire globe.
This means that each point on the globe is defined using the SAME coordinate system. Geographic coordinate systems are best for global analysis however it 
is important to remember that distance is distorted using a geographic lat / long
`CRS`.

The **geographic WGS84 lat/long** `CRS` has an origin - (0,0) -
located at the intersection of the 
Equator (0° latitude) and Prime Meridian (0° longitude) on the globe.


    ## OGR data source with driver: ESRI Shapefile 
    ## Source: "../../Global/Boundaries/ne_110m_graticules_all", layer: "ne_110m_graticules_30"
    ## with 17 features
    ## It has 6 fields

    ## OGR data source with driver: ESRI Shapefile 
    ## Source: "../../Global/Boundaries/ne_110m_graticules_all", layer: "ne_110m_wgs84_bounding_box"
    ## with 1 features
    ## It has 2 fields

![ ]({{ site.baseurl }}/images/rfigs/dc-spatio-temporal-intro/04-intro-CRS-metadata/geographic-WGS84-1.png) 



<i class="fa fa-star"></i> **Data Note:** The distance between the 2 degrees of
longitude at the equator (0°) is ~ 69 miles. The distance between 2 degrees of 
longitude at 40°N (or S) is only 53 miles. However measures of distance when using
lat/long project are not uniform.
{: .notice}


## Projected Coordinate Reference Systems


Spatial projection refers to the mathematical calculations 
performed to flatten the 3D data onto a 2D plane (our computer screen
or a paper map). Projecting data from a round surface onto a flat surface, results
in visual modifications to the data when plotted on a map. Some areas are stretched
and some some are compressed. We can see this when we look at a MAP of the entire
globe.


The mathematical calculations used in spatial projections are designed to 
optimize the relative size and shape of a particular region on the globe.

<figure>
    <a href="http://www.progonos.com/furuti/MapProj/Normal/CartDef/MapDef/Img/devSurfaces.png">
    <img src="http://www.progonos.com/furuti/MapProj/Normal/CartDef/MapDef/Img/devSurfaces.png">
    </a>
    <figcaption>The 3-dimensional globe must be transformed to create a flat
    2-dimensional map. How that transformation or **projection** occurs changes 
    the appearance of the final map and the relative size of objects in 
    different parts of the map.  
    Source: CA Furuti, progonos.com/furuti</figcaption>
</figure>

A few common example projections, and how they optimize the visual appearance of a region
on a map, are listed below.

# make the below into a table 
* **State Plane** Region: United States. Optimized for: states
in the U.S. Sub regions:  north / south (withing hte state). Units:
Feet 
* **Universal Trans Mercator (UTM)** Region: Entire Globe, Optimized for: regions 
throughout the globe. Sub regions: north / south (above / below the equator)divides the entire globe

### About UTM

The **Universal Transverse Mercator** (UTM) system is a projected coordinate
reference system. The earth is flattened and subdivided into zones,
numbered 0-60 (equivalent to longitude) and regions (north and south)


<i class="fa fa-star"></i> **Data Note:** UTM zones are also defined using bands,
lettered C-X (equivalent to latitude) however, the band designation is often 
dropped as it isn't esssential to specifying the location. 
{: .notice}

While UTM zones span the entire globe, UTM uses a regional 
projection and associated coordinate system. The coordinate system grid for each
zone is projected individually using the **Mercator projection**. 

The origin (0,0) for each UTM zone and associated region is located at the 
intersection of the equator and a location, 500,000 meters east of the central 
meridian of each zone. The origin location is placed outside of the boundary of 
the UTM zone, to avoid negative Easting numbers.  

# LINK TO PSU INFO ON UTM!

<figure>
    <a href="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ed/Utm-zones.jpg/800px-Utm-zones.jpg">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ed/Utm-zones.jpg/800px-Utm-zones.jpg">
    </a>
    <figcaption> The gridded UTM coordinate system across the globe.    
    Source: Public domain from Earth Observatory, NASA</figcaption>
</figure>

# edit this messy
The coordinates for the NEON Harvard Forest Field Site can be written as:
would be written as: UTM Zone 18N, 730782m, 4712631m
where the N denotes that it is in the Northern, not Southern hemisphere. Occassionally,
you may see: Zone 18T, 730782m Easting, 4712631m Northing.

<i class="fa fa-star"></i>**Data Tip:**  The UTM system doesn't apply to polar 
regions (>80°N or S). Universal Polar Stereographic (UPS) coordinate system is 
used in these area. This is where zones A, B and Y, Z are used if you were 
wondering why they weren't in the UTM lettering system.  
{: .notice }


### Datum

The datum describes the vertical and horizontal reference point of the coordinate 
system. The vertical datum describes the relationship between a specific ellipsoid 
(the top of the earth's surface) and the center of the earth. The datum also describes
the origin (0,0) of a coordinate system. 

Frequently encountered datums: 

* *WGS84* -- World Geodetic System (created in) 1984.  The origin is the center of
the earth.
* *NAD27* & *NAD83* -- North American Datum 1927 and 1983,
respectively.  The origin for NAD 27 is Meades Ranch in Kansas.
* *ED50* -- European Datum 1950

For more information, read
*  <a href="http://help.arcgis.com/en/arcgisdesktop/10.0/help/index.html#/Datums/003r00000008000000/" target="_blank">ESRI's ArcGIS discussion of Datums.</a> 
*  <a href="https://www.nceas.ucsb.edu/~frazier/RSpatialGuides/OverviewCoordinateReferenceSystems.pdf" target="_blank">page 2 in M. Fraiser's CRS Overview.</a> 


> NOTE: All coordinate reference systems have a vertical and horizontal datum
which defines a "0, 0" reference point. There are two models used to define 
the datum: **ellipsoid** (or spheroid): a mathematically representation of the shape of
the earth. Visit
<a href="https://en.wikipedia.org/wiki/Earth_ellipsoid" target="_blank">Wikipedia's article on the earth ellipsoid </a>  for more information and **geoid**: a 
model of the earth's gravitatinal field which approximates the mean sea level 
across the entire earth.  It is from this that elevation is measured. Visit 
<a href="https://en.wikipedia.org/wiki/Geoid" target="_blank">Wikipedia's geoid
article </a>for more information. We will not cover these concepts in this tutorial.



### Coordinate Reference System Formats

There are numerous formats that are used to document a `CRS`. Three common
formats include:

* **proj.4**
* **EPSG**
* Well-known Text (**WKT**)
formats.  

#### PROJ or PROJ.4 strings 

PROJ.4 strings are a compact way to identify a spatial or coordinate reference 
system. PROJ.4 strings are the primary output from most of the spatial data `R`
packages that we will use (e.g. `raster`, `rgdal`).

Using the PROJ.4 syntax, we specify the complete set of parameters (e.g. elipse, datum,
units, etc) that define a particular CRS.


## Understanding CRS in Proj4 Format
The `CRS` for our data are given to us by `R` in `proj4` format. Let's break
down the pieces of `proj4` string. The string contains all of the individual
`CRS` elements that `R` or another `GIS` might need. Each element is specified
with a `+` sign, similar to how a `.csv` file is delimited or broken up by 
a `,`. After each `+` we see the `CRS` element being defined. For example
`+proj=` and `+datum=`.

### UTM Proj4 String
Our project string for `point_HARV` specifies the UTM projection as follows: 

`+proj=utm +zone=18 +datum=WGS84 +units=m +no_defs +ellps=WGS84 +towgs84=0,0,0` 

* **+proj=utm:** the projection is UTM, UTM has several zones.
* **+zone=18:** the zone is 18
* **datum=WGS84:** the datum WGS84 (the datum refers to the  0,0 reference for
the coordinate system used in the projection)
* **+units=m:** the units for the coordinates are in METERS.
* **+ellps=WGS84:** the ellipsoid (how the earth's  roundness is calculated) for 
the data is WGS84

Note that the `zone` is unique to the UTM projection. Not all `CRS` will have a 
zone.

### Geographic (lat / long) Proj4 String

Our project string for `State.boundary.US` and `Country.boundary.US` specifies
the lat/long projection as follows: 

`+proj=longlat +datum=WGS84 +no_defs +ellps=WGS84 +towgs84=0,0,0` 

* **+proj=longlat:** the data are in a geographic (latitude and longitude)
coordinate system
* **datum=WGS84:** the datum WGS84 (the datum refers to the  0,0 reference for
the coordinate system used in the projection) 
* **+ellps=WGS84:** the ellipsoid (how the earth's roundness is calculated)
is WGS84

Note that there are no specified units above. This is because this geographic 
coordinate reference system is in latitude and longitude which is most 
often recorded in *Decimal Degrees*.

<i class="fa fa-star"></i> **Data Tip:** the last portion of each `proj4` string 
is `+towgs84=0,0,0 `. This is a conversion factor that is used if a `datum` 
conversion is required. We will not deal with datums in this particular series.
{: .notice}


* Read more about <a href="https://www.nceas.ucsb.edu/scicomp/recipes/projections" target="_blank">all three formats from the National Center for Ecological Analysis and Synthesis.</a>

* A handy four page overview of CRS <a href="https://www.nceas.ucsb.edu/~frazier/RSpatialGuides/OverviewCoordinateReferenceSystems.pdf" target="_blank">including file formats on page 1.</a>

#### EPSG codes
The EPSG codes are a structured dataset of CRSs that are used around the world. They were
originally compiled by the, now defunct, European Petroleum Survey Group, hence the
EPSG acronym. Each code is a four digit number.  

The codes and more information can be found on these websites: 
* <a href="http://www.epsg-registry.org/" target="_blank">The EPSG registry. </a>
* <a href="http://spatialreference.org/" target="_blank">Spatialreference.org</a>
* <a href="http://spatialreference.org/ref/epsg/" target="_blank">list of ESPG codes.</a>


    library('rgdal')
    epsg = make_EPSG()
    # View(epsg)
    head(epsg)

    ##   code                                               note
    ## 1 3819                                           # HD1909
    ## 2 3821                                            # TWD67
    ## 3 3824                                            # TWD97
    ## 4 3889                                             # IGRS
    ## 5 3906                                         # MGI 1901
    ## 6 4001 # Unknown datum based upon the Airy 1830 ellipsoid
    ##                                                                                            prj4
    ## 1 +proj=longlat +ellps=bessel +towgs84=595.48,121.69,515.35,4.115,-2.9383,0.853,-3.408 +no_defs
    ## 2                                                         +proj=longlat +ellps=aust_SA +no_defs
    ## 3                                    +proj=longlat +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +no_defs
    ## 4                                    +proj=longlat +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +no_defs
    ## 5                            +proj=longlat +ellps=bessel +towgs84=682,-203,480,0,0,0,0 +no_defs
    ## 6                                                            +proj=longlat +ellps=airy +no_defs

#### WKT or Well-known Text
Well-known Text (WKT) allows for compact machine- and human-readable representation of
geometric objects as well as to consisely describing the critical elements of
coordinate reference system (CRS) definitions. 

The codes and more information can be found on these websites: 
* <a href="http://docs.opengeospatial.org/is/12-063r5/12-063r5.html#43" target="_blank">Open Geospatial Consortium WKT document. </a>


*** 
##Additional Resources
ESRI help on CRS
QGIS help on CRS
NCEAS cheatsheets