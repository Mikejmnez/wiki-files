An OPeNDAP Quick Start Guide

<font size="-1">This guide to using OPeNDAP's software was originally
written by Tom Sgouros</font>

\[<http://www.opendap.org/><cite>OPeNDAP</cite>\] provides software that
allows you to access data over the internet, from programs that weren't
originally designed for that purpose, as well as some that were. While
OPeNDAP is the original developer of the [Data Access
protocol](http://www.opendap.org/pdf/ESE-RFC-004v1.1.pdf) which its
software uses, many other groups have adopted DAP and provide compatible
clients, servers and software development kits. The DAP is a [NASA
community
standard](http://earthdata.nasa.gov/our-community/esdswg/standards-process-spg);
here is the [offical
link](http://earthdata.nasa.gov/our-community/esdswg/standards-process-spg/rfc/esds-rfc-004-dap-20)
to the specification.

With OPeNDAP software, you access data using a URL, just like a URL you
would use to access a web page. However, before you request any data,
you need to know how to request it in a form your browser can handle.
OPeNDAP data is stored in binary form, and by default, it is transmitted
that way, too.

Another thing to consider with an OPeNDAP URL is that a single URL might
point to an archive containing 50 megabytes of data. You rarely want to
request the whole thing without knowing a little about it. OPeNDAP
provides sophisticated sub-sampling capabilities, but you need to know
something about the data in order to use them.

So here's what to do if someone gives you a raw URL, and says there's
some OPeNDAP data on the other end.

# What To Do With An OPeNDAP URL

Suppose someone gives you a hot tip that there's a lot of good data at:

    http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz

This URL points to monthly means of sea surface temperature, worldwide,
compiled by Richard Reynolds at the Climate Modeling branch of NOAA, but
pretend you don't know that yet.

The simplest thing you can do with this URL is to download the data it
points to. You could feed it to an OPeNDAP-enabled data analysis package
like Ferret, or you could append **.ascii**, and feed the URL to a
regular web browser like Firefox or Safari. This will work, but you
don't really want to do it because in binary form, there are about 60
megabytes of data at that URL.

> NOTE: An OPeNDAP server will work with many different clients, some of
> which are supported by the OPeNDAP team, and some of which are
> supported by others. The operation of any individual package is beyond
> the scope of this manual. This guide explains how to use a typical web
> browser such as Firefox, Internet Explorer or Safari to discover
> information about the data that will be useful when analyzing data in
> *any* package.

A better strategy is to find out some information about the data.
OPeNDAP has sophisticated methods for subsampling data at a remote site,
but you need some information about the data first. First, we'll try
looking at the data's Dataset Descriptor Structure (DDS). This provides
a description of the "shape" of the data, using a vaguely C-like syntax.
You get a dataset's DDS by appending **.dds** to the
[URL](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.dds).

<center>

<figure>
<img src="Reynolds_dds.png" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

[An OPeNDAP
DDS(sst.mnmean.nc.gz.dds)](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.dds)

</center>

From the DDS shown, you can see that the dataset consists of two
different pieces:

- A "Grid" containing a three-dimensional array of integer values
  (Int16) called sst, and three "Map" vectors:
  - A 89-element vector called "lat",
  - A 180-element vector called "lon",
  - A 1857-element vector called "time", and
- A 1857 by 2 array called "time_bnds".

The Grid is a special OPeNDAP data type that includes a multidimensional
array, and map vectors that indicate the independent variable values.
That is, you can use a Grid to store an array where the rows are not at
regular intervals. Here's a diagram of a simple grid:

<center>

<figure>
<img src="gridpts.gif" title="Image:gridpts.gif" />
<figcaption>Image:gridpts.gif</figcaption>
</figure>

A Grid

</center>

The array part of the grid (like **sst** in the example above) would
contain the data points measured at each one of the squares, the X map
vector would contain the horizontal positions of the columns (like the
**lon** vector above), and the Y map vector would contain the vertical
positions of the rows (like the **lat** vector above).

Of course you can also use a Grid to store arrays where the columns and
rows are at regular intervals, and you'll often see OPeNDAP data stored
that way.

(The other special OPeNDAP data type worth worrying about is the
*Sequence* . You'll see more about them later. There are also
*Structures* for representing arbitrary hierarchies.)

You can see from the DDS that the Reynolds data is in a 89x180x1857
element grid, and the dimensions of the Grid are called "lat", "lon",
and "time". This is suggestive, but not as helpful as one could wish. To
find out more about what the data *is*, you can look at the other
important OPeNDAP structure: the Data Attribute Structure, or DAS. This
structure is somewhat similar to the DDS, but contains information about
the data, such as units and the name of the variable. Part of the DAS
for the Reynolds data we saw above is shown in the figure below. Click
[sst.mnmean.nc.gz.das](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.das)
to see the rest of it.

<center>

<figure>
<img src="Reynolds_das.png" title="Image:Reynolds_das.png" />
<figcaption>Image:Reynolds_das.png</figcaption>
</figure>

[sst.mnmean.nc.gz.das](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.das)

</center>

> NOTE: Unlike the DDS, the DAS is populated at the data
>
> ` provider's discretion.  Because `
> ` of this, the quality of the data in it (the metadata) varies`
> ` widely.  The data in the Reynolds dataset used in this example are`
> ` COARDS compliant.  Other metadata standards you may encounter with`
> ` OPeNDAP data are HDF-EOS, EPIC, FGDC, or no metadata at all.`

Now we can tell something more about the data. Apparently the **lat**
vector contains latitude, in degrees north, and the range is from 89.5
to -89.5. Since this is a global grid, the latitude values probably go
in order. We can check this by asking for just the latitude vector, like

    http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?lat

What we've done here is to append a constraint expression to the OPeNDAP
URL, to indicate how to constrain our request for data. Constraint
expressions can take many forms. This guide will only describe a few of
them. (You can refer to the [User's
Guide](http://docs.opendap.org/UG/OPeNDAP_User%27s_Guide) for more
complete information about constraint expressions.) Try requesting the
[time](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?time)
and
[longitude](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?lon)
vectors to see how this works.

According to the DAS, time is kept in "days since 1800-1-1 00:00:00" in
this dataset. This DAS also contains the actual time period recorded in
the data (19723 to 76214) which, because of your familiarity with the
Julian calendar, you instantly recognize as beginning January 1, 1854.

OPeNDAP provides an **info** service that returns all the information
we've seen so far in a single request. The returned information is also
formatted differently (some would say "nicer"), and you can occasionally
find server-specific documentation here, as well. Some will find this
the easiest way to read the attribute and structure information
described above. You can see what information is available by appending
**.info** to a URL, like
\[<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.info><cite>this</cite>\]:

    http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.info

## Peeking at Data

Now that we know a little about the shape of the data, and the data
attributes, let's look at some of the data.

You can request a piece of an array with subscripts, just like in a C
program or in Matlab or many other computer languages. Use a colon to
indicate a subscript range.

[...sst/mnmean.nc.ascii?time\[0:6\]](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?time%5b0:6%5d)

This
[URL](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?time%5b0:6%5d)
will produce

<center>

<figure>
<img src="Reynolds_time_vector.png"
title="Image:Reynolds time vector.png" />
<figcaption>Image:Reynolds time vector.png</figcaption>
</figure>

Part of a vector

</center>

If you are interested in the Reynolds dataset, you are probably more
interested in the sea surface temperature data than the dependent
variable vectors. The temperature data is a three-dimensional grid. To
sample the
[sst](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?sst%5b0:1%5d%5b13:16%5d%5b103:105%5d)
Grid, you just add a dimension for time:

\[<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?sst%5b0:1%5d%5b13:16%5d%5b103:105%5d>...sst/mnmean.nc.ascii?sst\[0:1\]\[13:16\]\[103:105\]\]

This produces something like:

<center>

<figure>
<img src="Reynolds_sst.png" title="Image:Reynolds_sst.png" />
<figcaption>Image:Reynolds_sst.png</figcaption>
</figure>

[Part of the Reynolds SST
data](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?sst%5b0:1%5d%5b13:16%5d%5b103:105%5d)

</center>

Notice that when you ask for part of an OPeNDAP Grid, you get the array
part along with the corresponding parts of the map vectors.

One potentially confusing thing about this request is that we requested
the time, latitude and longitude by their position in the map vectors,
but in the returned information they are referenced by their values.
That is, we asked for the 0th and 1st time values, but these are 19723
and 19754. We also asked for the 103rd, 104th and 105th longitude
values, but these are 206, 208, and 210 degrees, respectively. The value
434 in the return can be referenced as

\[<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?sst%5b1%5d%5b15%5d%5b103%5d>...sst/mnmean.nc.ascii?sst\[1\]\[15\]\[103\]\]

Note that the sst values are in Celsius degrees multiplied by 100, as
indicated by the **scale_factor** attribute of the
[DAS](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.das).
Further, it's important to remember with this dataset, that the data
were obtained by calculating spatial and temporal means. Consequently,
the data points in the **sst** array should be ignored when the value is
the missing data flag (32767) as these pixels are probably coincident
with land (although there can be other reasons for missing data).

### Server Functions: Looking at geo-referenced data using Hyrax

There are a number of different DAP servers that have been developed by
different organizations. Hyrax, the DAP server developed by the OPeNDAP
group, supports access to geo-referenced data using lat/lon coordinates.
You probably noticed that the array and grid indexes used so far are not
very intuitive. You can see the data are global and are indexed by
latitude and longitude, but in the previous example we first looked at
the lat and lon vectors, saw which indexes corresponded to which
real-world locations and then made our accesses using those indexes.

Hyrax supports a small set of functions which can perform these look-up
operations for you. For example, we could rewrite the example above like
this:

\[<http://test.opendap.org/opendap/data/nc/sst.mnmean.nc.gz.ascii?geogrid(sst,62,206,56,210,%2219722%3Ctime%3C19755%22>)...mnmean.nc.gz.ascii?geogrid(sst,62,206,56,210,"19722\<time\<19755")\]

This produces:

<center>

<figure>
<img src="Reynolds_sst_geogrid.png"
title="Image:Reynolds sst geogrid.png" />
<figcaption>Image:Reynolds sst geogrid.png</figcaption>
</figure>

\[<http://test.opendap.org/opendap/data/nc/sst.mnmean.nc.gz.ascii?geogrid(sst,62,206,56,210,%2219722%3Ctime%3C19755%22>)
Part of the Reynolds SST data\]

</center>

The Syntax for `geogrid()` is:


geogrid(grid variable, upper latitude, left longitude, lower latitude,
right longitude, *other expressions*)

Where *other expressions* must be enclosed in double quotes, and can be
one of these forms:


variable relop value

value relop variable

value relop variable relop value

"Relop" stands for one of the relational operators: \<,\>,\<=,\>=,=,!=.
"Value" stands for a numeric constant, and "Variable" must be the name
of one of the grid dimensions. You can use multiple clauses by
separating them with commas, but each clause must be surrounded by
double quotes. For example, the following is yet another way to get the
same return data as the above example.

\[<http://test.opendap.org/opendap/data/nc/sst.mnmean.nc.gz.ascii?geogrid(sst,62,206,56,210,%2219722%3Ctime%22,%22time%3C19755%22>)...mnmean.nc.gz.ascii?geogrid(sst,62,206,56,210,"19722\<time","time\<19755")\]

You can figure out which functions are supported by Hyrax by calling the
server function
\[<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?version>()
version()\]. This will return an XML document that shows each registered
function and its version.

To find out how to call each function, you can call it with an empty
parameter list and get some documentation for that function. For
example, try
\[<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.ascii?geogrid>()...?geogrid()\].

### More fun with Server Functions

Server functions can be composed to form pipelines, feeding the value of
one function to another. Since the values in this data set are scaled up
by a factor of 100, we can use the *linear_scale()* function to scale
the result using

    y = mx + b

where **m** is the scale factor and **b** offset. The *linear_scale()*
function syntax is:


linear_scale(variable, scale factor, offset)

linear_scale(variable)

Use the first form when you want to specify **m** and **b** explicitly
or the second form when Hyrax can guess the values using data set
metadata (you'll get an error if the server cannot figure out value to
use).

\[<http://test.opendap.org/opendap/data/nc/sst.mnmean.nc.gz.ascii?linear_scale(geogrid(sst,78,0,56,10,%22time=19723%22>),0.01,0)...nc.gz.ascii?linear_scale(geogrid(sst,78,0,56,10,"time=19723"),0.01,0)\]

This produces:

<center>

<figure>
<img src="Reynolds_sst_linear_scale_geogrid.png"
title="Image:Reynolds sst linear scale geogrid.png" />
<figcaption>Image:Reynolds sst linear scale geogrid.png</figcaption>
</figure>

\[<http://test.opendap.org/opendap/data/nc/sst.mnmean.nc.gz.ascii?linear_scale(geogrid(sst,78,0,56,10,%22time=19723%22>),0.01,0)
Part of the Reynolds SST data, scaled\]

</center>

## Sequence Data

Gridded data works well for satellite images, model data, and data
compilations such as the Reynolds data we've just looked at. Other data,
such as data measured at a specific site, is not so readily stored in
that form. OPeNDAP provides a data type called a Sequence to store this
kind of data.

A Sequence can be thought of as a relational data table, with each
column representing a different data variable, and each row representing
a different measurement of a set of values (also called an "instance").
For example, an ocean temperature profile can be stored as a Sequence
with two columns: pressure and temperature. Each measurement is a
pressure and a temperature, and is contained in one row. A weather
station's data can be stored as a Sequence with time in one column, and
each weather variable occupying another column.

You can find a good example of a Sequence at:

[<http://test.opendap.org/dap/data/ff/gsodock.dat>](http://test.opendap.org/dap/data/ff/gsodock.dat.info)

This is a 24-hour record of measurements at a weather station on a dock
in Rhode Island. Each record consists of a dozen different variables
including air temperature, wind speed, and direction, as well as depth,
temperature and salinity of the water. The data is arranged into 144
measurements of each of the twelve variables.

Ask for the DDS, and you'll see the twelve variables, all contained in a
Sequence called URI_GSO-Dock:

<center>

<figure>
<img src="gsodock-dds.png" title="Image:gsodock-dds.png" />
<figcaption>Image:gsodock-dds.png</figcaption>
</figure>

\[<http://test.opendap.org/dap/data/ff/gsodock.dat.dds>A DDS for
Sequence data\]

</center>

The DAS contains the units for each data type, and a little other
information:

<center>

<figure>
<img src="gsodock-das.png" title="Image:gsodock-das.png" />
<figcaption>Image:gsodock-das.png</figcaption>
</figure>

\[<http://test.opendap.org/dap/data/ff/gsodock.dat.das>A DAS for
Sequence data\]

</center>

To select which data you want from a server, use a constraint
expression, just as you did with the gridded data above. Now, though,
the constraint contains two kinds of clauses. One is a list of variables
you wish to have returned, and the other is the conditions under which
they should be returned. The first is called the **projection** clause
and the second the **selection** clause.

For example, if you want to see salinity data read after noon that day,
try this:

\[<http://test.opendap.org/dap/data/ff/gsodock.dat.ascii?URI_GSO-Dock.Salinity&URI_GSO-Dock.Time%3E35234.5>...gsodock.dat.ascii?URI_GSO-Dock.Salinity&URI_GSO-Dock.Time\>35234.5\]

Selection clauses can be stacked endlessly against a projection clause,
allowing all the flexibility most people need to sample data files.
Here's an example of applying two conditions:

\[<http://test.opendap.org/dap/data/ff/gsodock.dat.ascii?URI_GSO-Dock.Salinity&URI_GSO-Dock.Time%3E35234.5&URI_GSO-Dock.Depth%3E2>...gsodock.dat.ascii?URI_GSO-Dock.Salinity&URI_GSO-Dock.Time\>35234.5&URI_GSO-Dock.Depth\>2\]

Try it yourself with three or four conditions, or more.

## An Easier Way

OPeNDAP also includes a way to sample data that makes writing a
constraint expression somewhat easier. Append **.html** to the URL, and
you get a form that directs you to add information to sample the data:
\[<http://test.opendap.org/opendap/data/nc/sst.mnmean.nc.gz.html>...sst.mnmean.nc.html\]

Sending a URL ending in **.html** returns a form like this:

<center>

<figure>
<img src="Reynolds_ifh.png" title="Image:Reynolds ifh.png" />
<figcaption>Image:Reynolds ifh.png</figcaption>
</figure>

\[<http://test.opendap.org/opendap/data/nc/sst.mnmean.nc.gz.html>The
OPeNDAP Dataset Access Form\]

</center>

It's useful to have a browser window open with one of these query forms
in it while you read this section. Right or Control click
\[<http://test.opendap.org/opendap/data/nc/sst.mnmean.nc.gz.html>here\]
to bring up a copy of the form to use while you read.

Near the top of the page, you'll see a box entitled "Data URL". At this
point, if you've been following along, it should look pretty familiar.
If you're just jumping in, it's the OPeNDAP URL connected to the data
we're interested in, but unsampled (that is, there's no constraint
expression on it).

Moving down the page, there is a list of "Global Attributes", which is
really just for your perusal. There's not much to be done with this, but
it is often helpful information.

The important part of the page is the "Variables" section. For each
variable in the dataset, you'll see the data description (something like
"Array of 32 bit Reals \[lat = 0..88\]"), a checkbox, a text input box,
and a list of the variable's attributes. If you click on the checkbox,
you'll see the variable's array bounds appear in the text box, and
you'll see the variable appear in a constraint expression appended to
the Data URL at the top of the page. If you edit the array bounds in the
text box, hitting "enter" will place your edits in the Data URL box.

In the unlikely event you dare try all this without your documentation
along, there's a **Show Help** button up near the top of the page.
Clicking there will show you instructions about how to proceed.

> NOTE: You'll see a "stride" mentioned. This is another way to
> subsample an OPeNDAP array or Grid. Asking for **lat\[0:4\]** gets you
> the first five members of the **lat** array. Adding a stride value
> allows you to skip array values. Asking for **lat\[0:2:10\]** gets you
> every second array value between 0 and 10: 0, 2, 4, 6, 8, 10.

Move on down the variable list, editing your request, and experiment
with adding and changing variable requests.

When you have a request you'd like to make, look at the buttons at the
top of the page.

<center>

<figure>
<img src="Reynolds_ifh_Action.png"
title="Image:Reynolds_ifh_Action.png" />
<figcaption>Image:Reynolds_ifh_Action.png</figcaption>
</figure>

Dataset Access Form Detail

</center>

You can click on **Get ASCII**, and the data request will appear in a
browser window, in comma-separated form. The **Binary (DAP) Object**
button will save a binary data file on your local disk, and the **Get as
NetCDF** will save the file in netCDF format on your local disk. (You
can read either of these later with several OPeNDAP clients, by giving
it the file name instead of a URL.)

The OPeNDAP Data Access Form interface works for Sequence data as well
as Grids. However, since Sequence constraint expressions look different
than Grid expressions, the form looks slightly different, too. You can
see that the variable selection boxes allow you to enter relational
expressions for each variable. Beside that, however, the function is
exactly the same.

<center>

<figure>
<img src="gsodock-html.png" title="Image:gsodock-html.png" />
<figcaption>Image:gsodock-html.png</figcaption>
</figure>

Dataset Access Form for Sequence Data (detail)

</center>

Click \[<http://test.opendap.org/dap/data/ff/gsodock.dat.html>here\] to
see a copy of a Sequence form. Click the checkboxes to choose which data
types you want returned, and then add constraint expressions as desired.
This data file contains a day's record of changing water properties off
a dock in Rhode Island. If you click the *Depth* and *Time* boxes (as in
the figure), you'll get a record of the tide going in and out twice. You
can add conditions by entering values in the text boxes. See what you
get when you limit the selection to records where the Depth is greater
than 2 meters.

> NOTE: The OPeNDAP project supports the server standard and also
> provides server software. Other groups also provide server software.
> This means that not all OPeNDAP servers support all the OPeNDAP
> functionality. There are a few OPeNDAP servers out there in the world
> that only support the bare minimum required by the standard. That
> minimum is to respond to queries for the DDS, DAS, and (binary) data.
> The ASCII data and the web access form are optional add-ons that are
> not required for the basic OPeNDAP function.

# Finding More OPeNDAP URLs

The OPeNDAP package was developed to improve ways to share data among
scientists. Many times, data comes in the form of a URL enclosed in an
email message. But there are several other ways to find data served by
OPeNDAP servers.

## GCMD

The \[<http://gcmd.gsfc.nasa.gov>Global Change Master Directory\] is a
source of a huge amount of earth science data. They now catalog OPeNDAP
URLs for the datasets that have them. You can search on "OPeNDAP" right
from the main page to find many of these datasets. Try that search, then
click on one of the data set names that returns, and look at the bottom
of the resulting Set Description*page, under the heading \`\`Related
URL.*

If you make that search, check the list for the Reynolds data from
chapter~1; it should be there.

## Web Interface

This is a little bit sneaky. Many sites that serve one OPeNDAP dataset
also serve others. The OPeNDAP web interface (if it's enabled by the
site) allows you to check the directory structure for other datasets.
For example, let's look at the
\[<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.html>Reynolds
data\] we saw previously:

\[<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.html>http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.html\]

If we use the same URL, but without the file name at the end, we can
browse the directory of data:

\[<http://test.opendap.org/dap/data/nc/>http://test.opendap.org/dap/data/nc/\]

The OPeNDAP server checks to see whether the URL is a directory, and if
so, it generates a directory listing, like
\[<http://test.opendap.org/dap/data/nc/>this:\]

<center>

<figure>
<img src="Test.oopendap.org_directory_view.png"
title="Image:Test.oopendap.org directory view.png" />
<figcaption>Image:Test.oopendap.org directory view.png</figcaption>
</figure>

\[<http://test.opendap.org/dap/data/nc/>Web Interface Index Listing \]

</center>

You can see from the directory listing that the monthly mean dataset
we've been looking at is accompanied by a host of other datasets. The
site you're looking at is our test data site - we use these datasets to
run many of our nightly tests. All of the files in the the */data/nc*
directory are stored in NetCDF files; other directories under */data*
hold data stored in other file types.

> **Note:** In general, this list is produced by an OPeNDAP server and
> this feature works on all servers. However, it only really understands
> OPeNDAP data files, so other file types will simply be sent without
> any interpretation. This can be useful if the 'other file' happens to
> be a README or other documentation file since this makes it simple to
> serve data stored in files and documented using plain text files -
> essentially the person or organization providing data doesn't need to
> do anything besides [installing the server](Hyrax "wikilink").

## File Servers

Some datasets you'll find are actually lists of other datasets. These
are called *file servers* and are themselves OPeNDAP datasets, organized
as a Sequence, containing URLs with some other identifying data (often
time). You can request the entire dataset, or subsample it just like any
other OPeNDAP dataset.

NASA's atmospheric composition data information services maintains some
OPeNDAP file servers:

\[<http://acdisc.sci.gsfc.nasa.gov/opendap/catalog/DatapoolCatalog/AIRS/contents.html>http://acdisc.sci.gsfc.nasa.gov/opendap/catalog/DatapoolCatalog/AIRS/contents.html\]

Try selecting one of the datasets listed in the above, and look at the
DDS and DAS of that dataset. You'll see it's a list of OPeNDAP URLs
(called **DODS_URL** here), labeled with the date of measurement. If you
go to the [html
form](http://acdisc.sci.gsfc.nasa.gov/opendap/catalog/DatapoolCatalog/AIRS/AIRX3C2M_005-cat.dat.html)
for one of them, and click on the **DODS_URL** checkbox to get a list of
URLs, and then add some conditions (try limiting the files to data from
2003), and click **Get ASCII**. Now you can cut and paste the resulting
URLs to get more data.

# Further analysis

This guide is about forming an OPeNDAP URL. After you have figured out
how to request the data, there are a variety of things you can do with
it. (OPeNDAP software mentioned here is available from the
\[<http://www.opendap.org>OPeNDAP Home Page\] .)

- Use a generic web client like **geturl** (a standard part of the
  OPeNDAP package), the free programs
  \[<http://www.gnu.org/manual/wget-1.5.3/html_mono/wget.html>wget\] or
  [lynx](http://lynx.browser.org), or even a browser like **Netscape
  Navigator** or **Internet Explorer** to download data into a local
  data file. To be able to use the data further, you will probably have
  to download the ASCII version by using the **.ascii** suffix on the
  URL, as in the examples shown.

<!-- -->

- There are pre-packaged OPeNDAP clients available that can download
  binary OPeNDAP data from the web into a useful form. As of today,
  command line clients (**loaddods**) are available for the Matlab and
  IDL data analysis environments, with which you can download OPeNDAP
  data directly into IDL or Matlab objects.

<!-- -->

- The [Ferret](http://ferret.wrc.noaa.gov/Ferret) and
  [GrADS](http://www.iges.org/grads/) free data analysis packages both
  support OPeNDAP. You can use these for downloading OPeNDAP data, and
  for examining it afterwards. (There are limitations. As of \today ,
  Ferret can not read datasets served as Sequence data.)

<!-- -->

- The Matlab analysis package also supports an OPeNDAP client attached
  to a graphical user interface. You can use the GUI to create a
  constrained OPeNDAP URL, and download the data directly into Matlab.
  The \[<http://www.opendap.org/user/mgui-html/mgui.html>The OPeNDAP
  Matlab GUI\] contains more information about the Matlab GUI client.

<!-- -->

- If you have a data analysis program or package that you like, you can
  look into the possibility of linking that package to the OPeNDAP
  toolkit library, in effect making your program into a web-capable
  OPeNDAP client. OPeNDAP libraries exist to mimic the behavior of the
  \[<http://www.unidata.ucar.edu/software/netCDF/>netcdf\],
  \[<http://www.hdfgroup.org/>HDF\] and
  \[<http://www1.whoi.edu/jgofs.html>JGOFS\] data access APIs. If your
  program already uses one of these APIs, getting it to run with OPeNDAP
  may be as simple as changing the libraries to which you link it. The
  \[<http://www.opendap.org/user/guide-html/guide.html>The OPeNDAP User
  Guide\] describes how to do this, and the
  \[<http://www.opendap.org/api/pguide-html/pguide.html>The OPeNDAP
  Toolkit Programmer's Guide\] describes how you can use the OPeNDAP
  toolkit directly to create a new application that doesn't use one of
  the established data access APIs.

The use of these clients, like the ways in which you can analyze the
data you find, is beyond the scope of this document. Enjoy.