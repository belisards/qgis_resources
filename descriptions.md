Notes by Alexandra Kanik (LEDE 2021).

# Types of geographic files
## Delimited text files
Delimited text files, like .csv and .tsv, generally hold point data or attribute data. 

Point data contain latitude and longitude values that allow mapping programs to plot them on a map.

Attribute data are not in-and-of themselves geographic, meaning that alone they are incapable of being displayed on a map. However, attribute datasets contain columns that can be joined on to geographic data in order to give meaning and style to the geographic element. 

For example, you will be able to download Census tract data here. But if you were to add that data to a map using QGIS or ArcGIS, you would find that there are no data associated with the shapes. They’re just shapes. You need to also download Census or other data and join the data to the tracts in order to use them fully.

## Shapefiles
Shapefiles are one of the more common mapping file types. Though some systems may make it seem like a shapefile is a single file, shapefiles are actually comprised of at least 3 different files that work in tandem to produce the mapped element. 

`.shp` file containing the feature geometries
`.dbf` file containing the attributes in dBase format
`.shx` index file (which basically means it loads a lot faster)

There are other files that may be present with a shapefile, notably:

`.prj` file containing projection information
`.cpg` file containing character encoding information for the data attributes

Here is a full listing of all files that may comprise a single shapefile.

Each file comprising a shapefile will have the exact same filename. Only the extension will vary.

It is crucial to NEVER separate the files that comprise a shapefile. Your shapefile will stop working or be seriously damaged if you do. Keep all shapefile files in the same directory. 

Pro tip: Shapefiles often come as a packaged .zip file. When you open the .zip file, all of your shapefile files will be contained within a directory, generally one that has the same name as the shapefile files themselves. Just keep it in there. Additionally, keep the original .zip file in case you damage or corrupt your opened shapefile.

There are noticeable setbacks to storing data in shapefile format. 

QGIS’s data attribute storage system only allows attribute headers (column names) to be 10 character maximum. If you import data with headers longer than 10 characters, they will be shortened for you. That may not seem like such a big deal but consider dealing with columns like:
pop_16under_white
pop_16under_black
pop_16under_asian
pop_16under_native

Each column would import as `pop_16unde`. Among other reasons, this is why you will commonly see human-readable column names replaced by codes. The Census Bureau is fond of doing this.

**Other issues:**
You can also only have 255 fields in a shapefile. This is not generally a problem until it is. 
Date fields are accepted but time formats are not. 
You can only have one data type per shapefile. For example, you can’t store points and polygons with a single shapefile.

## geoJSON
geoJSON is a type of JSON file that contains geographic data and encodings. Because of this, you can open a geoJSON file with any text or code editor.

Unlike the shapefile with its many files, all the data required to map a geoJSON element is contained within the single .geojson file.

Though more compact, geoJSON files are less efficient in the storage of data and they result in larger file sizes than shapefiles.

## geoPackage
geoPackage is an open source file format that was developed as a fix for many of the issues exhibited by shapefiles. 

geoPackages are lighter files, they load faster, allow you to mix data types and have full column names. You can also analyze the using common analysis tools such as R, Python and SQLite.

## KML/KMZ
The KML (and KMZ) file types were developed by Google (technically Keyhole Inc which was later acquired by Google) for use in their Google Earth products. 

KML files are a single file, like geoJSON, however they are capable of containing a variety of feature types (points, lines, polygons). They are also much more efficient (size and load time) than geoJSON.

## geoTIFF
geoTIFF are raster map files that assign georeferenced data to rasterized (not vector) images like satellite tiles.

geoTIFF files can be extremely large. You will often see geoTIFF files made available by weather and space companies like NASA and NOAA.

# Data classification

**Equal Count (Quantile)**: each bin will have the same number of elements. For example, if you want your data broken into 5 bins, and you have 50 features you’re styling, you would have 10 features in each bin.

**Equal Interval**: each bin will have the same size. For example, if you have features with values from 1 to 16 and four classes, each class will have a size of four: 1 - 4, 4 - 8, 8 - 12 and 12 - 16. Bins may contain unequal numbers of features, unlike with quantile mode.

**Logarithmic scale**: suitable for data with a wide range of values. Narrow classes for low values and wide classes for large values. For example, for decimal numbers with range and two bins, the first bin will be from 0 to 10 and the second bin from 10 to 100). Read more about logarithmic scales and when to use them.

**Natural Breaks (Jenks)**: the variance within each bin is minimized while the variance between bins is maximized. As a result, this classification method is good at identifying clusters in the data. 

**Pretty Breaks**: bin ranges are chosen so that they are 1, 2 or 5 times a power of 10. As a result, pretty breaks does not always adhere to the number of classes or bins you choose.

**Standard Deviation**: bins are built depending on the standard deviation of the values. First the mean is calculated, then each standard deviation from that mean becomes a bin.

## Useful links

[When Should I Use Logarithmic Scales in My Charts and Graphs?](https://www.forbes.com/sites/naomirobbins/2012/01/19/when-should-i-use-logarithmic-scales-in-my-charts-and-graphs/?sh=77e7cc7e5e67)

[Choropleth Maps – A Guide to Data Classification](https://gisgeography.com/choropleth-maps-data-classification/)

# Projections

Projections are how we use two-dimensional space to talk about three-dimensional space. Polygons like county and state borders are three-dimensional, but we do our best to show them in two dimensions in print and online.

A coordinate reference system defines how projected space reflects real space. 

If you want to do some more in depth reading on QGIS and projections, [you can read their documentation here](https://docs.qgis.org/3.16/en/docs/user_manual/working_with_projections/working_with_projections.html).

Geographic vs. projected: points vs. polygons
The best way I’ve ever heard the differentiation between geographic coordinate system (GCS) and projected coordinate systems (PCS) explained is from [Heather Smith at the Arc Blog](https://www.esri.com/arcgis-blog/products/arcgis-pro/mapping/gcs_vs_pcs/):

- A GCS defines where the data is located on the earth’s surface.
- A PCS tells the data how to draw on a flat surface, like on a paper map or a computer screen.

When you’ve got points, whose only definition are location, you want a GCS. When you’ve got lines or polygons, you need to know how to draw the shape on a flat surface, so you need a PCS.

## Which projection should I use and when?
QGIS supports over 7,000 coordinate reference systems, both GCS and PCS. So how do you know which to use and when?

One consideration is visual. A layer’s CRS defines how it will appear on a 2D map. Another consideration is alignment. If you are trying to do geospatial analyses with multiple layers, you need to make sure your layers are in the same projection. 

In recent years, QGIS has made great advances in how it works with projection. Currently, when you import a file into QGIS, the system determines the correct projection for that layer based on system settings. If you haven’t changed these settings, your mapping projections are likely set by the first layer that you import into your map.

From QGIS docs:

“QGIS supports “on the fly” CRS transformation for both raster and vector data. This means that regardless of the underlying CRS of particular map layers in your project, they will always be automatically transformed into the common CRS defined for your project. Behind the scenes, QGIS transparently reprojects all layers contained within your project into the project’s CRS, so that they will all be rendered in the correct position with respect to each other!”

# Geocoding

Google is probably the best geocoder out there, however their geocoding API policy states that “It is prohibited to use Geocoding API data on a map that is not a Google map.” This is rarely something I want to do, so I choose to use other services.

Bing also has a very good geocoding service and they expressly say that you can use their geocode results on third-party maps. That being said, their service requires knowledge of code in order to access an API. This is not always desirable.

Census has a free, unlimited geocoding service that does not require coding knowledge. However, their results can be less accurate than other geocoding services offer, especially when the address you’re trying to convert are messy.

Geocod.io is an online geocoding service that sources location data from over 1,600 public sources. As of the writing of this documentation, they are my go-to choice for a quick, reliable geocoder. Geocod.io gives you 2,500 free lookups a day and offers both an API and a non-code way of submitting and looking up addresses.

## Useful links

[Geocoding providers](https://github.com/DenisCarriere/geocoder#providers)
[Soma's tutorial](https://github.com/jsoma/NICAR20-geocoding-apis)