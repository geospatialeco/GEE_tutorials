![Shaun Levick](Logo2.PNG)
GEARS - Geospatial Ecology and Remote Sensing lab - https://www.geospatialecology.com

# Environmental Monitoring and Modelling
Lab 2 - Getting deeper into Earth Engine
--------------

### Acknowledgments
- Google Earth Engine Team
- Earth Engine Beginning Curriculum

------

## 1. The Earth Engine code editor

![Figure 1. The Google Earth Engine environment](gee_editor.PNG)


1. Editor Panel
	- The Editor Panel is where you write and edit your Javascript code
2. Right Panel
	- Console tab for printing output.
	- Inspector tab for querying map results.
	- Tasks tab for managing long­ running tasks.
3. Left Panel
	- Scripts tab for managing your programming scripts.
	- Docs tab for accessing documentation of Earth Engine objects and methods, as well as a few specific to the Code Editor application
	- Assets tab for managing assets that you upload.
4. Interactive Map
	- For visualizing map layer output
5. Search Bar
	- For finding datasets and places of interest
6. Help Menu
	- User guide ­ reference documentation
	- Help forum ­ Google group for discussing Earth Engine
	- Shortcuts ­ Keyboard shortcuts for the Code Editor
	- Feature Tour ­ overview of the Code Editor
	- Feedback ­ for sending feedback on the Code Editor
	- Suggest a dataset. Our intention is to continue to collect datasets in our public archive
and make them more accessible, so we appreciate suggestions on which new datasets we should ingest into the Earth Engine public archive.

---------

## 2. Basic JavaScript commands


```JavaScript
// Line comments start with two forward slashes. Like this line.

/* Multi line comments start with a forward slash and a star,
and end with a star and a forward slash. */

Variables are used to store objects, and are defined using the keyword var.
var the_answer = 42;

String objects start and end with a single quote.
var my_variable = 'I am a string';

String objects can also start and end with double quotes.
// But don't mix and match them.
var my_other_variable = "I am also a string";

Statements should end in a semi-colon, or the editor complains.
var test = 'I feel incomplete...'

Parentheses are used to pass parameters to functions.
print('This string will print in the Console tab.');

Square brackets are used for items in a list.  The zero index refers to the first item in the list.
var my_list = ['eggplant', 'apple', 'wheat'];
print(my_list[0]);

Curly brackets (or braces) can be used to define dictionaries (key:value pairs).
var my_dict = {'food':'bread', 'color':'red', 'number':42};

Square brackets can be used to access dictionary items by key.
print(my_dict['color']);

Or you can use the dot notation to get the same result.
print(my_dict.color);
```

--------

## 3. Getting started with images

1. Clear the script by selecting "Clear script" from the Reset button dropdown menu.
2. Search for “elevation” and click on the SRTM Digital Elevation Data 30m result to show the dataset description.
3. Click on Import, which moves the variable to the Imports section at the top of your script. Rename the default variable name "image" to be "srtm".
4. Add the image object to the map with the script:

```JavaScript
print(srtm);
```

5. Browse through the information that was printed. Open the “bands” section to show the one band named “elevation”. Note that all this same information is automatically available for all variables in the Imports section.
6. Use the Map.addLayer() method to add the image to the interactive map. We will start simple, without using any of the optional parameters.

```JavaScript
Map.addLayer(srtm);
```

The displayed map will look pretty flat grey, because the default visualization parameters map the full 16­bit range of the data onto the black–white range, but the elevation range is much smaller than that in any particular location. We’ll fix it in a moment.

7. Select the Inspector tab. Then click on a few points on the map to get a feel for the elevation in this area. Finally, set visualization parameters:

```JavaScript
Map.addLayer(srtm, {min: 0, max: 1000});
```

[Script link](https://code.earthengine.google.com/346d11d7fdbfd462a32994deefbd1e85)

----------

## 4. Applying a computation to an image

1. Pan over to the Kakadu National Park region, where there are some nice elevation differences.
2. Next add a simple computation, for example a threshold on elevation.

```JavaScript    
      var high = srtm.gt(200);
      Map.addLayer(high, {}, 'above 200m');
```

3. Do another computation to compute slope from the elevation data and display it on the map as a separate layer. Also add a third parameter to the addLayer() method, which names the layer.

```JavaScript
var slope = ee.Terrain.slope(srtm);
Map.addLayer(slope, {min: 0, max: 60}, 'slope');
```

4. Layers added to the map will have default names like "Layer 1", "Layer 2", etc. To improve the readability, give each layer a human­readable name.

```JavaScript
var slope = ee.Terrain.slope(srtm);
Map.addLayer(srtm, {min: 0, max: 1000}, 'DEM');
Map.addLayer(slope, {min: 0, max: 60}, 'slope');
```

[Scipt Link](https://code.earthengine.google.com/cb0385ce64d5bf1669f012e689aa91ae)

Things to try:

- Search for a specific location
- Use the inspector to estimate the value range
- Adjust the visualization parameters
- Look at the documentation tab (i.e. Map.addLayer)
- Try the context­ specific help (Ctrl­ space)

------

##5. Apply a spatial reducer
1. Select the polygon geometry tool and draw a triangle (or more complex polygon) on the map.
2. Print the mean value for the region.

```javascript
Map.addLayer(srtm, {min: 0, max: 1000}, 'DEM'); Map.addLayer(geometry);
var dict = srtm.reduceRegion({
        reducer: 'mean',
        geometry: geometry,
        scale: 90
});
      print('Mean elevation', dict);
```

3. Clear the workspace by clicking Reset ­> Clear script

[Script Link](https://code.earthengine.google.com/a0dc428620e5bd73086eeb5fbbed7121)


Things to try:
- Discuss the return type of reduceRegion (Dictionary).
- Try out different reducers: min, max, etc.
- Try out different geometry types: point, line

-------

##6. Load and filter an image collection
1. Delete the srtm object from the Import section, by rolling your cursor over the object, then clicking on the trash can icon.
2. Search for “Landsat 8 toa” and add the result to the imports section. Rename the collection "L8".
3. Apply a date filter to the image collection, using the filterDate() method.

```javascript
var filtered = L8.filterDate('2013-04-15', '2013-04-20');
Map.addLayer(filtered);
```


[Script Link](https://code.earthengine.google.com/f5677fa228ae2944a23d4277f5ed41dc)

-----

##7. Play with image bands

1. With the default visualization parameters, the data looks dark and the colors look wrong. Pick better visualization parameters.

```javascript
var filtered = L8.filterDate('2013-04-15', '2013-04-20');
Map.addLayer(filtered, {min: 0, max: 0.3, bands:['B4', 'B3', 'B2']});
```

2. Copy and paste the second line and modify the bands to create the classic false­color look, with vegetation highlighted in red, and demonstrate giving each layer a human­ readable name.

```javascript
var filtered = L8.filterDate('2013-04-15', '2013-04-20');
Map.addLayer(filtered, {min: 0, max :0.3, bands:['B4', 'B3', 'B2']}, 'RGB');
Map.addLayer(filtered, {min: 0, max: 0.3, bands:['B5', 'B4', 'B3']}, 'False Color');
```

3. We’re going to use these visualization parameters a lot, so pull them out into a variable.

```javascript
var rgb_vis = {min: 0, max: 0.3, bands:['B4', 'B3', 'B2']};
var filtered = L8.filterDate('2013-04-15', '2013-04-20');
Map.addLayer(filtered, rgb_vis, 'RGB');
```

4. You can also use the visualization dialog to select appropriate visualization parameters.
	- Zoom over an area of open ocean (for example, the channel islands off the coast of Los Angeles)
	- Click on the layer picker, then on the setting icon to the right of the layer opacity slider.
	- Select a Range Stretch method, and then apply it to the image.


[Script Link](https://code.earthengine.google.com/f31a1607692568c097458426a4dbfe43)

--------

##8. Reducing image collections

1. Expand out the date range.

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
var filtered = L8.filterDate('2014-01-01', '2015-01-01');
Map.addLayer(filtered, rgb_vis, 'RGB');
```

2. Show that you get the same result if you mosaic() the filtered collection
```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
var filtered = L8.filterDate('2014-01-01', '2015-01-01');
Map.addLayer(filtered.mosaic(), rgb_vis, 'RGB');
```

3. Show the results of using the median() reducer

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
var filtered = L8.filterDate('2014-01-01', '2015-01-01');
Map.addLayer(filtered, rgb_vis, 'RGB');
Map.addLayer(filtered.median(), rgb_vis, 'RGB - median reducer');
```

[Script Link](https://code.earthengine.google.com/ea9e8f946f9471855b01861c7fce52d2)

---------

##9. Isolate an Image

1. Narrow the time range to a 7­-10 day window.

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
var filtered = L8.filterDate('2015-09-01', '2015-10-30');
Map.addLayer(filtered, rgb_vis, 'RGB');
```

2. Add a Region of Interest (ROI) to the map, by using the point geometry tool. In the Imports section, rename the object to "roi".
3. Add an additional filter to limit it to images covering the ROI. Note that the resulting filtered object is an image collection which may contain one or more images.
```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
var filtered = L8.filterDate('2015-09-01', '2015-10-30')
        .filterBounds(roi);
      Map.addLayer(filtered, rgb_vis, 'RGB');
```

4. Extract a sample image from the image collection, and add it as a layer.

```javascript
      var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
      var filtered = L8.filterDate('2015-09-01', '2015-10-30')
        .filterBounds(roi);
var image = ee.Image(filtered.first());
Map.addLayer(image, rgb_vis, 'RGB');
```

[Script Link](https://code.earthengine.google.com/cc07cd2148b16dccd525705048a8a8bf)


Extra things to do:
- Sort the filtered collection by 'CLOUD_COVER'

---------

##10. Compute NDVI
1. Using the .select() method, pick out the NIR and red bands, and do some math the “hard” way by hand.
```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
var filtered = L8.filterDate('2015-09-01', '2015-10-30')
.filterBounds(roi);
var image = ee.Image(filtered.first());
var red = image.select('B4');
var nir = image.select('B5');
var ndvi = nir.subtract(red).divide(nir.add(red));
Map.addLayer(image, rgb_vis, 'RGB');
Map.addLayer(ndvi, {min: 0, max: 1}, 'NDVI');
```

2. Find the ee.Image.normalizedDifference() method in the docs. Use it to simplify the script.

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
var filtered = L8.filterDate('2015‐09‐01', '2015‐10‐30')
.filterBounds(roi);
var image = ee.Image(filtered.first());
var ndvi = image.normalizedDifference(['B5', 'B4']);
Map.addLayer(image, rgb_vis, 'RGB');
Map.addLayer(ndvi, {min: 0, max: 1}, 'NDVI');
```

[Script Link](https://code.earthengine.google.com/b4f6cbe2362beca66a6dd906f92ec551)

---------

##11. Write a Function
Our next goal is to calculate NDVI for a collection of images. To do so, we first need to refactor (rewrite) our code to using a function, which can then be applied to all images in a collection.

1. Start by writing a function that adds a band with NDVI data to an image:

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
    function addNDVI(image) {
    var ndvi = image.normalizedDifference(['B5', 'B4']);
    return image.addBands(ndvi);
    }
    var filtered = L8.filterDate('2015-09-01', '2015-10-30')
       .filterBounds(roi)
    var image = ee.Image(filtered.first());
    var ndvi = addNDVI(image);
    Map.addLayer(image, rgb_vis, 'RGB');
    Map.addLayer(ndvi, {min: 0, max: 1}, 'NDVI');
```

2. Click on the Inspector tab, then on the image. Look in the Inspector tab results to see that the code has added a band called “nd”.
3. Note that the image has change, because the first band (B1) is being displayed by default, instead of the "nd" band. Add a visualization parameter to correct this:

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
function addNDVI(image) {
    var ndvi = image.normalizedDifference(['B5', 'B4']);
    return image.addBands(ndvi);
    }
    var filtered = L8.filterDate('2015-09-01', '2015-10-30')
       .filterBounds(roi);
var image = ee.Image(filtered.first());
var ndvi = addNDVI(image);
Map.addLayer(image, rgb_vis, 'RGB');
Map.addLayer(ndvi, {bands: 'nd', min: 0, max: 1}, 'NDVI');
```

[Script Link](https://code.earthengine.google.com/6a560cb635de3fe260b3772f2f02e7f4)


---------

##12. Map a Function over a Collection
1. Now that we have a function, we will 'map' the function across the filtered Landsat 8 collection.

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
function addNDVI(image) {
    var ndvi = image.normalizedDifference(['B5', 'B4']);
    return image.addBands(ndvi);
}
var filtered = L8.filterDate('2015-09-01', '2015-10-30')
   .filterBounds(roi);
var with_ndvi = filtered.map(addNDVI);
Map.addLayer(filtered, rgb_vis, 'RGB');
Map.addLayer(with_ndvi, {bands: 'nd', min: 0, max: 1}, 'NDVI');
```

2. Using the inspector tab, click on the map to show that each image now has an ‘nd’ band containing the NDVI.
3. Get rid of the bounds filter, so that all areas in the viewport are used.
4. To get rid of some clouds, expand out the time range and switch to using a median reducer.

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
function addNDVI(image) {
   	var ndvi = image.normalizedDifference(['B5', 'B4']);
    return image.addBands(ndvi);
}
var filtered = L8.filterDate('2015-01-01', '2015-10-30');
var with_ndvi = filtered.map(addNDVI);
Map.addLayer(filtered.median(), rgb_vis, 'RGB');
Map.addLayer(with_ndvi.median(), {bands: 'nd', min: 0, max: 1}, 'NDVI');
```

[Script Link](https://code.earthengine.google.com/58d5c94fb654846fcf9eac0295695a83)


--------

##13. Build a Greenest­ Pixel Composite

1. We will now work on an even better way to produce cloud­ free mosaics. The ee.ImageCollection.qualityMosaic() method can be used to order image bands, on a pixel­ by ­pixel basis, by a specified metric (we will use NDVI).

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
function addNDVI(image) {
	var ndvi = image.normalizedDifference(['B5', 'B4']);
    return image.addBands(ndvi);
}
var filtered = L8.filterDate('2015-01-01', '2015-10-30');
var with_ndvi = filtered.map(addNDVI);
Map.addLayer(filtered.median(), rgb_vis, 'RGB (median)');
var greenest = with_ndvi.qualityMosaic('nd');
Map.addLayer(greenest, rgb_vis, 'RGB (greenest pixel)');
```

2. If your chosen location doesn’t make for a decent max­NDVI composite, you can fly somewhere interesting - like a rainforest belt. Try the Daintree area in the vicinity of Cairns, Australia.

[Script Link](https://code.earthengine.google.com/30003261c641d6bf0fa9a917f7143ade)


--------

##14. Chart NDVI over Time

1. Click on the ROI point that was added earlier, and then drag it to an agricultural field. Add the following line to make a chart of NDVI over time for your ROI.

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
function addNDVI(image) {
    var ndvi = image.normalizedDifference(['B5', 'B4']);
    return image.addBands(ndvi);
}
var filtered = L8.filterDate('2015-01-01', '2015-10-30');
var with_ndvi = filtered.map(addNDVI);
var greenest = with_ndvi.qualityMosaic('nd');
Map.addLayer(filtered.median(), rgb_vis, 'RGB (median)');
Map.addLayer(greenest, rgb_vis, 'RGB (greenest pixel)');
print(Chart.image.series(with_ndvi.select('nd'), roi));
```

2. Try out the interactivity of the chart by hovering, expand it to full screen, and testing out the SVG/PNG/CSV download buttons.

[Script Link](https://code.earthengine.google.com/dce9f1f9a0b4f8ce309b8337d1ab6286)

--------

##15. Export an RGB Image

The source data that you work with can have many different characteristics (single­band, multispectral, high ­bit ­depth, etc.), and the visualization tools in Earth Engine allow you to display the data in a variety of ways. You can also export the data in a variety of ways, such as a multi­band image. This section will demonstrate how to export a 3­band (RGB) 8­bit image that can be easily displayed in other tools outside of Earth Engine.

1. Start by creating an 8­bit RGB image object, using the ee.Image.visualize() command, and add it to the interactive map

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
function addNDVI(image) {
    var ndvi = image.normalizedDifference(['B5', 'B4']);
    return image.addBands(ndvi);
}
var filtered = L8.filterDate('2015-01-01', '2015-10-30');
var with_ndvi = filtered.map(addNDVI);
var greenest = with_ndvi.qualityMosaic('nd');
var rgb = greenest.visualize(rgb_vis);
Map.addLayer(rgb,{}, 'RGB');
```

2. Export the image (by default to your Google Drive account).

```javascript
var rgb_vis = {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']};
function addNDVI(image) {
    var ndvi = image.normalizedDifference(['B5', 'B4']);
    return image.addBands(ndvi);
}
var filtered = L8.filterDate('2015-01-01', '2015-10-30');
var with_ndvi = filtered.map(addNDVI);
var greenest = with_ndvi.qualityMosaic('nd');
var rgb = greenest.visualize(rgb_vis);
Map.addLayer(rgb, {}, 'RGB (greenest pixel)');
Export.image(rgb, 'GreenestPixel');
```

3. Look at the Docs tab for the Export object, and check out the other things that you can export. Tables! Videos!

[Script Link](https://code.earthengine.google.com/f6e9d5f20ed15cdf191f8a2907fced59)


-----------
GEARS - Geospatial Ecology and Remote Sensing lab - https://www.geospatialecology.com
