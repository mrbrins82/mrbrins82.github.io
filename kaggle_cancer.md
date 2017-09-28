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

<meta charset="utf-8">
<style>
<center>
form {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  position: absolute;
  left: 10px;
  top: 10px;
}

label {
  display: block;
}
</center>
</style>
<form>
  <label><input type="radio" name="mode" value="grouped"> Grouped</label>
  <label><input type="radio" name="mode" value="stacked" checked> Stacked</label>
</form>
<svg width="650" height="400"></svg>
<script>

var n = 4, // The number of series.
    m = 58; // The number of values per series.

// The xz array has m elements, representing the x-values shared by all series.
// The yz array has n elements, representing the y-values of each of the n series.
// Each yz[i] is an array of m non-negative numbers representing a y-value for xz[i].
// The y01z array has the same structure as yz, but with stacked [y₀, y₁] instead of y.
var xz = d3.range(m),
    yz = d3.range(n).map(function() { return bumps(m); }),
    y01z = d3.stack().keys(d3.range(n))(d3.transpose(yz)),
    yMax = d3.max(yz, function(y) { return d3.max(y); }),
    y1Max = d3.max(y01z, function(y) { return d3.max(y, function(d) { return d[1]; }); });

var svg = d3.select("svg"),
    margin = {top: 40, right: 10, bottom: 20, left: 10},
    width = +svg.attr("width") - margin.left - margin.right,
    height = +svg.attr("height") - margin.top - margin.bottom,
    g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");

var x = d3.scaleBand()
    .domain(xz)
    .rangeRound([0, width])
    .padding(0.08);

var y = d3.scaleLinear()
    .domain([0, y1Max])
    .range([height, 0]);

var color = d3.scaleOrdinal()
    .domain(d3.range(n))
    .range(d3.schemeCategory20c);

var series = g.selectAll(".series")
  .data(y01z)
  .enter().append("g")
  .attr("fill", function(d, i) { return color(i); });

var rect = series.selectAll("rect")
  .data(function(d) { return d; })
  .enter().append("rect")
  .attr("x", function(d, i) { return x(i); })
  .attr("y", height)
  .attr("width", x.bandwidth())
  .attr("height", 0);

rect.transition()
    .delay(function(d, i) { return i * 10; })
    .attr("y", function(d) { return y(d[1]); })
    .attr("height", function(d) { return y(d[0]) - y(d[1]); });

g.append("g")
    .attr("class", "axis axis--x")
    .attr("transform", "translate(0," + height + ")")
    .call(d3.axisBottom(x)
    .tickSize(0)
    .tickPadding(6));

d3.selectAll("input")
    .on("change", changed);

var timeout = d3.timeout(function() {
  d3.select("input[value=\"grouped\"]")
      .property("checked", true)
      .dispatch("change");
}, 2000);

function changed() {
  timeout.stop();
  if (this.value === "grouped") transitionGrouped();
  else transitionStacked();
}

function transitionGrouped() {
  y.domain([0, yMax]);

  rect.transition()
      .duration(500)
      .delay(function(d, i) { return i * 10; })
      .attr("x", function(d, i) { return x(i) + x.bandwidth() / n * this.parentNode.__data__.key; })
      .attr("width", x.bandwidth() / n)
    .transition()
      .attr("y", function(d) { return y(d[1] - d[0]); })
      .attr("height", function(d) { return y(0) - y(d[1] - d[0]); });
}

function transitionStacked() {
  y.domain([0, y1Max]);

  rect.transition()
      .duration(500)
      .delay(function(d, i) { return i * 10; })
      .attr("y", function(d) { return y(d[1]); })
      .attr("height", function(d) { return y(d[0]) - y(d[1]); })
    .transition()
      .attr("x", function(d, i) { return x(i); })
      .attr("width", x.bandwidth());
}

// Returns an array of m psuedorandom, smoothly-varying non-negative numbers.
// Inspired by Lee Byron’s test data generator.
// http://leebyron.com/streamgraph/
function bumps(m) {
  var values = [], i, j, w, x, y, z;

  // Initialize with uniform random values in [0.1, 0.2).
  for (i = 0; i < m; ++i) {
    values[i] = 0.1 + 0.1 * Math.random();
  }

  // Add five random bumps.
  for (j = 0; j < 5; ++j) {
    x = 1 / (0.1 + Math.random());
    y = 2 * Math.random() - 0.5;
    z = 10 / (0.1 + Math.random());
    for (i = 0; i < m; i++) {
      w = (i / m - y) * z;
      values[i] += x * Math.exp(-w * w);
    }
  }

  // Ensure all values are positive.
  for (i = 0; i < m; ++i) {
    values[i] = Math.max(0, values[i]);
  }

  return values;
}

</script>



<style>
<center>
body {
  font: 10px sans-serif;
}

#container {
  width: 650px;
  height: 400px;
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
	lon = -79.9959,
	lat = 40.4406;

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
