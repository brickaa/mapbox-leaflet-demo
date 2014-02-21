#Mapmaking! So fun.

This tutorial will teach you how to make a Mapbox/Leaflet map, complete with legend and hover text. If you have any questions or suggestions for improvement, tweet me @becca_aa.

##Step 1: *DATA*

The first step to building a map is getting your data in working order. Most databases don’t have geodata — polygon shapefiles or lat/lons — already included, so you'll have to join your data to a standard shapefile (.shp) with geolocation information. This example uses QGIS, an open/free alterantive to ArcGIS. 

To ensure everything works properly once you start working with your data in QGIS, clean up your .csv so that all of the data points that will be read as numbers don’t have any commas or extraneous text. Also, make sure any fields that you want to appear as text look exactly how you’d want them to look on your map. It’s annoying, but duplicating your data columns with both “real” and “string” fields is sometimes the easiest way to have the data you're using to style your map also appear as numbers with commas in the map’s hover/teaser. To ensure QGIS reads your column fields correctly as either a "real" number or "string" field, save a .csvt in the same folder with the same name as your .csv.

*See example files FY2012_Counties_Debt.csv and FY2012_Counties_Debt.csvt*

##Step 2: *BUILD YOUR SHAPEFILE*

Now you need to attach your data to the polygon shapes on your map. If you’re new to QGIS, find an online tutorial. Here’s a very basic walk-thru:

**Create a new project.**

**Add your shapefile layer:** 
Go to “Layer” > “Add a new vector layer…”

Select your .shp file. For our example, we’re using a Census TIGER/Line shapefile of Texas counties, ‘tl_2010_48_county10.shp’

***Note**: It’s important to add your polygon .shp first, because the project will automatically pick up its projection layer. If you're not familiar with projection layers, and why they're important for mapping: <https://www.ncjrs.gov/html/nij/mapping/ch1_7.html>*

**Add your data layer:** Go to “Layer” > “Add a new vector layer…” and this time select your .csv file, ‘FY2012_Counties_Debt.csv’

**Join the .csv to the .shp:** Right click on the .shp layer, ‘tl_2010_48_county10,’ and select “Properties.” Go to the “Joins” tab and click “+” to add join the .csv to the .shp. 

	Select the following:
	“Join layer”: Name of the .csv layer.
	“Join field”: Column name in the .csv layer 
	“Target field: Column name in the .shp

*Note: If the columns are not of the same type, i.e. both “String” fields or “Real” fields, QGIS won’t be able to recognize that they’re the same thing, and the program won’t be able to join the files properly. You can check the field types for your columns by right-clicking the layer and selecting “Properties” > “Fields.” **This is why adding a .csvt file is important!*** 

**Check to make sure the join worked properly before moving on.** Right click the .shp layer, select “Open Attributes Table,” and scroll over to make sure all of the columns/fields you wanted are there.

**Finally, save your newly joined .shp as a .geojson.** Right click the .shp layer and click “Save as…” (Make sure the projection/CRS matches the projection/CRS of your original .shp. In our case, the projection is "NAD83.") 

##Step 3: *STYLE* 
One of the cool things about Mapbox is you can style your maps in **TileMill**, a separate program that makes it easy to preview the styles on your map, add multiple layers and create interactive elements, including legends, hover text and teasers. When you're ready to publish, TileMill also converts your stylized map into a series of "tiles" or images that will load much, much faster on your website than a complicated .geojson file. *Pro-tip: Always err on the side of efficiency/faster load times.*
Mapbox has great examples of how to stylize maps in different ways using [CartoCSS](https://www.mapbox.com/tilemill/docs/manual/carto/) in TileMill. 
###*Ok, let's walk-thru of our example...*
**Open TileMill and create a new project.** TileMill will save your project locally under whatever "Filename" you use. *It's better not to use spaces.* But it's OK to use spaces in the "Name" assigned to the project.

*Note: TileMill loads every new project with a "countries" layer and basic styles for that layer. If you don't want this layer, which we won't, you can just delete it.*

**Create a new layer.** On the bottom left side of the screen, click the icon that has three rectangles layers on top of each other. 
*Notice the "#countries" layer matches the "#countries" CSS element in the style editor on the right.*

To add our geojson, click "+ Add Layer". Assign the layer an #ID. For our map, use "counties." For more advanced styling, you can also assign your layer a CSS .class. This is optional. Use browse to find your datafile, and select the geojson we just created: *'FY2012_Counties_Debt.geojson'*

Click "Save & Style."
**Bam!** Your shapefile should appear on the map, and on your right, you'll notice the #counties element has also appear on our style editor:
	#counties {
  		line-color:#594;
  		line-width:0.5;
  		polygon-opacity:1;
  		polygon-fill:#ae8;
		}

Green counties are cool and all, but let's make our map pretty.

**CartoCSS allows you to create conditional styles using the data in your geojson layer.** So let's style our map to show how the "County Debt Per Capita" compares in each county.

**First, you'll need to find the ID for the "County Debt Per Capita" feature in your geojson layer.** Click on the "layer" icon again. Next to "counties," click on the icon that looks like a table. Find the column that shows "County Debt Per Capita," it should be called *"FY2012_Counties_Debt_Debt_per_capita"* Copy the name of the column and close the table.

*Note: When you join files in QGIS, it will combine the names of your layers in the column titles. For shorter column titles, create shorter file names. We didn't do that in this example for clarity, but it's OK.*

Now, let's distinguish all of the counties that have $0 in debt per capita. Within your #counties, add the line: **[FY2012_Counties_Debt_Debt_per_capita = 0]{polygon-fill:#edf8fb}**
This means, for each row in our data where the Debt per Capita field equals 0, color that area #edf8fb. (I picked a color scale off [ColorBrewer](http://colorbrewer2.org/).) Let's use a color scale to fill in the rest of our counties:

	#counties {
  	line-color:#ccc;
 	 line-width:1;
 	 polygon-opacity:1;
 	 [FY2012_Counties_Debt_Debt_per_capita = 0] {polygon-fill:#edf8fb}
  	[FY2012_Counties_Debt_Debt_per_capita > 0] {polygon-fill:#b2e2e2}
  	[FY2012_Counties_Debt_Debt_per_capita > 100] {polygon-fill:#66c2a4}
  	[FY2012_Counties_Debt_Debt_per_capita > 500] {polygon-fill:#2ca25f}
  	[FY2012_Counties_Debt_Debt_per_capita > 1000] {polygon-fill:#006d2c}
	}

**Save and voila!**

We'll get to adding a legend in a moment... but, first...

##Step 4: *Interactivity*

Let's create a tooltip so that you can click on the map and find more information about a region.

**Click on the finger icon** above the layer icon on the bottom left. Here is where you can create templates for the legend, hover text or full text (what shows up when you click on the map.) 

To start, **select a layer to use for the interactivity** under the drop down menu. We'll select "counties." Now you can access data points using mustache tags around the specific field you want to reference. For example, {{{FY2012_Counties_Debt_Debt_per_capita}}} will call the data point for "Debt per Capita" for that region.

**Build the templates using basic < html >, CSS and your {{{mustache tags}}}.** 

Here's our example: 


	<div class='teaser'>
		<div class='title'>
			{{{FY2012_Counties_Debt_Issuer}}} County — <em>Pop. {{{FY2012_Counties_Debt_County_Population}}}</em>
		</div>
		<div class='info'>
			<dl>
				<dt>Total Outstanding Debt FY2012: {{{FY2012_Counties_Debt_Total_Debt_Outstanding}}}</dt>
				<dt>Outstanding Debt Per Capita: {{{FY2012_Counties_Debt_Total_Debt_Outstanding_per_capitastr}}}</dt>
			</dl>
		</div>
		<div class='debt-link'>
			<a href="{{{FY2012_Counties_Debt_Link}}}">{{{FY2012_Counties_Debt_Text_for_link}}}</a>
		</div>
	</div>
	
	<style type='text/css'>
	
	.teaser {
	    font-family: "HelveticaNeue-Light";
	}
	.teaser .title {
		text-align: left;
	    margin-bottom: .1em;
	    font-weight: bold;
	    font-size: 90%;
	}
	
	.teaser .info {
		text-align: left;
		margin-right: .1em;
		font-size: 80%;
	}
	.teaser .info-list dl {
	    margin-bottom: .1em;
	    padding: 0;
	    float: left;
	    list-style: none;
	}
	.teaser .info-list dl dt {
	    font-size: 80%;
	    list-style: none;
	    margin-left: 0;
	    line-height: 18px;
	    margin-bottom: .05em;
	}
	.teaser .debt-link a {
		font-size: 80%;
		margin-bottom: .1em;
		text-align: right;
		color: #379d92;
	}
	
	</style>
		


You can use this code in either your hover or full template. For our example, I've used it in both.

If you build a legend in TileMill, you'll have to add it as a separate layer. This is a perfectly legitimate option, but for this example, we're going to build the legend in the code on our page. Just for variety's sake. Ok, let's keep going.

##Step 5: *Export to Mapbox*
On the top right corner, click "Export," then select your export option. You can download your TileMill images to use any way you'd like. We're going to upload the map to Mapbox, which will host the images for us, and then pull them onto our page.

**Adjust your map settings.** To reduce the file size, you'll likely want to limit your zoom settings to whatever is appropriate for your map, and select the correct center point. For our example, I've set the zoom to be between 5 - 10.

##Step 6: *Add the map to your page*
A map in space is fine and dandy, but you want this thing on your website, right? OK, let's add it. If you're following along with my files, open *example1.html*. Looks like this:


	<!DOCTYPE html>
	
	<html>
	<head>
	    <meta charset="UTF-8">
	    <title>My First Leaflet Map!</title>
	
	    <meta name='viewport' content='width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no' />
	    <script src='https://api.tiles.mapbox.com/mapbox.js/v1.6.1/mapbox.js'></script>
	    <link href='https://api.tiles.mapbox.com/mapbox.js/v1.6.1/mapbox.css' rel='stylesheet' />
	
	    <style>
	      body { margin:0; padding:0; }
	      #map { position:absolute; top:0; bottom:0; width:75%; height:700px;}
	    </style>
	
	</head>
	<body>
	<div>
	
	 <div id='map'></div> 
	<!--  This <div> is your map. Put it in your html where you want the map to go. -->
	
	<script>
	
	//Add the background map layer
	// L.mapbox.map(element, id|url|tilejson, options)
	var map = L.mapbox.map('map', 'examples.map-9ijuk24y')
	    .setView([31.35, -99.64], 6)
	
	// Set the latitude and longitude of the map's starting view, and zoom level.
	
	// Add your custom data layer on top
	var layer = L.mapbox.tileLayer('texastribune.FY2012_Counties_Debt').addTo(map);
	
	</script>
	
	</body>
	</html>
	
	
Self-explanatory? Good. If not, here's a run-down: 

In the document header, I've added the Mapbox v1.6.1 library and stylesheet. This means our page will reference the library; we can use the Mapbox code to build our map, and it'll look pretty.

In the style, I've set basic parameters for our map height and width. You need these settings to control the basic look and placement of your map.

In the body, I've placed our map in a div tag. If you have lots of code, like paragraphs and what not, put this div wherever you want the map to go.

In the script, the first line sets a background map layer: 

	//Add the background map layer
	// L.mapbox.map(element, id|url|tilejson, options)
	var map = L.mapbox.map('map', 'examples.map-9ijuk24y')
	    .setView([31.35, -99.64], 6)

First we create the variable "map," and then use Leaflet "L.mapbox.map" to set that variable as 'examples.map-9ijuk24y' with a starting Lat/Lon view of "[31.35, -99.64]" on zoom level 6. 

Next, add the map we created as a layer on top of the background "map" layer.

	
	var layer = L.mapbox.tileLayer('texastribune.FY2012_Counties_Debt').addTo(map);
	
Open up the page on your browser and test it out. Looks wonderful! But our interactive hover/click function isn't working. What gives? We'll need to add that function as "gridlayer." 

	// Add interactive elements
	var gridlayer = L.mapbox.gridLayer('texastribune.FY2012_Counties_Debt');
	    //Create a gridlayer, set it to the 'map' id of your custom data layer
	map.addLayer(gridlayer);
	    //Append it to the map
	map.addControl(L.mapbox.gridControl(gridlayer, {follow: true}));
	    //Tell your map to autodetect the interactive elements from your gridlayer. 
	    
Now, let's add our legend. It's basically the same process, except instead of adding the html and CSS as a template on TileMill, we're going to put it directly on our page. Add the CSS to your style tag at the top of your page, and the html underneath your map. (You can also import your CSS as a stylesheet, but for simplicity sake, we're not going to do that here.) Should look like: 
	
	    <style>
	      body { margin:0; padding:0; }
	      #map { position:absolute; top:0; bottom:0; width:75%; height:700px;}
	        .legend {
	            font-family: "HelveticaNeue-Light";
	        }
	         .legend .legend-title {
	            text-align: center;
	            margin-bottom: 3px;
	            font-weight: bold;
	            font-size: 90%;
	            }
	          .legend .legend-subtitle {
	            text-align: center;
	            margin-bottom: 5px;
	            font-weight: bold;
	            font-size: 70%;
	            }
	          .legend .legend-scale ul {
	            margin: 0;
	            margin-bottom: 5px;
	            padding: 0;
	            float: left;
	            list-style: none;
	            }
	          .legend .legend-scale ul li {
	            font-size: 80%;
	            list-style: none;
	            margin-left: 0;
	            line-height: 18px;
	            margin-bottom: 2px;
	            }
	          .legend ul.legend-labels li span {
	            display: block;
	            float: left;
	            height: 16px;
	            width: 30px;
	            margin-right: 5px;
	            margin-left: 0;
	            border: 1px solid #999;
	            }
	          .legend .legend-source {
	            font-size: 70%;
	            color: #999;
	            clear: both;
	            }
	          .legend a {
	            color: #777;
	            }
	    </style>
	
	</head>
	<body>
	<div>
	
	 <div id='map'></div> 
	<!--  This <div> is your map. Put it in your html where you want the map to go. -->
	
	<div id='legend-content' style='display: none;'>
	<div class='legend'>
	<div class='legend-title'>Oustanding Debt Per Capita</div>
	<div class='legend-subtitle'>Fiscal Year 2012</div>
	<div class='legend-scale'>
	  <ul class='legend-labels'>
	    <li><span style='background:#edf8fb;'></span>$0</li>
	    <li><span style='background:#b2e2e2;'></span>$1-$100</li>
	    <li><span style='background:#66c2a4;'></span>$101-$500</li>
	    <li><span style='background:#2ca25f;'></span>$501-$1,000</li>
	    <li><span style='background:#006d2c;'></span>>$1,000</li>
	  </ul>
	</div>
	<div class='legend-source'>Source: <a href="http://www.texastransparency.org/Data_Center/Search_Datasets.php">Texas Transparency</a></div>
	</div>
	</div>
	</div>
	...

Then go back to your script. You're going to add the legend directly to your "map" variable, and tell it to put the html code with the id "legend-content" on top of that layer. It will know to pull the html code for our legend, because it's contained in a div with the id='legend-content'

	// Add your legend
	map.legendControl.addLegend(document.getElementById('legend-content').innerHTML);
	
##Step 8: *Adding Markers*	
Like your legend, marker layers can also be added and styled in TileMill. But here's an example of how you'd load them directly in your script. 

**Create your marker(s): **

	var geojson = [
	  {
	    "type": "Feature",
	    "geometry": {
	      "type": "Point",
	      "coordinates": [-97.75, 30.25]
	    },
	    "properties": {
	      "title": "Austin! Best City Ever",
	      "marker-color": "#fc4353",
	      "marker-size": "large",
	      "marker-symbol": "circle"
	    }
	  } 
	]; 
		
After you've created your "map" layer, add your markers to that layer:

	//Create Marker Layer
	map.markerLayer.setGeoJSON(geojson);
		
##Step 7: *Final Touches*
Check out our map online. Looks pretty freakin' sweet, eh? Zoom out a little bit. Oh no! It disappeared! That's because we only created tiles for zoom levels 5-10. Let's add a few more settings to make sure things that that don't happen.

	var map = L.mapbox.map('map', 'examples.map-9ijuk24y', {
	    minZoom: 5,
	    maxZoom: 10,
	    zoomControl: true
	}).setView([31.35, -99.64], 6);
	 
There are a whole host of options, and these are very basic ones. But now you know where to start!

#Congrats, you made your first map!

 