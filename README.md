# google-earth-engine
find NDVI through landsat 8
// Define the area of interest (AOI)
//var aoi = ee.Geometry.Rectangle([xmin, ymin, xmax, ymax]);
var geometry = 
    /* color: #98ff00 */
    /* displayProperties: [
      {
        "type": "rectangle"
      }
    ] */
    ee.Geometry.Polygon(
        [[[74.19579447887435, 35.94882382116793],
          [74.19579447887435, 35.84202508063599],
          [74.53499735973372, 35.84202508063599],
          [74.53499735973372, 35.94882382116793]]], null, false);

// Define the time range of interest
var start_date = ee.Date('2018-01-01');
var end_date = ee.Date('2022-12-31');

// Load the Landsat 8 Surface Reflectance collection
var l8sr = ee.ImageCollection('LANDSAT/LC08/C01/T1_SR')
  .filterBounds(geometry)
  .filterDate(start_date, end_date)
  .select(['B4', 'B5']);

// Define a function to calculate NDVI and add it as a band to the image
function addNDVI(image) {
  var ndvi = image.normalizedDifference(['B5', 'B4']).rename('NDVI');
  return image.addBands(ndvi);
}

// Apply the function to the image collection
var l8sr_ndvi = l8sr.map(addNDVI);

// Create a visualization layer for NDVI
var ndvi_vis = {min: -1, max: 1, palette: ['blue', 'white', 'green']};

// Display the NDVI layer on the map
Map.addLayer(l8sr_ndvi.select('NDVI'), ndvi_vis, 'NDVI');
