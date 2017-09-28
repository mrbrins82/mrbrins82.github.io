---
layout: default
---
# [](#header-1)[Home](index) || [Research](research) || [<font color="MediumSlateBlue">Projects</font>](projects)
<br/>

* * * 

## [](#header-2)Projects

*   [Twitter Streaming & Sentiment Analysis for<br/>Game 7 of the NHL Eastern Conference Finals:<br/>Penguins vs. Senators](hockeytweets)<br/>
*   [(Kaggle) Titanic: Machine Learning from Disaster](kaggle_titanic)<br/>
*   [(Kaggle) House Prices: Advanced Regression Techniques](kaggle_houseprices)<br/>
*   <font color="MediumSlateBlue">(Kaggle) Personalized Medicine: Redefining Cancer Treatment</font>


* * * 

<style>

<center>body {
  font: 10px sans-serif;
}

#container {
  width: 700px;
  height: 350px;
  overflow: hidden;
}
</center>
</style>
<div id="container">
  <canvas id="map"></canvas>
</div>
<script>

var canvas = d3.select("#map").call(drag),
    context = canvas.node().getContext("2d");

var w = 960,
	h = 500,
	lon = -122.41948,
	lat = 37.76487;

var project = d3.geo.mercator()
	.scale(1 / (2 * Math.PI))
	.translate([.5, .5]);

var view = pixymaps.view()
	.size([w, h])
	.center(project([lon, lat]))
	.zoom(12);

var image = pixymaps.image()
	.view(view)
	.url(pixymaps.url("http://{S}tile.cloudmade.com"
	+ "/1a1b06b230af4efdbb989ea99e9841af" // http://cloudmade.com/register
	+ "/999/256/{Z}/{X}/{Y}.png")
	.hosts(["a.", "b.", "c.", ""]))
	.render(canvas.node());

function drag(selection) {
  var p0;

  selection
  .on("mousedown", mousedown);

  d3.select(window)
  .on("mousemove", mousemove)
  .on("mouseup", mouseup);

  function mousedown() {
	p0 = [d3.event.pageX, d3.event.pageY];
	d3.event.preventDefault();
  }

  function mousemove() {
	if (p0) {
	  var p1 = [d3.event.pageX, d3.event.pageY];
	  view.panBy([p1[0] - p0[0], p1[1] - p0[1]]);
	  image.render(canvas.node());
	  p0 = p1;
	  d3.event.preventDefault();
	}
  }

  function mouseup() {
	if (p0) {
	  p0 = null;
	  d3.event.preventDefault();
	}
  }
}

</script>
<div id="copy">
  &copy; 2011
  <a href="http://www.cloudmade.com/">CloudMade</a>,
  <a href="http://www.openstreetmap.org/">OpenStreetMap</a> contributors,
  <a href="http://creativecommons.org/licenses/by-sa/2.0/">CCBYSA</a>.
</div>
