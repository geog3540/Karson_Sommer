var polygonJSONFile2 = 'data/RoadSummary.geojson'
// change the projection if needed *d3.geoAlbers()*
var projection2 = d3.geoAlbers();
// Relative placement of legend that ranges between 0 and 1.
// 0 is the start, whereas 1 i the end of each panel.
// If you want to put the legend on the left, then use 0.
// legendY = 0.5 puts the legend in the middle of the Y Axis.
var legendX2 = 0.1, legendY2 = 0.72

// id attribute and the name attribute to be used in the tooltip
var idAttribute2 = 'FIPS', nameAttribute2 = 'County';


// Height, width, margins
var widthheightRatio2 = 0.4;
var chartWidth2 = d3.select('#myMap2').node().getBoundingClientRect().width;
var chartHeight2 = chartWidth2 * widthheightRatio2;
var margin2 = {top: 10, right: 20, bottom: 10, left:20};
var width2 = chartWidth2 - margin2.right - margin2.left;
var height2 = chartHeight2 - margin2.top - margin2.bottom;
var histHeight2 = (chartHeight2 * .25);

var att12 = "Length";
var att1alias2 = "Length";
var charts2 = [
    {'map2': '#myMap2', 'var': att12, 'ax': att1alias2}
];

var buttons2 = [
    {'btn': '#buttonLength2'},
    {'btn': '#buttonVehicleMiles2'}
];
activeButton2 = 'buttonLength2';

// Functions for the chart
d3.selection.prototype.moveToFront = function() {
    return this.each(function(){
        this.parentNode.appendChild(this);
    });
}
d3.selection.prototype.moveToBack = function() {
    return this.each(function(){
        this.parentNode.appendChild(this).style('z-index', 1);
    });
}

var ordinal2 = d3.scaleOrdinal(d3.schemeCategory10)
.domain(["Avenue", "Street", "Highway", "Way", "Road", "MALFORMED", "NULL"])

var current_scale2 = ordinal2;

// Set up the legend dimensions and style
var holdLegend2 = d3.legendColor()
.shapeWidth(15)
.shapeHeight(5)
.shapePadding(10)
.orient('vertical')
.useClass(false)
.scale(current_scale2)


var polygons2;
var proj2;
    //this is where you read the json polygon data
d3.json(polygonJSONFile2).then(function(data2) {
        // set up the projection
        polygons2 = data2;
        proj2 = d3.geoPath().pointRadius(2);
        proj2.projection(projection2.fitSize([width2, height2], polygons2));

        // Set the buttons up for interactivity
        buttons2.forEach(function(d) {
            d3.select(d.btn).on('click', function() {
                d3.selectAll('.btn').classed('active', false);
                d3.select(this).classed('active', true);
                var buttonid2 = d3.select(this).node().id;
                activeButton2 = buttonid2;

                if(document.getElementById("myCheck2").checked) {
                    if(buttonid2 == "buttonLength2"){
                        $(".container").fadeOut(200);
                        functionAlert("These charts will summarize roads by the percent of total length in each county");
                    } else if(buttonid2 == "buttonVehicleMiles2"){
                        $(".container").fadeOut(200);
                        functionAlert("These charts will summarize roads by percent of total miles driven by vehicles for each county. Roads with more traffic will have higher weight, and low traffic roads will have less weight");
                    }
                }

                //current_scale = d.scale;
                if(activeButton2 == 'buttonLength2') {
                    charts2[0].var = "Length"
                    charts2[0].ax = "Length"
                } else if(activeButton2 == 'buttonVehicleMiles2') {
                    charts2[0].var = "Vehicle_Miles"
                    charts2[0].ax = "Vehicle_Miles"
                } else {
                    err.message;
                }

                updateMap2(polygons2)
            });
        });

        // For each input attribute make a map and a histogram
        updateMap2(polygons2)

});


function updateMap2(polygons2) {
        charts2.forEach(function(d) {
            // Set scales + variable for each map
            d3.select("#panelSVG2").remove();

            var dependent2 = d.var;
            console.log(dependent2)

            // tooltip for maps
            var tooltipD32 = d3.select(d.map2)
            .append("div")
            .attr("class", "tooltipD3")
            .attr("id", function() { return d.map2.substr(1) + '-tooltip'; })
            .style('display', 'none');

            // Set up the panel that includes the map and the legend
            var panel2 = d3.select(d.map2).append("svg")
            .attr("id","panelSVG2")
            .attr("width", width2 + margin2.left + margin2.right)
            .attr("height", height2 + margin2.top + margin2.bottom)
            .append("g")
            .attr("transform", "translate(" + margin2.left + "," + margin2.top + ")");

            // append the choropleth map
            var map2 = panel2.selectAll('.path')
            .data(polygons2.features)
            .enter().append('g');
            map2.append('path')
            .attr("class", function(e) { return classPolygon2(e, d.map2); })
            .on('mouseover', function(e) { return mouseOver2(e, dependent2); })
            .on('mouseout', mouseOut2)
            .on('mousemove', mousemoveFunc2)
            .attr("d", proj2)
            .style('fill', function(c) {
                return current_scale2(c.properties[d.var]);
            });


            holdLegend2.labelFormat(d.form);
            // add the legend
            panel2.append('g')
            .attr("class", "legendQuant")
            .attr("transform", "translate(" + (width2 * legendX2) + "," + (height2 * legendY2) + ")");
            panel2.select('.legendQuant').call(holdLegend2.scale(current_scale2));

        })
}

window.addEventListener("resize", displayWindowSize2);
function displayWindowSize2() {
    chartWidth2 = d3.select('#myMap2').node().getBoundingClientRect().width;
    chartHeight2 = chartWidth2 * widthheightRatio2;
    margin2 = {top: 10, right: 20, bottom: 10, left:20};
    width2 = chartWidth2 - margin2.right - margin2.left;
    height2 = chartHeight2 - margin2.top - margin2.bottom;
    histHeight2 = (chartHeight2 * .25);

    hist_scale2 = d3.scaleLinear().range([0, width2]); // hist X
    hist_y2 = d3.scaleLinear().range([histHeight2, 0]);	// hist Y
    bins2 = d3.histogram()
    .domain(hist_scale2.domain())
    .thresholds(10);

    proj2.projection(projection2.fitSize([width2, height2], polygons2));

    updateMap2(polygons2);
}

function classPolygon2(polygon2, map2) {
    // Function to create the correct CSS style name for each polygon.
    // (e.g. Johnson county polygon, FIPS 19) >> 'polygons.lung.polygon19'
    return 'polygons ' + map2 + ' polygon' + polygon2.properties[idAttribute2];
}

function getBoundingBoxCenter2 (selection2) {
    // get the DOM element from a D3 selection
    var element2 = selection2.node();
    // use the native SVG interface to get the bounding box
    var bbox2 = element2.getBBox();
    // return the center of the bounding box
    return [bbox2.x + bbox2.width/2, bbox2.y + bbox2.height/2];
}

function mouseOver2(polygon2, attr2) {
    /* Mouse over routine
    Highlight the polygon on all maps, adjust the tooltip */
    //enter the id of the polygon
    var id2 = polygon2.properties[idAttribute2],
    //enter the name of the polygon
    name2 = polygon2.properties[nameAttribute2];

    return charts2.forEach(function(d) {

        var map2 = d3.select(d.map2);
        var tip2 = d.map2 + '-tooltip';
        // select the polygon by its id attribute
        map2.select('.polygon'+id2)
        .moveToFront()
        .style('stroke', 'black')
        .style('opacity', '1');

        // map tooltip
        d3.select(tip2)
        .style('display', null)
        .html("<p><b>" + name2 + " " + nameAttribute2 + "</b><br/>" + d.var +
        ": " + polygon2.properties[d.var] + "</p>");

    });
}

function mousemoveFunc2(polygon2) {
    /* Moves tooltip */
    //the id of the polygon
    var id2  = '.polygon' + polygon2.properties[idAttribute2];

    charts2.forEach(function(d) {
        var map2 = d3.select(d.map2);
        var polygonOnMap2 = getBoundingBoxCenter2(map2.select(id2));
        var tip2 = d.map2 + '-tooltip';

        var offsetY2 = d3.select('#myMap2').node().getBoundingClientRect().y
        var offsetX2 = d3.select('#myMap2').node().getBoundingClientRect().x

        d3.select(tip2)
        .style("top", (polygonOnMap2[1] + offsetY2 + window.pageYOffset - 40) + "px" )
        .style("left", (polygonOnMap2[0] + offsetX2 + window.pageXOffset + 30) + "px");
    });
}

// Resets the polygon after mouseout
function mouseOut2(polygon2) {

    var id2 = polygon2.properties[idAttribute2],
    name2 = polygon2.properties[nameAttribute2];

    charts2.forEach(function(d) {

        var map2 = d3.select(d.map2);
        var tip2 = d.map2 + '-tooltip';

        map2.select(('.polygon'+id2))
        .style('stroke', "#dadada")
        .style('opacity', '0.85');
        // map tooltip
        d3.select(tip2)
        .style('display', "none");
    });
}