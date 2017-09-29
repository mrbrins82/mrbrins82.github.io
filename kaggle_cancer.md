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
<title>2016-2017 NHL Team Stats</title>
<style>

svg {
  font: 14px sans-serif;
  fill: gray;
}

.background path {
  fill: none;
  stroke: none;
  stroke-width: 20px;
  pointer-events: stroke;
}

.foreground path {
  fill: none;
  stroke: #F39C12;
  stroke-opacity: 1.0;
  stroke-width: 1.5px;
}

.axis .title {
  font-size: 16px;
  fill: gray;
  font-weight: bold;
  text-transform: uppercase; 
}

.axis line,
.axis path {
  fill: none;
  stroke: purple;
  shape-rendering: crispEdges;
}

.label {
  -webkit-transition: fill 125ms linear;
}

.active .label:not(.inactive) {
  font-weight: bold;
  fill: darkOrange;
}

.label.inactive {
  fill: #ccc;
}

.foreground path.inactive {
  stroke: teal;
  stroke-opacity: 0.35;
  stroke-width: 1px;
}

</style>
<body>
<script src="//d3js.org/d3.v3.min.js"></script>
<script>

var margin = {top: 30, right: 40, bottom: 20, left: 300},
    width = 1100 - margin.left - margin.right,
    height = 550 - margin.top - margin.bottom;

var dimensions = [
  {
    name: "team",
    scale: d3.scale.ordinal().rangePoints([0, height]),
    type: String
  },
  {
    name: "GF",
    scale: d3.scale.linear().range([height, 0]),
    type: Number
  },
  {
    name: "GA",
    scale: d3.scale.linear().range([height, 0]),
    type: Number
  },
  {
    name: "SF",
    scale: d3.scale.sqrt().range([height, 0]),
    type: Number
  },
  {
    name: "SA",
    scale: d3.scale.linear().range([height, 0]),
    type: Number
  }
];

var x = d3.scale.ordinal()
    .domain(dimensions.map(function(d) { return d.name; }))
    .rangePoints([0, width]);

var line = d3.svg.line()
    .defined(function(d) { return !isNaN(d[1]); });

var yAxis = d3.svg.axis()
    .orient("left");

var svg = d3.select("body").append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
  .append("g")
    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

var dimension = svg.selectAll(".dimension")
    .data(dimensions)
  .enter().append("g")
    .attr("class", "dimension")
    .attr("transform", function(d) { return "translate(" + x(d.name) + ")"; });

d3.tsv("teamstats_20162017.tsv", function(error, data) {
  if (error) throw error;

  dimensions.forEach(function(dimension) {
    dimension.scale.domain(dimension.type === Number
        ? d3.extent(data, function(d) { return +d[dimension.name]; })
        : data.map(function(d) { return d[dimension.name]; }).sort());
  });

  svg.append("g")
      .attr("class", "background")
    .selectAll("path")
      .data(data)
    .enter().append("path")
      .attr("d", draw);

  svg.append("g")
      .attr("class", "foreground")
    .selectAll("path")
      .data(data)
    .enter().append("path")
      .attr("d", draw);

  dimension.append("g")
      .attr("class", "axis")
      .each(function(d) { d3.select(this).call(yAxis.scale(d.scale)); })
    .append("text")
      .attr("class", "title")
      .attr("text-anchor", "middle")
      .attr("y", -9)
      .text(function(d) { return d.name; });

  // Rebind the axis data to simplify mouseover.
  svg.select(".axis").selectAll("text:not(.title)")
      .attr("class", "label")
      .data(data, function(d) { return d.name || d; });

  var projection = svg.selectAll(".axis text,.background path,.foreground path")
      .on("mouseover", mouseover)
      .on("mouseout", mouseout);

  function mouseover(d) {
    svg.classed("active", true);
    projection.classed("inactive", function(p) { return p !== d; });
    projection.filter(function(p) { return p === d; }).each(moveToFront);
  }

  function mouseout(d) {
    svg.classed("active", false);
    projection.classed("inactive", false);
  }

  function moveToFront() {
    this.parentNode.appendChild(this);
  }
});

function draw(d) {
  return line(dimensions.map(function(dimension) {
    return [x(dimension.name), dimension.scale(d[dimension.name])];
  }));
}

</script>
</body>
