Step 1: **DATA**
================
The first step to building a map is getting your data in working order. Most databases don’t have geodata — polygon shapefiles or lat/lons — already included, so you'll have to join your data to a standard shapefile (.shp) with geolocation information. This example uses QGIS, an open/free alterantive to ArcGIS. 

To ensure everything works properly once you start working with your data in QGIS, clean up your .csv so that all of the data points that will be read as numbers don’t have any commas or extraneous text. Also, make sure any fields that you want to appear as text look exactly how you’d want them to look on your map. It’s annoying, but duplicating your data columns with both “real” and “string” fields is sometimes the easiest way to have the data you're using to style your map, also appear as numbers with commas in the map’s hover/teaser. To ensure QGIS reads your column fields correctly as either a "Real" number or "String," save a .csvt in the same folder with the same name as your .csv.

*See example files FY2012_Counties_Debt.csv and FY2012_Counties_Debt.csvt*

Step 2: **BUILD YOUR SHAPEFILE**
================================
Now you need to attach your data to the polygon shapes on your map. If you’re new to QGIS, find an online tutorial. Here’s a very basic walk-through:

Create a new project
Add your shapefile layer: Go to “Layer” > “Add a new vector layer…”
Select your .shp file. For our example, we’re using a Census TIGER/Line shapefile of Texas counties, ‘tl_2010_48_county10.shp’

*Note: It’s important to add your polygon .shp first, because the project will automatically pick up its projection layer. If you're not familiar with projection layers, and why they're important for mapping: https://www.ncjrs.gov/html/nij/mapping/ch1_7.html*

Add your data layer: Go to “Layer” > “Add a new vector layer…” and this time select your .csv file, ‘FY2012_Counties_Debt.csv’

Join the .csv to the .shp: Right click on the .shp layer, ‘tl_2010_48_county10,’ and select “Properties.” Go to the “Joins” tab and click “+” to add join the .csv to the .shp. 
	Select the following:
	“Join layer”: Name of the .csv layer.
	“Join field”: Column name in the .csv layer 
	“Target field: Column name in the .shp

*Note: If the columns are not of the same type, i.e. both “String” fields or “Real” fields, QGIS won’t be able to recognize that they’re the same thing, and the program won’t be able to join the files properly. You can check the field types for your columns by right-clicking the layer and selecting “Properties” > “Fields.” **This is why adding a .csvt file is important!** * 

Before moving on, check to make sure the join worked properly. Right click the .shp layer, select “Open Attributes Table,” and scroll over to make sure all of the columns/fields you wanted are there.

Next, right click the .shp layer and click “Save as…” and save your newly joined .shp as a .geojson. Make sure the projection/CRS matches the projection/CRS of your original .shp. In our case, that’s NAD83. 

