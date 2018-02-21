# Introduction

In this lesson, you will learn how to use Google Sheets to access and view air quality data from the Lafayette 
Engagement and Research Network (LEaRN) sensor network in Lafayette, LA. You can learn more about the LEaRN project
[here](https://learnlafayette.com).  Introductory slides with background information on the LEaRN air quality project
can be found [here](https://learnlafayette.com/misc/LEaRN_LPSS_Workshop_20180203.pdf).

# Step 1: LEaRN Data API Introduction

LEaRN air quality data can be downloaded by using what is know in computer programming as an application programming 
interface (API). APIs provide allow different computer programs to communicate with each other. Computer programmers 
use APIs to build software applications.

The LEaRN API uses the [SensorThings API](http://docs.opengeospatial.org/is/15-078r6/15-078r6.html) standard.
SensorThings is an international software standard that defines how Internet of Things (IoT) devices, like air quality
sensors, communicate with the rest of the world.  

The LEaRN API allows users and software developers to access LEaRN air quality data over the World Wide Web (WWW). 
For the purposes of this lesson, we'll use a test version of the LEaRN API that is accessible via the following 
Uniform Resource Locator (URL):

https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/

You can browse the API using your web browser. If you load the above URL into your web browser, you should see
something like the following:

```json
{
  "value" : [ {
    "name" : "Datastreams",
    "url" : "https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/Datastreams"
  }, {
    "name" : "MultiDatastreams",
    "url" : "https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/MultiDatastreams"
  }, {
    "name" : "FeaturesOfInterest",
    "url" : "https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/FeaturesOfInterest"
  }, {
    "name" : "HistoricalLocations",
    "url" : "https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/HistoricalLocations"
  }, {
    "name" : "Locations",
    "url" : "https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/Locations"
  }, {
    "name" : "Observations",
    "url" : "https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/Observations"
  }, {
    "name" : "ObservedProperties",
    "url" : "https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/ObservedProperties"
  }, {
    "name" : "Sensors",
    "url" : "https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/Sensors"
  }, {
    "name" : "Things",
    "url" : "https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/Things"
  } ]
}
```

This is known the "root" of the API (like the root or base of a plant), and it is written in a format called JSON 
(which stands for JavaScript Object Notation). Each string of text follow the attribute "name" is the name of a
SensorThings entity. You can learn more about SensorThings entities 
[here](http://docs.opengeospatial.org/is/15-078r6/15-078r6.html#22).

You can browse the LEaRN SensorThings API using the URLs associated with each entity. For the purposes of this 
lesson, we will focus on the following entities: Things, Observations, and Datastreams; the following diagram below 
shows the relationship between these entities:

![LEaRN entity summary](http://learnlafayette.com/img/misc/LPSS_TeacherTraining_20180129.png)

You can think of a "Thing" as a box that contains one or more sensors.  Each time one of these sensors makes a 
measurement (e.g. measuring air temperature, relative humidity, ozone, or particulate matter) an Observation is 
created. Lastly, you can think of a Datastream as a group of one or more Observations taken by a single sensor 
installed in a single Thing; a Thing can have many Datastreams, one for each sensor contained by the Thing.  
Observations can have many attributes, but the two that we will focus on are the *phenomenonTime* (the time when 
the sensor made the observation), and *result* (the value of the datum read from the sensor).

In the sections that follow, you'll learn how to use Google Sheets to discover Things and Datastreams, to download 
Observations and then make a simple chart.   
 
# Step 2: Copy starter Google Sheet

We'll be using a read-only Sheet to quickly get started accessing and manipulating LEaRN air quality data. You can 
find the LEaRN API starter Google Sheet here:

https://docs.google.com/spreadsheets/d/1UuTOr1qpS9RCBtSxea0DVsjsDm7Etb2GlOvSXtpWH2A/edit#gid=954489212

Use the following instructions to get started.

1. Load this Google Sheet in your web browser
2. From the "File" menu, click on "Make a copy..."
3. Set the "Name" to "LEaRN API LPSS"
4. Choose a "Folder" to store the sheet in (the default of "My Drive" is fine)
5. Click "OK"

This will create a copy of the "LEaRN API LPSS" Sheet within your own Google Drive that you can edit.

# Step 3: Explore API Root and Things worksheets

The "Readme" worksheet contains a description of the other worksheets in the LEaRN API LPSS Sheet.  The "API Root"
sheet contains the same information as the API root JSON listed above, only this time the data have been converted 
into a format that Google Sheets can understand.  We use a third-party Google Sheets extension called "ImportJSON" 
to do this conversion for us (you can learn more about ImportJSON by following the link in the "script" worksheet).

The "Things" sheet lists all of the Things in the LEaRN database.  This is the same information you would see if
you loaded the following URL into your web browser:

https://dev-sta-api.learnlafayette.com/SensorThingsService/v1.0/Things

# Step 4: Select a Thing and Datastream

Things have a Location associated with them. We will use this location, along with the LEaRN dashboard, to help us 
select the Datastream whose Observations we want to download.  You can find the LEaRN dashboard:

http://learn-app-dev.azurewebsites.net/#/pages/livestream

Here is a screenshot of the current version of the dashboard (as of Jan. 29, 2018):

![LEaRN dashboard](http://learnlafayette.com/img/misc/Dashboard-BETA3.png)

The *LEaRN Things map* in the upper left panel of the dashboard shows the location of LEaRN air quality Things 
throughout Lafayette (note that these are currently simulated Things; the LEaRN Things will not begin being deployed
until March 2018).  If you click on a Thing (one of the blue dots), a pop-up box will appear showing the geographic 
coordinates the of Thing's location, as well as the description and name of the Thing.  Clicking on a Thing will 
also cause Observations to be loaded in both the *Live data* as well as *Historical data* panels.  The live data will
update as new Observations are made.  The historical data allow you to zoom in to examine a past data.  By default,
Observations from all Datastreams are displayed in the live and historical graphs.  You can disable (or later 
re-enable) the display of Observations for a Datastream by clicking on the name of the Datastream in the graph legend
(the legend is along the bottom of the live graph, and along the left side of the historical graph).

There are several Datastreams associated with the Things in the LEaRN test database.  The first Datastream simulates
raw (i.e. unprocessed) ground-level ozone, the second simulates ozone data that has undergone quality assurance/quality
control processing (so-called QA/QCed data), and the third Datastream simulates an alert datastream for ozone data; an
alert datastream would for exmaple be used to signal Observations that exceed a particular threshold (e.g. unsafe
ozone levels for the young, elderly, or other vulnerable populations).  Because these are simulated Datastreams, not
all of the them may contain data.

You can use the map view as well as the live and historcal data views to discover Things and Datastreams whose data you
wish to analyze further using Google Sheets.  You can try it yourself by following these steps:

1. Find the Thing named "Lafayette Middle" by clicking through the Things in the map view; this will cause the live and
historical data to load for this Thing.
2. De-select all datastreams except for the ozone Datastream.

In the following steps we'll use the ozone sensor for the Thing at Lafayette Middle as an example.

# Step 5: Find Datastream IDs on Thing-Datastream worksheet

To be able to download Observations from the ozone sensor of the Thing located at "Lafayette Middle", we first need to 
know the unique identifier or ID of the Datastream that collects all of the ozone Observations for this Thing.  We'll 
use the "Thing-Datastream" sheet of the "LEaRN API LPSS" Google Sheet to find this ID.

1. Click on the "Thing-Datastream" tab at the bottom of your copy of the "LEaRN API LPSS" Google Sheet.

![Thing-Datastream worksheet](http://learnlafayette.com/img/misc/Thing-Datastream.png)

> Note: if the *Thing-Datastream* (or any other worksheet) does not contian the data you expect to see, but instead
> displays something like '"NAME?" error' on the first line, you can force Sheets to reload the worksheet data by
> making a small edit to the query in the formula bar, for example changing '$top=3000' to '$top=3001' and pressing
> the enter key on your keyboard.  This will force Sheets to try to reload the data.  You may have to do this more 
> than once to resolve the problem, but generally one time is enough to get the data to load as expected.

2. From the *View* menu, choose *Freeze* and then *1 row*.  This will ensure that the column headings in the first
row of the "Thing-Datastream" worksheet remain visible as we scroll through the rows.

3. Search for the following text: "Lafayette Middle".  

There should be 6 rows in the Thing-Datastream worksheet that contain the words "Lafayette" and "Middle" 
(rows 176-181).  Row 176 represents the location of the simulated Thing installed at Lafayette Middle; column I shows 
the same coordinates that were displayed for this Thing in the dashboard (except with the longitude listed first, and 
the latitude listed second).  Row 177 contains all of the fields of the ozone Datastream for the Lafayette Middle
Thing.  There are many other fields in this Datastream, but for now we're only interested in the ID, which you can 
find in column M (which is named Value Datastreams @iot.id).

The ID for the ozone Datastream of the Lafayette Middle Thing is 37.  We will use this ID in the next step.

# Step 6: Download ozone Observation data for the Thing at Lafayette Middle

The worksheet named "Observations_DS_1" contains Observations for the Datastream whose ID is 1.  These Observations 
were loaded into the Google Sheet a few days ago.  In this section, we'll make a copy of this worksheet and use it to
load data from the Lafayette Middle ozone Datastream (the one with ID 37) we identified above.

1. Click on the arrow head on the "Observations_DS_1" sheet, and choose "Duplicate" from the pop-up menu.

![Observations_DS_1 duplication](http://learnlafayette.com/img/misc/Observations-Duplicate-crop.png)

This should result in a new worksheet named "Copy of Observations_DS_1" appearing in your Sheet.

2. Rename "Copy of Observations_DS_1" to "Observations_DS_37".
3. Edit the Datastream ID from "1" to "37" in the API query listed in the formula bar, then hit Enter.

![Observations_DS_37 forumla bar](http://learnlafayette.com/img/misc/FormulaBar.png)

This will cause the ImportJSON script to load Observations for Datastream 37.  There are several fields/columns 
associated with each Observation, but we will focus on "Value Phenomenontime" (column B) and "Value Result" (column C). 
The field "Value Phenomenontime" is how the ImportJSON script translates the name of the "phenomenonTime" attribute 
for each Observation (to review, phenomenonTime is the time an Observation was made by a sensor).  Likewise, the field 
"Value Result" is how ImportJSON refers to the "result" attribute of an Observation (the result is the value read
from the sensor, in this case ozone in units of parts-per-billion).

In the next step, we'll copy the phenomenonTime and result data for Datastream 37 to a new worksheet so that we can
make a chart and do other analyses.

# Step 7: Copy ozone data and calculate summary statistics

1. Click the "+" button at the lower left of the "LEaRN API LPSS" Sheet to create a new worksheet; call this 
worksheet "MyDatastream37_Data"
2. Copy columns C and D from the "Observations_DS_37" worksheet and paste them into columns A and B of the 
"MyDatastream37_Data" worksheet.
3. In the "MyDatastream37_Data" worksheet, change the value of cell A1 from "Value Phenomenontime" to "phenomenonTime",
and change the value of cell B1 from "Value Result" to "ozone". 
4. Select the "phenomenonTime" column (column A) and choose *Format > Number > Date time*.  This will tell Sheets that
this column contains datetime data.

> Note: the phenomenonTime data are stored as [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-formatted date
strings.  ISO stands for Iternational Organization for Standardization.  ISO date strings look like this: 
`2018-01-11T22:45:53.882Z`, which means the Observation was generated on January 11, 2018 at 22-hours, 45-minutes
and 53.882 seconds.  The "Z" at the end of the string indicates that the timezone of this phenomenonTime is 
[UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), which stands for "Coordinated Universal Time", also
known as [Greenwich Mean Time](https://en.wikipedia.org/wiki/Greenwich_Mean_Time) or GMT.

5. Select the "result" column (column B) and choose *Format > Number > Number*.  This tells Sheets that this column
contains numbers.

> Before trying to make charts or to do analyses of data stored in Sheets, it is always a good idea to make sure that
the data type is properly set for each data column in your worksheet.

In the next few steps we'll calculate some summary statistics.

6. In cell D1, type "Min", then in cell D2 type `=min(B1:B1001)`.  This will calculate the minimum ozone value in this
Datastream.
7. In cell E1, type "Max", then in cell E2 type `=max(B1:B1001)`.  This will calculate the maximum value of the data.
8. In cell F1, type "Mean", then in cell F2 type `=average(B1:B1001)`.  This will calculate the average or arithmetic 
mean of the data, which is a measure of central tendency.
9. In cell G1, type "Standard deviation", then in cell G2 type `=stdev(B1:B1001)`.  This will calculate the standard 
deviation of the data; standard deviation is a measure of how much the data vary from the mean.  Higher numbers
indicates greater variability.
10. In cell H1, type "Median", then in cell H2 type `=median(B1:B1001)`.  This will calculate the median of the data;
the median is a measure of central tendency that is less sensitive to extreme values than the mean.

When you're done, your worksheet should look something like this:

![Example summary statistics](http://learnlafayette.com/img/misc/SummaryStatistics.png)

In the next step, we'll make a chart of ozone data.

# Step 8: Graph ozone data

Next we'll create a time series graph using the `phenomenonTime` and `ozone` data in columns A and B of the
"MyDatastream37_Data" sheet.

1. Select columns A and B.  
2. From the *Insert* menu, choose *Chart*.  The Chart editor will appear on the right side of Sheet.

![Chart editor](http://learnlafayette.com/img/misc/ChartEditor1.png)

3. Click on *Chart type* then select "Line chart"
4. Click on the *Customize* tab of the Chart editor.
5. Expand the *Chart & axis titles* section of the Chart editor and under *Title text* enter "Lafayette Middle Ozone" and 
press Enter.
6. From *Type* select *Vertical axis title* and under *Title text* enter "Ozone (ppb)" and press Enter.
7. From *Type* select *Horizontal axis title* and delete the *Title text* and press Enter.
8. Expand the *Horizontal axis* section of the Chart editor and under *Slant labels* choose 60&deg;.
9. Close the Chart editor by clicking on the "X" button in the top right corner.
10. Resize the chart so that all of the X-axis labels can be printed on the graph.  

When you are done, your chart should look something like the graphic below:

![Final chart](http://learnlafayette.com/img/misc/Graph1_final.png)

Next, let's create a scatter plot comparing ozone data for Datastream 37 with ozone data from Datastream 1.

1. Copy column C from the worksheet "Observations_DS_1" to column C of the "MyDatastream37_Data" worksheet.
2. On worksheet "MyDatastream37_Data", change the value of cell C1 from "Value Result" to "ozone_ds1".
3. Select column C (ozone_ds1) and choose *Format > Number > Number*.  This tells Sheets that this column
contains numbers.
4. Select columns B and C.
5. From the *Insert* menu, choose *Chart*.  The Chart editor will appear on the right side of Sheet.
6. From Chart type, choose "Scatter chart".
7. Click on the *Customize* tab of the Chart editor.
8. Expand the *Chart & axis titles* section of the Chart editor and under *Title text* enter "Ozone: DS37 vs. DS1" and 
press Enter.
9. From *Type* select *Vertical axis title* and under *Title text* enter "Ozone DS1 (ppb)" and press Enter.
10. From *Type* select *Horizontal axis title* and under *Title text* enter "Ozone DS37 (ppb)" and press Enter.
11. Expand the *Series* section of the Chart editor and click the box next to *Trendline*, as well as the box next
to *Show R^2.
 
When you are done, your chart should look something like the graphic below:

![Final chart](http://learnlafayette.com/img/misc/Graph2_final.png)

A scatter chart provides a very easy and intuitive way to look for a relationship between two variables.  The trendline
can help to identify possible trends in the data.  In the case of the above graph, the data are the same, so the 
points of the scatter plot perfectly along the diagonal of the graph.  Likewise the R^2 value (also known as the
coefficient of determination) is equal to 1.  The R^2 value represents the amount of variation in the dependent 
variable, Ozone DS1 in this case (the dependent variable is usually plotted along the Y-axis) that can be explained 
using only the independent variable, Ozone DS37 in this case (the independent variable is usually plotted along the 
X-axis).  The R^2 value was generated based on the prediction of the dependent variable Ozone DS1 using a linear 
regression between Ozone DS37.
