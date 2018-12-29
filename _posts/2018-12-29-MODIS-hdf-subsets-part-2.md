---
title: MODIS hdf data extraction in r (part 2)
author: Koen Hufkens
layout: post
tags:
  - R
  - remote-sensing
  - data-processing
---

In a previous [blog post]((https://khufkens.com/2016/04/20/modis-hdf-data-extraction-in-r/ ) I describe how to subset MODIS hdf data. However, this was a rather simple example. Today, a graduate student emailed me to help her out with a subsetting problem she had when running the code, or better the lack of an option to extract a region of interest (rather than point data) in the previous example I gave.

You can find the final code (below) to help her define a region of interest using a bounding box and calculate summary statistics (mean) for this region on a file-by-file basis. The data output is tidy, so you can throw in any number of files (from multiple tiles) if you subset the final output properly. I hope this will help others in dealing with custom MODIS data subsets as well.

```R
# load the required libraries
library(raster)
library(MODIS)
library(sf)

# set the default MODIS projection
sinus <- "+proj=sinu +lon_0=0 +x_0=0 +y_0=0 +a=6371007.181 +b=6371007.181 +units=m +no_defs"

# location of some demo files
files <- list.files("~/Downloads/",
                    "*.hdf", full.names = TRUE)

# loop over all files
output <- lapply(files, function(file){
  
  # extract the date of the file name
  # first split into parts using the filename only (no path)
  # and a . (escaped as it is a special character) as a
  # delimiter and unlist the results - finally select the
  # second element (the date string)
  file_parts <- unlist(strsplit(basename(file), "\\."))
  date_string <- file_parts[2]
  
  # the date is formatted as A + YEAR + DOY (e.g. A2002295)
  # we can take substrings of the original to split out
  # the year and the day of year (DOY) value
  year <- as.numeric(substr(date_string, 2, 5))
  doy <- as.numeric(substr(date_string, 6, 8))
  
  # for later output we also grab the tile number
  # from the file
  tile <- file_parts[3]
  
  # list all SDS layers in the hdf
  sds <- MODIS::getSds(file)
  
  # read data into memory (if small enough raster should
  # figure this out on its own - you can force this using
  # brick)
  hdf_layer <- raster::raster(readGDAL(sds$SDS4gdal[1], as.is = TRUE))
  
  # apply the correct multiplier for the Land Surface
  # Temperature - you can check the values here on the LPDAAC
  # website (remember temperatures in K not C)
  hdf_layer <- hdf_layer * 0.02

  # mask Raster* with sf object (bounding box)
  # first define a polygon
  b <- as(raster::extent(23, 23.5, 40, 40.5), 'SpatialPolygons')
  raster::crs(b) <- "+init=epsg:4326"
  b <- sf::st_as_sf(b)
  
  # transform the polygon
  b <- sf::st_transform(b, sinus)
  
  # mask the hdf_layer (check outlier values)
  hdf_layer <- raster::mask(hdf_layer, b)
  
  # extract all data
  lst <- cellStats(hdf_layer, mean)
  
  # return all data
  return(list(
    tile = tile,
    year = year,
    doy = doy,
    land_surface_temperature = lst))
})

# flatten the output with a do.call
output <- do.call("rbind", output)

# print the first couple of lines
print(head(output))
```

