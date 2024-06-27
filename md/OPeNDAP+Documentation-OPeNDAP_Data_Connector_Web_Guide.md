<table width=100%>
<tr>
<td width=150>

[image:Opendap_logo_masthead.gif‎](image:Opendap_logo_masthead.gif‎ "wikilink")

</td>
<td>

 <b>OPeNDAP Data Connector</b>

<hr>

 <b>Web Guide</b>
 Version 2.50
 1 June 2004
 funded by the National Oceanographic Partnership Program

</td>
</tr>
</table>

The OPeNDAP Data Connector (ODC) can search for, retrieve and plot
datasets published by OPeNDAP data servers. It can help you preview data
and move it into the application of your choice. The ODC works on
virtually any computing platform including the PC, Macintosh and most
Unix- or Linux-based computers.

This web guide to ODC illustrates all of its major features and shows
you how they work:

# Navigation - Getting Around in the ODC

When you first start the ODC you will see a splash screen while the
program loads and then the interface will appear. Normally the interface
starts on the search page. At the top of interface you will see two rows
of tabs like this:

[image:ss_navigation.png](image:ss_navigation.png "wikilink")


## Using the Tabs

The top row of tabs, search, retrieve, view and help, are the kinds of
features. Each of these has sub tabs. For example, as you can see from
the screen shot above the search tab has four sub-tabs: Dataset List,
Favorites, Recent and GCMD.
There are no menus in the ODC like you find in some applications. This
is because menus are more appropriate for document-based applications
wheras tabbed interfaces are better for control/activity-based
applications like the ODC.
To explore you can return to the web guide or investigate the tabs.
See our [OPeNDAP home](http://www.opendap.org) for more information
about OPeNDAP.

# Searching - Searching for Data

There are four main kinds of search:

1.  <b>Dataset List</b>   A unified list of known data sources
    maintained at UCAR
2.  <b>GCMD</b>   NASA's Global Change Master Directory
3.  <b>Favorites</b>   Datasets that you have asked to be in your
    favorite's list
4.  <b>Recent</b>   Datasets you have recently used

## Using a Search Interface

The basic idea is the same for all four of the search panels. You search
for a dataset you are interested in, select it and then click the red
"To Retrieve" button. The button is red with a network icon to indicate
that when you click it you will be doing a network access.
A shortcut in all the search panels is to double-click the desired
dataset. This has the same effect as clicking the "To Retrieve" button.

See our [OPeNDAP home](http://www.opendap.org) for more information
about OPeNDAP.

## Using the Dataset List

The dataset list search interface looks like this:

[image:ss_datasetlist.png](image:ss_datasetlist.png "wikilink")

On the left side is a directory tree showing various organizations and
their data. On the right is an information pane. When you select a given
dataset information about will appear in the information pane.
The "To Retrieve" button located in the upper left is used to move the
dataset to the retrieve pane where you can get get data out of it.
You can search the entire tree by entering a word in the search box and
clicking the "Search" button. This will winnow the tree so that it
contains only nodes with the requested word. If you search returns no
matches the tree will be unchanged and a message will appear in the
status bar at the bottom of the screen announcing that there were no
matches. To return to the entire tree make the text box blank and click
the search button. There is also a button "Refresh Dataset List". This
will go out onto the network and retrieve the most recent version of the
dataset list available from UCAR.

See our [OPeNDAP home](http://www.opendap.org) for more information
about OPeNDAP.

## Using the GCMD

The GCMD search interface looks like this:

[image:ss_gcmd.png](image:ss_gcmd.png "wikilink")

It has two tabs: Free Text and Keyword search. In the screen shot the
user has done a free text search on the word "aerosol" and the results
that were returned are shown in the list in the middle. When a result is
selected you can click the "Get Summary" button to get further textual
information about the dataset which appears in the lower pane of the
screen. In addition to using search text you can constrain the results
geospatially and temporally. The geospatial constraint panel allows you
to interactively specify a geographic and/or a time constraint for your
search. It includes a user-editable gazetteer. The geo-temporal
contraint panel looks like this:

[image:ss_geospatial.png](image:ss_geospatial.png "wikilink")

If you want to include the entire world you can click the global button
or the clear button (in which case no geographic constraint will be
applied).
To select a region by hand drag the mouse over the area of the world you
wish to do the search in. Note that you can use the border buttons
marked "\>" and "\<" to scroll the entire map left and right. For an
exact latitude/longitude area enter the coordinates in the four boxes to
the right. The map will update to show the region you have specified.
<b>Using the Gazetteer</b> The gazetteer allows you to select a region
of the earth by name. This functionality in the lower portion of the
geo-temporal panel. Select a feature type (land, body of water, mountain
etc) from the box in the lower left. The defined areas for that feature
type will appear in the list next to it. When you select one of these
the map and coordinates will be updated to reflect your selection. The
third list allows you to add your own regions. When you have defined a
region in the map above you can click the "Add" button to add the region
to the gazetteer. You will be prompted for a title for the region. (Unix
users note that the write permissions on the gazetteer.txt file must be
ok). You can also edit the gazetteer.txt file directly. It has been
deliberately designed in a human-readable format so you can edit it and
create your own entries. This file is located in the ODC home directory
(the same place odc.jar is located).

See our [OPeNDAP home](http://www.opendap.org) for more information
about OPeNDAP.

# Retrieving - Retrieving and Subsetting Data

The retrieval panel allows the user to navigate server directories, see
the structure of the data, constrain the data query and output the data
to one of various output options.

## Using the Retrieval Panel

The retrieve panel looks like this:

[image:ss_retrieval.png](image:ss_retrieval.png "wikilink")

When you click "To Retrieve" in one of the search panes (or
double-click) the selected dataset will appear in the "Datasets" area of
this screen (upper left). You can also add an URL manually by typing it
in the "Location" bar at the top of the screen and pressing the "Add to
List" button.
If the URL points to a directory, the directory browser will become
active in the lower left. In the screen shot above the user has selected
a NASA MODIS directory and has drilled down into by double-clicking in
the directory navigator. Note that many of the entries in the navigator
have an ellipsis ("...") after them. That means that the directory has
not been visited so the ODC does not know what they contain. The user
must explicitly double-click a directory to see its contents. This
action does a web hit.
When a directory has been retrieved any files it contains will be shown
in the list to the immediate right of the directory navigator.
Double-clicking a file will cause its structure to be retrieved and
shown in the "Additional Criteria" panel on the right. In the screenshot
above you can see that the user has double-clicked on the file
O04MWN1.sst.ADD2001017.041.2003279195249.hdf.gz and its structure has
been displayed. The file has a lot of variables in it, seven of which
are visible in the Additional Criteria pane.
Selecting and Contraining Data
Use the "Additional Criteria" panel located on the right to select and
constrain the variables you want out of a given dataset. A dataset may
have only one or many variables. The structure of OPeNDAP data is
hierarchical, but the view shown in this panel lists each variable in a
flat list. For example, in the screen above the variable "band_0" is
inside a structure called "band" which itself is inside a structure
called "Calibration".
Here the user has selected two variables "Grid" and "Grid.Data
Fields.sst_mean". Note that the space in "Data Fields" shows as a "%20".
This is an escape mechanism that will allow the request to be sent over
the internet without the space interrupting request.
The user has also sub-sampled the grid by picking a step of 5. This will
return only every fifth value in each dimension (or every 25th value
overall). By doing this the download has been made significantly
smaller.
You can see the size of download for grid and array variables after them
in brackets. For example, in the screen shot above the download will be
5 megabytes. When the user is ready to output the data (here the output
is set to the plotter) the "Output" button will be pressed. The View /
Plotter panel will then automatically be activated and the data will be
downloaded.
See our [OPeNDAP home](http://www.opendap.org) for more information
about OPeNDAP.

# Viewing - Features for Previewing Data

The view / plot panel allows the user to plot the data.

## Using the Plot Panel

The plot panel looks like this:

[image:ss_view_plot.png](image:ss_view_plot.png "wikilink")

When data is sent from the retrieve panel to the plotter using the
"Output to" button, the title of the dataset will appear in the list at
the top of the plot panel (this list can be hidden with the "Show
Datasets" checkbox). After selecting a dataset the user needs to
indicate what kind of plot they want to do, pseudocolor, vector, xy or
histogram. Pick an item from the "Plot Type" drop-down list to do this.
When a given dataset is selected from this list the "Variables" sub-tab
below allows the user to indicate which variable to plot and how to
configure the axes as well as other options. Here the user has indicated
that the variable "dsp_band_1" should be the variable plotted and the
axes should be scaled by the vectors "lat" and "lon". If the data is in
the format of a grid the ODC can automatically configure the axes in
many cases. Select an output destination in the drop-down list next to
the blue "Plot" button and click the button. The plot will be generated.

## Check out the ODC Plot Gallery

<b>Example plots with step-by-step instructions</b> Here is a sampling
of plots all created from within the OPeNDAP Data Connector (ODC) along
with detailed instructions for creating each one. All of the actual png
files you see in the browser were generated directly by the ODC. No
image editing program was used to create or modify the images.
[Starter Tutorial](OPeNDAP_Data_Connector_Tutorial "wikilink") -
Essential for new users
The ODC can generate plots for any OPeNDAP data set. It is a generic
plotter requiring no special configuration, variable conventions or
other setup. The supported plot types are pseudocolor, XY
(line/scatter), vector and histogram. <b>Pseudocolor</b> Pseudocolor or
false color plots associate values or ranges of values in the data with
a particular color. The ODC has a powerful color control capability that
makes it easy to generate and modify color spectrums.

<table border="0" cellpadding="0" cellspacing="0" width="100%">
<td align="center">

[image:ps_bathymetry_index.png](image:ps_bathymetry_index.png "wikilink")
<b>[Carolinas
Bathymetry](OPeNDAP_Data_Connector_Web_Guide#Bathymetry "wikilink")</b>

</td>
<td align="center">

[image:ps_sst_index.png](image:ps_sst_index.png "wikilink")
<b>[Sea Surface
Temperature](OPeNDAP_Data_Connector_Web_Guide#Sea_Surface_Temperature "wikilink")</b>

</td>
<td align="center">

[image:ps_weather_index.png](image:ps_weather_index.png "wikilink")
<b>[Winter
Weather](OPeNDAP_Data_Connector_Web_Guide#Winter_Weather "wikilink")</b>

</td>
</table>

### Bathymetry

<b>Bathymetry and relief plot from the Carolinas Coastal Ocean Observing
and Prediction System </b> The Carolinas Coastal Ocean Observing and
Prediction System sponsors research concerning ocean storms and climate
affecting the Carolinas shore in North America. This plot illustrates a
display of both land relief and bathymetry using their bathymetry data
set. The plot is good way to learn some of the features of the ODC's
color control system.
[image:ps_bathymetry.png](image:ps_bathymetry.png "wikilink")
<b>Step-By-Step</b>

1.  Search / Dataset List
2.  retrieve Carolinas Coastal Ocean Observing and Prediction System /
    Storm Surge directory
3.  open "bathy" directory
4.  double-click bathys.nc file to get DDS
5.  select Grid and bathymetry variables from DDS
6.  output to plotter


[image:ps_bathymetry_intermediate1.png](image:ps_bathymetry_intermediate1.png "wikilink")
<b>Discussion:</b>
In this intermediate plot we can see a couple of things. First of all
the missing values found automatically by the ODC (1.0 4.0 5.0 15.0) are
incorrect. This is because developed areas near the coast are paved over
and have identical elevations. This leads to spikes in the datasets that
the ODC identifies as possible missing values. We can turn this mistake
to our advantage however because it neatly shows where the developed
areas are which will be a good addition to our plot. Another thing about
this plot is that the default colors spread right across the border
between ocean and land. It would be much better if we could set
distinctive colors for the land and ocean. We can generate just such
colors by using the multi-hue color generator. If you are unfamiliar
with the color generator you may want to read the help topic on this
feature to get an idea of what it can do. in the plotter go to the
colors tab
Plot Specification : change "Plots generate their own colors" to "Plot
uses: Default"
Color Generator : create a 2-color multi-hue \[two new ranges will
appear\]
select the default range (that goes from -4197.4 to 275) and delete it
Now we want to make the break between the hues at the 0 elevation (the
coast). select range 1 (-4197 : -1961.2)
change range 1's DataTo range from -1961.2 to 0
select range 2 (-1961.2 : 275.0)
change range 2's DataTo range from -1961.2 to 0
modify the colors of range 1 to go from cyan to magenta continuously
intensify the red highlights by increasing the brightness on the dark
end of range 2
[modify text annotations](Instructions_:_Working_with_Text "wikilink")
[plot it](Instructions_:_Generating_the_Plot "wikilink")
See our [OPeNDAP home](http://www.opendap.org) for more information
about OPeNDAP.

### Sea Surface Temperature

<b>Sea Surface Temperature from MODIS AQUA</b> The highest-quality sea
surface temperature data is derived from the Moderate-resolution Imaging
Spectroradiometer (MODIS) instruments aboard the NASA Terra and Aqua
satellites. Data are currently available globally at 4 km, 36 km and 1
degree resolutions with daily, weekly and monthly time intervals and all
are available via OPeNDAP. This plot shows how easy it is to instantly
generate a pseudocolor with no configuration using the ODC.

[image:ps_sst.png](image:ps_sst.png "wikilink")
<b>Step-By-Step</b>

1.  Go to the dataset list
2.  Expand NASA/JPL Physical Oceanography...(PODAAC)
3.  Expand Sea Surface Temperature
4.  Select 36 km Gridded Daily (Aqua) to the retrieve panel

<b>Discussion:</b>
This is one of the datasets derived from the AQUA satellite. With a lot
of the remotely sensed data there may little or no semantic information
in the data description itself and the file names do not help much
either. To get handle on the data, after you have done the basic
directory retrieval try this technique: copy the initial part of the URL
from the location bar at the top of the retrieval screen (in this case
the URL starts with <http://dods.jpl.nasa.gov> and visit the web site.
You will often find detailed information on the normal website and that
is true of the PODAAC datasets such as MODIS AQUA. Drill down to
2004/001 and select the first file (MY36MDN1.sst...)
Retrieve the structure for MY36MDN1.sst by double-clicking on the file
name
Select the main array by selecting Structure, Structure, SST Mean
\[all three radio buttons should be on\]
Click the red Output to (plotter) button \[red indicates a network
access will occur\]
\[you will now automatically be moved to the View / Plot panel\]
\[the data, about 2M, will download; when it completes the variable
selector tab will populate\]
Click the blue Plot to (preview) button \[the default settings should be
fine for this plot\]
<em>Without changing or specifying an values we have created a
pseudocolor plot. In the published plot there are a couple of minor
changes: the axes are indexed, the ajusted temperature values are shown
and there is different text on the plot. Below is how to make these
changes so your plot looks just like the one above:</em> <em>Modifying
the Text:</em>
Go to the Plot Options tab
Turn off "Generate Axis Captions"
Add the new title, date and colorbar caption
<em>Note that a blocky sans-serif font has been selected and that the
color of the text has been changed to charcoal (set saturation to 0 and
brightness to around 60-80). The title has been layed out left-aligned
to the plot area with a 10-pixel vertical offset and the date is
right-aligned to the plot area with a 4-pixel vertical offset. If you
need more information on managing text see special instructions on
[working with text](Instructions_:_Working_with_Text "wikilink").</em>
<em>Indexing the Axes:</em>
Make sure pseudocolor is the plot type selected
Go to the variable selector tab of the plot panel
\[to left (y-axis) and to the bottom (x-axis) of the axes you will see
the axis mapping selectors\]
In each of the selector drop-down boxes select "\[indexed\]"
<em>Setting the Colorbar Bias:</em>
\[The actual temperature values from the satellite are on a different
scale than the standard of measurement (degrees celsius). We need to
find out what this bias is and adjust for it. Usually you can see the
linear bias values (slope and intercept) in the structure description on
the retrieval pane if you click the "show descriptions" check box, but
here a syntax error is preventing the DAS (description) information from
being read. You can see the warning that this happened if you go to View
/ Text and click the "Show Errors" button. This warning also briefly
appears in the status bar when it occurs. So we need to manually get the
DAS.\] <b>Accessing a DAS Manually:</b>Copy the file URL from the
location bar up to but not including the question mark and append ".DAS"
and paste it in a browser. The DAS text will be shown. At the bottom of
this long file you will see the slope is 0.01 and the intercept is -300.
Now we can proceed... Go to the plot options tab
In the legend section there is a sub-panel entitled "Linear Bias"
Turn off the check "No adjustment"
Enter the slope and intercept determined as in the paragraph above
<b>Tip: Finding More Data</b>
The dataset list only contains a collection of sites and resources we
have identified, but there are a lot of other directories not listed.
When you are interested in big resource site like PODAAC go to their web
site to find out what data they have and then look for the OPeNDAP URLs.
You will often be able to find URLs that are not in our dataset
list.</em>

See our [OPeNDAP home](http://www.opendap.org) for more information
about OPeNDAP.

### Winter Weather

<b>Winter Weather from the National Climatic Data Center (NCDC)</b> With
the OPeNDAP you have instant access to the world's primary weather data
sources.
This plot illustrates how to do animations.
[image:ps_weather.png](image:ps_weather.png "wikilink")
<b>Step-By-Step</b>

1.  Go to the dataset list
2.  Expand NOAA - National Climatic Data Center (NCDC)
3.  Select AWIPS Eta Model Data to the retrieve panel
4.  Drill down the directories to /200401/20040108
5.  Select (single-click) the file early-eta_212_20040108_0000_fff

<b>Discussion:</b>
<em>These weather datasets can be complicated. We need an introduction
to the data. Here's an easy way to get it: notice that when you selected
the file the full URL to that resource appeared in the location bar. It
should read:</em>
<http://nomads.ncdc.noaa.gov:9090/dods/NCDC_NOAAPort_ETA/200401/20040108/early-eta_211_20040108_0000_fff>
<em>This URL points to an OPeNDAP server (aka DODS), but it's a good
guess that the machine and domain have introductory materials. Copy and
paste the first part of the URL (http://nomads.ncdc.noaa.gov) into a
browser. Bingo! The host has a full set of web documents describing the
web resource which you can research to find out what the data means.
Now, armed with this information, we can continue. </em> Double-click
the file to see it's structure
Check "Show Descriptions"
\[this will give use more information on the fields in the data\]
Choose the grid named "cpofpsfc" (select the grid and array variable
cpofpsfc)
\[according to the description this is surface probability of frozen
precipitation, ie snow\]
Output to plotter
[Turn on coastline](Instructions_:_ODC_Options "wikilink")
[Plot it](Instructions_:_Generating_the_Plot "wikilink")
The results should look something like this:

[image:ps_weather_intermediate.png](image:ps_weather_intermediate.png "wikilink")
<b>Discussion:</b>
<em>Besides adding nice text labeling there is another thing we can do
to improve the plot. Notice that the ODC has automatically determined
missing values to be -9.99E33, 0, and 100. If you look back at the data
description on the retrieve panel you can see it only lists the -9.99E33
which it calls a "fill value". The reason why the ODC has also
identified 0 and 100 is that they have solid continuous areas that cause
value spikes in the data. The ODC tends to regard these kind of spikes
as likely missing values. We can improve our plot by making dedicated
colors for these special values.</em> <b>Add Colors for Key
Values:</b>
Go to the Colors tab
Click "Plot Uses"
\[the color editor will become active\]
Select the Missing range
Edit the missing values text box in the range editor (middle of screen)
by deleting 0 and 100
Press enter to save your changes
\[notice how the range list updates to reflect your change\]
Add a new range (add range button is in lower left corner)
Select the new range
\[notice that an interface to modify the range appears in the range
editor\]
Change "Single Value" to "Yes"
Change the data value to 100
Change the color to a slightly dark green (I used 47 FF D0 )
Repeat for the 0 value but used a washed out, low-saturation, yellow (29
50 FF)
\[coloring the 0 value will show us the satellite swath\]
<b>Modifying the Text:</b>

- Go to the Plot Options tab
- Turn off "Generate Axis Captions"
- \[Add the new title, date and axes captions\]

<b>Animate the Plot:</b>

- Go to the Variables tab
- Change the Time constraint from "1" to "1-17"
- [Plot it](Instructions_:_Generating_the_Plot "wikilink")

\[you are watching the snow pattern over a 17-hour period on January 8,
2004\]
\[tip: if your image size is too large you could run out of memory doing
this\]
<b>Tip: Finding More Data</b>
<em>The dataset list only lists some of the data available at NCDC.
Explore there to find more OPeNDAP data sets.</em>

See our [OPeNDAP home](http://www.opendap.org) for more information
about OPeNDAP.