D3-Code
=======

This repository is the host to D3 code that can be utilized for interactive data representation.

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">

<title>Antineutrino Quasi-Elastic Scattering vs. Muon Neutrino Quasi-Elastic Scattering</title>

<script type="text/javascript" src="../d3.v3.js"></script>
<style type="text/css">

/*=============================Begin CSS Elements=============================*/

.axis path,
.axis line {
  fill: none;
  stroke: black;
  stroke-width: 2px;
  shape-rendering: crispEdges;
  }

.axis text {
  font-family: sans-serif;
  font-size: 11px;
  fill: black;
  }

#circle:hover{
  fill: #0000FF;
  }

#tooltip{
  position:absolute;
  width:auto;
  height: auto;
  padding: 10px;
  background-color: #CCE5FF;
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  border-radius: 10px;
  -webkit-box-shadow: 4px 4px 10px rgba(0,0,0,0.4);
  -moz-box-shadow: 4px 4px 10px rgba(0,0,0,0.4);
  box-shadow: 4px 4px 10px rgba(0,0,0,0.4);
  pointer-events: none;
  }

#tooltip.hidden{
  display:none;
  }

#tooltip p {
  margin:0;
  font-family:sans-serif;
  position: center;
  font-size: 16px;
  line-height: 20px;
  }

form{
position: absolute;
  left:40px;
  top: 20px;
  }

/*=============================End CSS Elements===============================*/
</style>
</head>
<!--=========================Begin HTML Elements=============================-->
<body>

  <form>
    <label><input type="radio" name="mode" id="dataset1" checked>
      Antineutrino Qusai-Elastic Scattering/Proton</label>

    <label><input type="radio" name="mode" id="dataset2">
      Muon Neutrino Quasi-Elastic Scattering/Neutron</label>
  </form>

  <div id="tooltip" class="hidden">
    <p><strong>Fractional Systematic Uncertainties</strong></p>
    <p><span id="value">100</span>x 10e-39</p>


<!--===========================End HTML Elements=============================-->

<script type="text/javascript">

//=============================Begin JavaScript===============================\\


//----------------------Define Width and Height of Graph----------------------\\

var w = 850;
var h = 600;
var padding = 75;

//------------------------------Dynamic dataset-------------------------------\\

var dataset = [
              [0.0125, 8.13],
              [0.0375, 10.61],
              [0.075, 11.85],
              [0.15, 10.96],
              [0.3, 7.77],
              [0.6, 3.4],
              [1.0, 1.23],
              [1.6, 0.41]
              ];


//--------------------------Create scale functions----------------------------\\

//Create xScale
var xScale = d3.scale.linear()
                .domain([0, 2])
               .range([padding, w - padding * 2]);

//Create yScale
var yScale = d3.scale.linear()
               .domain([0, 20])
               .range([h - padding, padding]);


//-------------------------------Define axis'---------------------------------\\

//Define X axis
var xAxis = d3.svg.axis()
              .scale(xScale)
              .orient("bottom")
              .tickValues([0, 0.5, 1, 1.5, 2])
              .ticks(15);

//Define Y axis
var yAxis = d3.svg.axis()
              .scale(yScale)
              .orient("left")
              .tickValues([0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20])
              .ticks(20);

//----------------------------Create SVG element------------------------------\\

var svg = d3.select("body")
            .append("svg")
            .attr("width", w)
            .attr("height", h);


//-----------------------------Create Text Labels-----------------------------\\

// text label for the x axis
svg.append("text")
   .attr("x", w / 2.19)
   .attr("y", h - padding / 3)
   .style("text-anchor", "middle")
   .text("GeV{^2}");

//text label for y axis
svg.append("text")
   .attr("transform", "rotate(-90)")
   .attr("y", padding/4)
   .attr("x",0 - (h / 2))
   .attr("dy", "1em")
   .style("text-anchor", "middle")
   .text("cm^2/GeV^2");

//-------Make a Clipping path so elements stay withing the graph space--------\\

//Define clipping path
svg.append("clipPath")
   .attr("id", "chart-area")
   .append("rect")
   .attr("x", padding)
   .attr("y", padding)
   .attr("width", w - padding * 3)
   .attr("height", h - padding * 2);

//------------Chose To Define a Color Gradient for Graphed Points-------------\\

//Define Gradient
svg.append("radialGradient")
   .attr("id", "area-gradient")
   .attr("gradientUnits", "userSpaceOnUse")
   .attr("spreadMethod", "pad")
   .selectAll("stop")
   .data([
        {offset: "10%", color: "#CCE5FF", opacity: "1"},
        {offset: "100%", color: "#0000CC", opacity: "1"}
        ])
   .enter().append("stop")
   .attr("offset", function(d) { return d.offset; })
   .attr("stop-color", function(d) { return d.color; });

//------------------------Create Points for the Graph-------------------------\\

//create circles
svg.append("g")
   .attr("id", "circles")
   .attr("clip-path", "url(#chart-area)")
   .selectAll("circle")
   .data(dataset)
   .enter()
   .append("circle")
   .attr("cx", function(d) {
      return xScale(d[0]);
      })
   .attr("cy", function(d) {
      return yScale(d[1]);
      })
   .attr("r", 6)
   .attr("fill", "url(#area-gradient)")


//-----------Create Tooltip for the Graph(Shows Points on MouseOver)----------\\

//tooltip
.on("mouseover", function(d) {

//Get this bar's x/y values, then augment for the tooltip
    var xPosition = parseFloat(d3.select(this).attr("x")) + xScale.range / 2;
    var yPosition = parseFloat(d3.select(this).attr("y")) / 2 + h / 2;

//Update the tooltip position and value
    d3.select("#tooltip")
      .style("left", xPosition + "px")
      .style("top", yPosition + "px")
      .select("#value")
      .text(d);

//Show the tooltip
    d3.select("#tooltip").classed("hidden", false);
    })
    .on("mouseout", function() {

//Hide the tooltip
    d3.select("#tooltip").classed("hidden", true);

});


//-------------------------Create the x and y Axis----------------------------\\

//Create X axis
svg.append("g")
   .attr("class", "x axis")
   .attr("transform", "translate(0," + (h - padding ) + ")")
   .call(xAxis);

//Create Y axis
svg.append("g")
   .attr("class", "y axis")
   .attr("transform", "translate(" + padding  + ",0)")
   .call(yAxis);

//=============Transition between two datasets (To another Graph)=============\\

//adding an update for new value
d3.select("body")
  .on("click", function() {

//-------------------New Values Our Graph Will Transition To------------------\\

//New values for dataset
dataset2 =[
        [0.0125, 7.61],
        [0.0375, 11.46],
        [0.075, 13.43],
        [0.15, 14.90],
        [0.3, 10.63],
        [0.6, 5.83],
        [1.0, 2.42],
        [1.6, 0.97]
        ];

//-----------------Update scale domains(May or may not need)------------------\\

/*
xScale.domain([0, d3.max(dataset, function(d) { return d[0]; })]);
yScale.domain([0, d3.max(dataset, function(d) { return d[1]; })]);
*/


//---------------------Transition to Circles for the New Graph----------------\\

//Update all circles
svg.selectAll("circle")
   .data(dataset2)
   .transition()
   .duration(1000)
   .ease("linear")
   .each("start", function(d){
           d3.select(this)
              .attr("fill", "blue")
             .attr("r", 10)
        })
   .attr("cx", function(d) {
           return xScale(d[0]);
        })
   .attr("cy", function(d) {
           return yScale(d[1]);
        })
   .transition()
   .duration(1000)
   .attr("fill", "url(#area-gradient)")
   .attr("r", 4);



//---------------------------Update Axis' For New Graph-----------------------\\

//update xAxis
svg.select(".x.axis")
   .transition()
   .duration(1000)
   .call(xAxis);

//Update Y axis
svg.select(".y.axis")
   .transition()
   .duration(1000)
   .call(yAxis);

//------------------------Update Axis Text for new Graph----------------------\\

//Update yAxis Text
/*
svg.select("text")
  .attr("transform", "rotate(-90)")
  .attr("y", padding/4)
  .attr("x",0 - (h / 2))
  .attr("dy", "1em")
  .style("text-anchor", "middle")
  .text("cm^2/GeV^2/Neutron");

//Update xAxis Text
svg.select("text")
   .attr("x", w / 2.19)
   .attr("y", h - padding / 3)
   .style("text-anchor", "middle")
   .text("Gev^2");
*/

//--------------------------end update for new Value--------------------------\\

//Here
});

//=============================End of JavaScript==============================\\

</script>
</body>
</html>
