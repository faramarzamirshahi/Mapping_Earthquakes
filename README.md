# Mapping_Earthquakes
# Overview
The objective of this project is to use [Leaflet javascript api library]("https://unpkg.com/leaflet@1.7.1/dist/leaflet.css") to display earthquakes data on a map.<br>
Link to this library in the `head` section of `index.html`<br>
`<link rel="stylesheet" href="https://unpkg.com/leaflet@1.7.1/dist/leaflet.css" ... />`<br> 
The earhquake data is formatted as geoJSON and comes from the following source:<br>
[current week earthquake data](https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson)<br>
[current week major earthquake data with magnitude > 4.5 ](https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/4.5_week.geojson)<br>
<br>Use the following Mapbox Static Tiles API from mapplot to renders the base maps:<br>
[Street map](https://api.mapbox.com/styles/v1/mapbox/streets-v11.html?title=true&access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NDg1bDA1cjYzM280NHJ5NzlvNDMifQ.d6e-nNyBDtmQCVwVNivz7A#2/0.0/0.0)<br>
[Satellite map](https://api.mapbox.com/styles/v1/mapbox/satellite-streets-v11.html?title=true&access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NDg1bDA1cjYzM280NHJ5NzlvNDMifQ.d6e-nNyBDtmQCVwVNivz7A#2/0.0/0.0)<br>
[Dark map](https://api.mapbox.com/styles/v1/mapbox/dark-v10.html?title=true&access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NDg1bDA1cjYzM280NHJ5NzlvNDMifQ.d6e-nNyBDtmQCVwVNivz7A#2/0.0/0.0)<br>

Use the Leaflet tileLayer API to set up our base maps<br>
* `let streets = L.tileLayer('https://api.mapbox.com/styles/v1/mapbox/streets-v11/tiles/{z}/{x}/{y}?access_token={accessToken}', { ... });`
* `let satelliteStreets = L.tileLayer('https://api.mapbox.com/styles/v1/mapbox/satellite-streets-v11/tiles/{z}/{x}/{y}?access_token={accessToken}', {...});`
* `let dark = L.tileLayer('https://api.mapbox.com/styles/v1/mapbox/dark-v10/tiles/{z}/{x}/{y}?access_token={accessToken}', { ...});`
-
Create a map object in by referring to division specified in "index.html"
`<div id="mapid"></div>`
and set the default layer to `streets` tile
`let map = L.map('mapid', {center: [40.7, -94.5], zoom: 3, layers: [streets]});`
Create the layer object for the base maps. The base maps can be displayed only one at a time.
`let baseMaps = {"Streets": streets,"Satellite": satelliteStreets,"Dark":dark};`<br>
For overlays we need to define one one object for each layer, the overlays are displayed on top of the base layer.<br>
```
let allEarthquakes = new L.LayerGroup();
let tectonicPlates = new L.LayerGroup();
let majorEQ = new L.LayerGroup();
```
<br>
Define the overlay object

```
let overlays = {"Earthquakes": allEarthquakes, "Tectonic Plates":tectonicPlates, "Major Earthquakes":majorEQ};
```
The layers are all specifed in the layer control and we display it on the map with `addTo` method
`L.control.layers(baseMaps, overlays).addTo(map);`<br>
To display data in the map we use the `d3` javascript library, this is specified in the `<head>` section of `index.html'

`<script src="https://d3js.org/d3.v5.min.js"></script>`<br>
### Display earthquake data in "Earthquakes" layer
Use the `json` method of `d3` api to read earthquake data asynchronously

```
d3.json("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson").then(function(data) {
    // define various functions to the style and size markers are displayed
    function styleInfo(){...}; function getColor(){...}; function getRadius(){...};
    //use the Leaflet method to display in the desired overlay<br>
    `L.geoJson(data, {....}).addTo(allEarthquakes);`
})
```
Add "tectonic plates" and "major earthquakes" datasets by refactoring the snippet above
### Display legend
To display legend we use the Leaflet `control` method to create a legend control object<br>
specify where the legend should be displayed
`let legend = L.control({position: "bottomright"});`
define the content of the legend `legend.onAdd` in a function that returns an html `<div>` element
`legend.onAdd = function() {`<br>
+ Create an html division object<br>
+ Populate the inner html of this devision by:<br>
+ Defining two arrays<br>
++ one for the earth quake magnitudes and one for the display color<br>
+ Define the logic to relate the magnitude to the display color<br> in a for loop<br>
++ and populate the `div`<br>
+ return `div}` <br>

<br>
Finally add the legend to the map

`legend.addTo(map);`
