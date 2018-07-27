# Bar-stacked-datalabel-trick

--read it all tfm
Hello!
Thanks for this very cool library!
My question is how it is possible to repeat functionality for show/hide chart for custom legend? Yes, this functionality working for charts by default. But I have to change default legend to custom and lost show/hide function.

I made a default legend hidden and generate my own like this:

var weightChartOptions = {
    responsive: true,
    legendCallback: function(chart) {
        var legendHtml = []; 
        legendHtml.push('<table>');
        legendHtml.push('<tr>');
        for (var i=0; i<chart.data.datasets.length; i++) {
            legendHtml.push('<td><div class="chart-legend" style="background-color:' + chart.data.datasets[i].backgroundColor +'"></div></       td>');  
            if (chart.data.datasets[i].label) {
                legendHtml.push('<td class="chart-legend-label-text">' + chart.data.datasets[i].label + '</td>');
            }
        }
        legendHtml.push('</tr>');
        legendHtml.push('</table>');
        return legendHtml.join("");
    },
    legend: {
        display: false
    }
};

var ctx = document.getElementById("weightChart").getContext("2d");
var weightChart = new Chart(ctx, {
    type: 'line',
    data: weightChartData,
    options: weightChartOptions
});
document.getElementById("weightChartLegend").innerHTML = weightChart.generateLegend();
How I can keep this custom legend and add show/hide function?
Thanks.

 @panzarino panzarino added Version: 2.x  labels on 16 May 2016
@etimberg
Member
etimberg commented on 16 May 2016
@achirkof you'll need to attach your own event listeners to your HTML legend and then emulate the hidden functionality

The function that does this for the regular legend is: https://github.com/chartjs/Chart.js/blob/master/src/core/core.legend.js#L16-L26

 @achirkof
achirkof commented on 16 May 2016 • 
Git it! Thank you very much!

var weightChartOptions = {
        responsive: true,
        legendCallback: function(chart) {
            console.log(chart);
            var legendHtml = [];
            legendHtml.push('<table>');
            legendHtml.push('<tr>');
            for (var i=0; i<chart.data.datasets.length; i++) {
                legendHtml.push('<td><div class="chart-legend" style="background-color:' + chart.data.datasets[i].backgroundColor + '"></div></td>');                    
                if (chart.data.datasets[i].label) {
                    legendHtml.push('<td class="chart-legend-label-text" onclick="updateDataset(event, ' + '\'' + chart.legend.legendItems[i].datasetIndex + '\'' + ')">' + chart.data.datasets[i].label + '</td>');
                }                                                                              
            }                                                                                  
            legendHtml.push('</tr>');                                                          
            legendHtml.push('</table>');                                                       
            return legendHtml.join("");                                                        
        },                                                                                     
        legend: {                                                                              
            display: false                                                                     
        }                                                                                      
    };                                                                                         

    // Show/hide chart by click legend
    updateDataset = function(e, datasetIndex) {
        var index = datasetIndex;
        var ci = e.view.weightChart;
        var meta = ci.getDatasetMeta(index);

        // See controller.isDatasetVisible comment
        meta.hidden = meta.hidden === null? !ci.data.datasets[index].hidden : null;

        // We hid a dataset ... rerender the chart
        ci.update();
    };

    var ctx = document.getElementById("weightChart").getContext("2d");
    window.weightChart = new Chart(ctx, {
        type: 'line',
        data: weightChartData, 
        options: weightChartOptions
    });
    document.getElementById("weightChartLegend").innerHTML = weightChart.generateLegend();
};
The most important parts are:
onClick function call for each legend label

if (chart.data.datasets[i].label) {
    legendHtml.push('<td class="chart-legend-label-text" onclick="updateDataset(event, ' + '\'' + chart.legend.legendItems[i].datasetIndex + '\'' + ')">' + chart.data.datasets[i].label + '</td>');
}
and function

updateDataset = function(e, datasetIndex) {
    var index = datasetIndex;
    var ci = e.view.weightChart;
    var meta = ci.getDatasetMeta(index);

    // See controller.isDatasetVisible comment
    meta.hidden = meta.hidden === null? !ci.data.datasets[index].hidden : null;

    // We hid a dataset ... rerender the chart
    ci.update();
};
Also make chart instance of Window adding window. before:

window.weightChart = new Chart(ctx, {
        type: 'line',
        data: weightChartData, 
        options: weightChartOptions
});
 @yusufozturk yusufozturk referenced this issue on 30 May 2016
 Closed
v2 Doughnut legend right/left support #2666
@etimberg
Member
etimberg commented on 27 Jun 2016
Closing as resolved.

 @etimberg etimberg closed this on 27 Jun 2016
@coolbombom
Contributor
coolbombom commented on 27 Oct 2016
@achirkof

Hi achirkof

your code does not work when you have a pie chart and only one dataset, it hides all elements instead of hiding the chosen element.

I'm trying to find a solution but without luck so far, do you have a suggestion?

kind regards
c_bb

 @coolbombom
Contributor
coolbombom commented on 27 Oct 2016
@achirkof

never mind found the solution :-)

replaced:
meta.hidden = meta.hidden === null? !ci.data.datasets[index].hidden : null;

with:
if (meta.dataset) {
meta.hidden = !meta.hidden;
} else {
meta.data[elementIndex].hidden = !meta.data[elementIndex].hidden;
}

and added elementIndex as an argument in the function:
function(e, datasetIndex, elementIndex)

kind regards
c_bb

 @shaifulborhan shaifulborhan referenced this issue in jtblin/angular-chart.js on 23 Dec 2016
 Closed
How do I access the chart object? #574
@notflip
notflip commented on 14 Feb 2017
Any idea how to implement this for multiple charts on 1 page? Can't seem to get it working..

 @mrmagcore
mrmagcore commented on 26 May 2017
I store all the charts in my page in an array. Then I have a function to toggle the dataset:

var pageCharts = new Array();

// .... some other, obvious stuff

var myBarChart = new Chart(ctxRight, {
type: 'bar',
data: myData,
options: myOptions)
});

pageCharts[0] = myBarChartRight;
var myBarChart = new Chart(ctxRight, {
type: 'bar',
data: myData2,
options: myOptions2)
});

pageCharts[1] = myBarChartRight;

function toggleChartDataset(chart, datasetIndex) {
var meta = chart.getDatasetMeta(datasetIndex);

meta.hidden = meta.hidden === null? !chart.data.datasets[datasetIndex].hidden : null;
chart.update();
}

 @GeraudWilling
GeraudWilling commented on 23 Jan
I'm actually facing a similar issue. I"ve make a custom legend component and I'm able to show/hide using the above codes. However, this only works for bar chart. It raise the following exception when using it with donut chart:

TypeError: Cannot read property '_meta' of undefined at Chart.getDatasetMeta (core.controller.js:656)

Have anyone faced a similar issue please?

 @benmccann benmccann added type: question  and removed type: question  type: support  labels on 26 Jan
@sadikyalcin
sadikyalcin commented on 10 Apr
@GeraudWilling yes - your index is undefined.

var index = datasetIndex;
var ci = this.presentationsChart;
var meta = ci.getDatasetMeta(index);
if the index is undefined, it will throw that error. I'm having a similar issue when hiding. The chart is destroyed when I hide a dataset :/
