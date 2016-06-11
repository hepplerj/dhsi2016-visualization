---
title       : "Introduction to Leaflet"
author      : Jason A. Heppler
maketitle: true
output: html_document
---





## Leaflet

Leaflet is a popular open-source JavaScript library for creating interactive maps, used by a range of sites including the *New York Times*, the *Washington Post*, GitHub, Flickr, OpenStreetMap, Mapbox, and CartoDB.

This package makes using Leaflet in R simple.

## Leaflet

Leaflet features include:

- Interactive panning/zooming
- Compose maps using:
    - Map tiles
    - Markers
    - Polygons
    - Lines
    - Popups
    - GeoJSON
  
## Leaflet

Leaflet features include:

- Create maps right from the R console or RStudio
- Embed maps in knitr/R Markdown documents and Shiny apps
- Easily render Spatial objects from the sp package, or data frames with latitude/longitude columns
- Use map bounds and mouse events to drive Shiny logic

## Leaflet


```r
install.packages("leaflet")
# or the dev version
# devtools::install_github("rstudio/leaflet")
```

## Leaflet

Let's recall our process for building maps in `ggplot`.


```r
library(ggplot2)
library(ggmap)
map <- ggplot() + 
           geom_point(data=superfund_sites, 
                      aes(x=longitude, y=latitude))
```

```
## Error in fortify(data): object 'superfund_sites' not found
```

## {.fullscreen}


```
## Error in eval(expr, envir, enclos): object 'map' not found
```

## Leaflet


```r
basemap <- get_map(location = "United States",
                      zoom=4,
                      maptype = "terrain",
                      source="google",
                      color="bw")
```

```
## Map from URL : http://maps.googleapis.com/maps/api/staticmap?center=United+States&zoom=4&size=640x640&scale=2&maptype=terrain&language=en-EN&sensor=false
```

```
## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=United%20States&sensor=false
```

## Leaflet


```r
map <- ggmap(basemap) + 
      geom_point(data=superfund_sites, aes(x=longitude, y=latitude), 
                 size = 0.5, alpha = 0.4, color = "#e34a33")
```

```
## Error in fortify(data): object 'superfund_sites' not found
```

## {.fullscreen}


```
## Error in eval(expr, envir, enclos): object 'map' not found
```

## Leaflet

Leaflet maps are easily initiated by invoking `leaflet()`.


```r
library(leaflet)
leaflet() %>% addTiles() %>% 
  addMarkers(lng = -123.3656, lat = 48.4284)
```

```
## Error in html_screenshot(x): Please install the webshot package (if not on CRAN, try devtools::install_github("wch/webshot"))
```

## Leaflet - Superfund Map

Let's try mapping the first thirty Superfund sites in the dataset. 


```r
superfund_leaflet <- leaflet() %>% addTiles() %>%
      addCircleMarkers(data = superfund_sites[1:30,])
```

```
## Error in derivePoints(data, lng, lat, missing(lng), missing(lat), "addCircleMarkers"): object 'superfund_sites' not found
```

## {.fullscreen}


```
## Error in eval(expr, envir, enclos): object 'superfund_leaflet' not found
```

## Leaflet - Popups


```r
leaflet() %>% addTiles() %>%
      addCircleMarkers(data=superfund_sites[1:20,],
                       stroke = FALSE, fillOpacity = 0.5, radius=8,
                       popup=~paste("<strong>Site:</strong>", site.name,
                       "<br>",
                       "<strong>Date Added:</strong>", date,
                       "<br>",
                       "<strong>County:</strong>", county))
```

```
## Error in derivePoints(data, lng, lat, missing(lng), missing(lat), "addCircleMarkers"): object 'superfund_sites' not found
```

## Leaflet

Let's look at another dataset.


```r
peaks <- readOGR("./data/peaks.geojson", "OGRGeoJSON", verbose=FALSE)
# Yosemite boundary shapefile
yosemite <- readOGR("./data/yosemite", "yosemite", verbose = FALSE)
```

## Yosemite


```
## Error in derivePolygons(data, lng, lat, missing(lng), missing(lat), "addPolylines"): object 'yosemite' not found
```

## Leaflet


```r
# Convert peak values to numeric
peaks <- subset(peaks, peaks@data$ele != "0")
peaks@data$ele <- round(as.numeric(levels(peaks@data$ele))[peaks@data$ele] * 3.28084)

# Make map of peaks
leaflet() %>%
  addTiles() %>%
  addCircleMarkers(data=peaks, weight=0, radius=8, fillOpacity=0.6,
                   popup=~paste("Name: ", name, "<br>Elevation: ", ele, "feet")) %>%
  addPolylines(data=yosemite, fillOpacity = 0, dashArray=c(10,10), fill=FALSE, color="red")
```

## {.fullscreen}


```
## Error in subset(peaks, peaks@data$ele != "0"): error in evaluating the argument 'x' in selecting a method for function 'subset': Error: object 'peaks' not found
```

```
## Error in levels(peaks@data$ele): error in evaluating the argument 'x' in selecting a method for function 'levels': Error: object 'peaks' not found
```

```
## Error in derivePoints(data, lng, lat, missing(lng), missing(lat), "addCircleMarkers"): object 'peaks' not found
```

## Leaflet


```r
leaflet() %>%
  addTiles() %>%
  addCircleMarkers(data=peaks, weight=0, radius=~ele/1000, fillOpacity=0.6,
                   popup=~paste("Name: ", name, "<br>Elevation: ", ele, "feet")) %>%
  addPolylines(data=yosemite, fillOpacity = 0, dashArray=c(10,10), fill=FALSE, color="red")
```

## {.fullscreen}


```
## Error in derivePoints(data, lng, lat, missing(lng), missing(lat), "addCircleMarkers"): object 'peaks' not found
```

## Leaflet - Color and Legends


```r
# Create the color scale from elevation
# using the ColorBrewer palette
spectral <- brewer.pal(11, "Spectral")  %>% rev()
eleColor <- colorBin(spectral, peaks@data$ele)

leaflet(peaks) %>%
  addTiles() %>%
  addCircleMarkers(weight=0, radius=~ele/1000, fillOpacity=0.7, color = ~eleColor(ele),
                   popup=~paste("Name: ", name, "<br>Elevation: ", ele)) %>%
    addLegend(title = "Peak Elevation", pal = eleColor,
            values = ~ele, opacity = 1, position="bottomleft") %>%
  addPolylines(data=yosemite, fillOpacity = 0, dashArray=c(10,10), fill=FALSE, color="blue")
```

## {.fullscreen}


```
## Error in colorBin(spectral, peaks@data$ele): object 'peaks' not found
```

```
## Error in structure(list(), leafletData = data): object 'peaks' not found
```

## Leaflet - Basemaps


```r
leaflet() %>%
  addProviderTiles("CartoDB.Positron") %>%
  addPolylines(data=yosemite, fillOpacity = 0, dashArray=c(10,10), fill=FALSE, color="black")
```

## {.fullscreen}


```
## Error in derivePolygons(data, lng, lat, missing(lng), missing(lat), "addPolylines"): object 'yosemite' not found
```

## {.fullscreen}


```
## Error in structure(list(), leafletData = data): object 'peaks' not found
```

## Layer Switcher

Assign a **group** to each of the layers for a layer selector to work.

## {.fullscreen}


```r
mapbox <- "http://api.tiles.mapbox.com/v4/mapbox.outdoors/{z}/{x}/{y}.png?access_token=pk.eyJ1Ijoiam9zaHBlcHBlciIsImEiOiJuTWdrY2k4In0.HCCXtgU04scrTB_-ON4kjA"

leaflet(peaks) %>%
  addCircleMarkers(weight = 0, radius = ~ele/1000, 
                   fillOpacity = 0.7, color = ~eleColor(ele),
                   popup = ~paste("Name: ", name, "<br>Elevation: ", ele),
                   group = "Peaks") %>%
  
  addLegend(title = "Peak Elevation", pal = eleColor,
            values = ~ele, opacity = 1, position="bottomright") %>%
  
  addPolylines(data = yosemite, fillOpacity = 0, 
               dashArray = c(10,10), fill = FALSE, color = "blue",
               group = "Yosemite") %>%
  
  # Add basemaps
  addProviderTiles("CartoDB.Positron", group="Simple") %>%
  addProviderTiles("Esri.WorldImagery", group="Satellite") %>%
  addTiles(urlTemplate = mapbox, group="Outdoors") %>%
  
  # Add layer switcher
   addLayersControl(
    baseGroups = c("Simple", "Satellite", "Outdoors"),
    overlayGroups = c("Peaks","Yosemite"),
    options = layersControlOptions(collapsed = FALSE)
  )
```

```
## Error in structure(list(), leafletData = data): object 'peaks' not found
```
