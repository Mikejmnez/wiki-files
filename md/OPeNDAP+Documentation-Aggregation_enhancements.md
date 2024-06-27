In response to requests from NASA, and with their support, we have added
two new kinds of aggregation to Hyrax. Both of these aggregation
operations provide a way for client software to specify the granules
that will be used to build the aggregate result. While our existing
aggregation interface, based on NcML, works well for NASA's level 3 data
products, it is all but useless for level 2 *swath data*. These
aggregation functions are specifically designed to work with satellite
swath data without being limited to just swath data and are explicitly
intended for use with search interfaces that have knowledge of the
individual files that make up typical satellite data sets (often called
a *dataset inventory*).

## Overview of the new capability

**Summary**: This service provides value-based subsetting for satellite
*swath data*. It's applicable to lots of other kinds of data, but works
best with data that meet certain requirements.

Providing search results that include explicit references to hundreds or
thousands of discrete files has been the only option for many search
interfaces up to this point. This is especially when the datasets holds
satellite swath data because swath data are not easily aggregated. For
this interface to Hyrax's aggregation software, we provide two kinds of
responses: Data in multiple files that are bundled together using an zip
archive and data in tabular form. For clients that request the aggregate
result in a zip file, given a request for values from N files, there
will be N entries in the resulting zip archive. Some of these entries
may simply indicate that no data matching the spatial or other
constraints were found. While the source data files can be in any format
that the Hyrax server can read, the response will be either netCDF3,
netCDF4 or ASCII. The netCDF3/4 files returned will conform to CF 1.6 to
the extent possible (the underlying data files may lack information CF
1.6 requires). For clients that request data in tabular form, the data
from N files will be returned in one ASCII CSV response. These values
can be easily assimilated by database systems, Excel and other tools.

### Intended audience

This service was originally intended for software developers working
data search tools who need to be able to return results that encompass
hundreds or thousands of granules. It works best from a programmatic
interface, but it's certainly open to end users, see the examples using
curl for one way to access the service.

### Accessing the Aggregation Services

This 'service' is accessed using HTTP's GET or POST methods. In this
documentation I will describe how to use POST to send information, but
the same key-value parameters can be sent using the GET method, albeit
within the character limits of a URL (which vary depending on
implementation).

The service is accessed using the following set of key-value parameters:

operation: Use *operation* to select from various kinds of responses. The form of the response also determines how the aggregation is built. The current values for this parameter are: *version* which returns information about the service's version; *file* returns a collection of files; *netcdf3*, *netcdf4*, *ascii* all translate the underlying granule format to netcdf3, etc., and return that collection of translated files; *csv* returns data from many granules as a single table of data, using Comma Separated Values (csv) format. More information about this is given below.
file: The URL path component to a granule served by Hyrax. This parameter will appear once for each file in the aggregation.
var: A comma-separated list of variables to include in the files returned when using *operation* equal to *netcdf3*, *netcdf4*, *ascii*, or *csv*
bbox: Limit the values returned to those that fall within a bounding box for a given variable. Like *var*, this applies only to *netcdf3*, *netcdf4*, *ascii*, or *csv*

##### How to use these parameters

The *operation* and *file* parameters are the key to the service. By
listing multiple files, you can explicitly control which files are
accessed and the order of that access. The *operation* parameter
provides a way to choose between a zipped response with many files
either in their native format (*file*) or in one of three well known
representations (*netcdf3*, netcdf4*or*ascii'').

While a complete request can make use of only the *operation* and *file*
parameters, adding the variable and value subsetting can provide a much
more manageable response. The *var* and *bbox* parameters can appear
either once or *N* times where *N* is the number of time the *file*
parameter appears. In the first case, the values of the single instances
of *var* and/or *bbox* are applied to every file/granule listed in the
request. In the second case the value of *var1* is used with *file1*,
*var2* with *file2*, and so on up to *varN* and *fileN*. The same is
true of the *bbox* parameter. Furthermore, these parameters act
independently, so a request can use one value for *var* and *N* values
for *bbox* or vice versa.

##### Response formats

This service will either return a collection of files bundled in a *zip
archive* or it will return a since CSV/text file. When *operation* is
*file*, *netcdf3*, *netcdf4* or *ascii*, the service will take each of
the files as they are retrieve or built and put them in a zip archive
that it streams back to the client. The ZIP64(tm) format extensions are
used to overcome the size limitations of the original ZIP format.

For the *csv* operation, the response is a single CSV/text file.

##### More about *var*

The *var* parameter is a comma-separated list of variables in the files
listed in the request. Each of the variables must be named just as it is
in the DAP dataset. If you're getting errors from the service that 'No
such variable exists in the dataset ...', use a web browser or *curl* to
look at one of the granules and see what the exact name is. For many
NASA dataset, these names can be quite long and have several components,
separated by dots. One way to test the name is to build a URL to the
file and use the *getdap* (part of the libdap software package) tool
like this


*getdap -d <url> -c <var name>*

If this returns an error, look at the DDS or DMR from the dataset and
figure out the correct name. Do that using


*getdap -d <url>* or

*getdap4 -d <url>*

##### More about *bbox*

The *bbox* parameter is probably the most powerful of the parameters in
terms of its ability to select specific data values. It has two
different modes, one when used with the zip-formatted responses (i.e.,
*operation* is *netcdf3*, *netcdf4* or *ascii*) and another when its
used with *operation* equal to *csv*. However, ther are somethings that
are common to both uses of the parameter. In either case, *bbox* is used
to select a range of values for a particular variable or a set of
variables. The format for a *bbox* request has the following form


*\[* <lower value> *,* <variable name> *,* <upper value> *\]*

for each variable in the subset request. If more than one variable is
included, use a series of *range requests* surrounded by double quotes.
An example *box* request looks like


&bbox="\[49,Latitude,50\]\[167,Longitude,170\]"

which translates to *"for the variable Latitude, return only values
between 49 and 50 (inclusive) and for the variable Longitude return only
values between 167 and 170"*. Note that the example here uses two
variables named *Latitude* and *Longitude*, but any variables in the
dataset could be used.

The *bbox* operation is special, however, because the range limitation
applies not only to the variable listed, but to any other variables in
the request that share dimensions with those variables. Thus, for a
dataset that contains *Latitude*, *longitude* and *Optical_Depth* where
all have the shared dimensions *x* and *y*, the *bbox* parameter will
choose values of *Latitude* and *Longitude* within the given values and
then apply the resulting bounding box to those variables and any other
variables that use the same named dimensions as those variables. The
named (i.e., *shared*) dimensions form the linkage between the
subsetting of the variables named in the *bbox* value subset operation
and the other variables in the list of *var*s to return.

You can find out if variables in a dataset share named dimensions by
looking at the DDS (DAP2) or DMR (DAP4) for the dataset. Note that for
DAP4, in the example used in the previous paragraph, *Latitude*,
*longitude* and *Optical_Depth* form a 'coverage' where *Latitude* and
*longitude* are the domain and *Optical_Depth* is the 'range'.

Note that the variables in the *bbox* range requests must also be listed
in the *var* parameter if you want their values to be returned.

The next two sections describe how the return format (zipped collection
of files or CSV table of data) affects the way the *bbox* subset request
is interpreted.

###### *bbox & zip-formatted returns*

When the Aggregation Service is asked to provide a zipped collection of
files (*operation* = *netcdf3*, *netcdf4* or *ascii*), the resulting
data is stored as N-dimensional arrays in those kinds of responses. This
limits how *bbox* can form subsets, particularly when the values are in
the form of 'swath data.' For this request type, *bbox* forms a bounding
box for each variable in the list of range requests and then forms the
*union* of those bounding boxes. For swath data, this means that some
extra values will be returned both because the data rarely fit perfectly
in a box for any given domain variable and then the union of those two
(imperfect) subsets usually results in some data that are actually in
neither bounding box. The *bbox* operation (which maps to a Hyrax server
function) was designed to be liberal in applying the subset to as to
include all data points that meet the subset criteria at the cost of
including some that don't. The alternative would be to exclude some
matching data. Similarly, the bounding box for the set of variables is
the union for the same reason. Hyrax contains server functions that can
form both the union and intersection of several bounding boxes returned
by the *bbox* function.

###### *bbox & the csv response*

The *csv* response format is treated differently because the data values
are returned in a table and not arrays. Because of this, the
interpretation of *bbox* is quite different. The subset request syntax
is interpreted as a set of *value filters* that can be expressed as an
series of relational expressions that are combined using a logical AND
operation. Returning to the original example


&bbox="\[49,Latitude,50\]\[167,Longitude,170\]"

a corresponding relational expression for this subset request would be


49 \<= Latitude \<= 50 AND 167 \<= Longitude \<= 170

Because the response is a single table, each variable named in the
request appears as a column. If there are *N* variables listed in *var*,
then *N* columns will appear in the resulting table (with one potential
exception where *N+1* columns may appear). The filter expression built
from the *bbox* subset request will be applied to each row of this
table, and only those rows with values that satisfy it will be included
in the output.

A tabular response like this implies that all of the values of a
particular row are related. For this kind of response (*operation* =
*csv*) to work, each variable listed by use a common set of named
dimensions (i.e., shared dimensions). The one exception to this rule is
when the variables listed with *var* fall into two groups, one of which
has *M* dimensions (e.g., 2) and another group has *N* (e.g., 3) and the
second group's named dimensions contains the first group's as a proper
subset. In this case, the extra dimension(s) of the second group will
appear as additional columns in the response. It sounds confusing, but
in practice it is pretty straightforward. Here's a concrete example.
Suppose a dataset has *Latitude*, *Longitude* and
*Corrected_Optical_Depth* and both Latitude and Longitude are two
dimensional arrays with named dimensions *x* and *y* and
Corrected_Optical_Depth is a three dimensional array with named
dimensions *Solution_3_Land*, *x* and *y*. The *csv* response would
include four columns, one each for Latitude, Longitude and
Corrected_Optical_Depth and a fourth for Solution_3_Land where the value
would be the index number.

### Performance and Implementation

Performance is linear in terms of the number of granules. The response
is streamed as it is built, so even very large responses use only a
little memory on the server. Of course, that won't be the case on the
client...

##### Implementation

The interface described here is built using a Servlet that talks to the
Hyrax BES - a C/C++ Unix daemon that reads and processes data building
the raw DAP2/4 response objects. The Servlet builds the response objects
it returns using the response objects returned by the BES. In the case
of the 'zipped files' response, the BES is told one by one to subset the
granules and return the result as *netcdf3*, et cetera. It streams each
returned file using a *ZipPOutputStream* object from the Apache Commons
set of Java libraries. In the case of the *csv* response the BES is told
to return the filtered data as ASCII and the servlet uses the Java
FilteredOutputStream class to strip away redundant header information
from the second, ..., Nth file/granule.

For each type of request, most of the work of subsetting the values is
performed by the BES, its constraint evaluator and a small set of server
functions. The server functions used for this service are:

roi: subsetting based on indices of shared dimensions
bbox: building bounding boxes described in array index space
bbox_union: building bounding boxes for forming the union or intersection of two or more bounding boxes
tabular: building a DAP Sequence from *N* arrays, where when *N* \> 1, each array must be a member of the same DAP4 'coverage'

It is possible to access the essential functionality of the Aggregation
Service using these functions.

##### Design

The design of the [Aggregation
Service](Use_cases_for_swath_and_time_series_aggregation "wikilink") is
documented as well, although some aspects of that document are old and
incorrect. it may also be useful to look at the source code, which can
be found on GitHub at [olfs](https://github.com/opendap/olfs) and
[bes](https://github.com/opendap/bes) in the
[aggregation](https://github.com/OPENDAP/olfs/tree/master/src/opendap/aggregation)
and [functions](https://github.com/OPENDAP/bes/tree/master/functions)
parts of those repos, respectively.

### Examples

This section lists a number of examples of the aggregation service. We
have only a handful of data on our test server, but these examples
should work. Because the aggregation service is a machine interface, the
examples require that use of curl and text files that contain the POST
requests (except for the version operation).

##### Version

Request: <http://test.opendap.org/dap/aggregation/?&operation=version>

<!-- -->

Returns:

``` xml
Aggregation Interface Version: 1.1
<?xml version="1.0" encoding="UTF-8"?>
<response ns="http://xml.opendap.org/ns/bes/1.0#" reqID="[ajp-bio-…">
  <showVersion>
    <Administrator>support@opendap.org</Administrator>
    <library name="bes">3.16.0</library>
    <module name="dap-server/ascii">4.1.5</module>
    <module name="csv_handler">1.1.2</module>
    <library name="libdap">3.16.0</library>
…
</response>
```

##### Returning an Archive

**NB:** To get these examples, clone <https://github.com/opendap/olfs>,
then cd to *resources/aggregation/tests/demo*.

The example files are also available here:

- [d1_netcdf3_variable_subset.txt](http://docs.opendap.org/index.php/File:D1_netcdf3_variable_subset.txt)
- [d2_netcdf3_bbox_subset.txt](http://docs.opendap.org/images/1/1e/D2_netcdf3_bbox_subset.txt)
- [d3_csv_subset.txt](http://docs.opendap.org/images/4/46/D3_csv_subset.txt)
- [d4_csv_subset_dim.txt](http://docs.opendap.org/images/8/88/D4_csv_subset_dim.txt)

In the OLFS repo on github, you'll see a file named
*resources/aggregation/tests/demo/short_names/d1_netcdf3_variable_subset.txt*.
Here's what it looks like:

``` bash
edamame:demo jimg$ more short_names/d1_netcdf3_variable_subset.txt
&operation=netcdf3
&var=Latitude,Longitude,Optical_Depth_Land_And_Ocean
&file=/data/modis/MOD04_L2.A2015021.0020.051.NRT.hdf
&file=/data/modis/MOD04_L2.A2015021.0025.051.NRT.hdf
&file=/data/modis/MOD04_L2.A2015021.0030.051.NRT.hdf
```

This example shows how the DAP2 projection constraint can be given once
and applied to a number of files. It's also possible to provide a unique
constraint for each file.

Each of the parameters begins with an ampersand (*&*). This command,
which will be sent to the service using POST, specifies the *netcdf3*
response, three files, and the DAP projection constraint
*Latitude,Longitude,Optical_Depth_Land_And_Ocean*. It may be that the
parameter name *&var* is a bit misleading since you can actually provide
array subsetting there as well (but not the filtering-type DAP2/DAP4
constraints).

To send this command to the service, use *curl* like this:

``` bash
edamame:demo jimg$ curl -X POST -d @short_names/d1_netcdf3_variable_subset.txt http://test.opendap.org/opendap/aggregation > d1.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                                     Dload  Upload   Total   Spent    Left  Speed
100  552k    0  552k  100   226   305k    124  0:00:01  0:00:01 --:--:--  305k
```

The output of *curl* is redirected to a file (*d1.zip*) and we can list
its contents

`verifying that it contains the files we expect.`

``` bash
edamame:demo jimg$ unzip -t d1.zip
Archive:  d1.zip
    testing: MOD04_L2.A2015021.0020.051.NRT.hdf.nc   OK
    testing: MOD04_L2.A2015021.0025.051.NRT.hdf.nc   OK
    testing: MOD04_L2.A2015021.0030.051.NRT.hdf.nc   OK
No errors detected in compressed data of d1.zip.
```

##### Returning a Table

In this example, a request is made for data from the same three
variables from the same files, but the data are returned in a single
table. This request file is in the same directory as the previous
example.

The command file is close to the same as before, but uses the
*&operation* or *csv* and also adds a *&bbox* command, the latter
provides a way to specify filtering based on latitude/longitude bounding
boxes.

``` bash
edamame:demo jimg$ more short_names/d3_csv_subset.txt
&operation=csv
&var=Latitude,Longitude,Image_Optical_Depth_Land_And_Ocean
&bbox="[49,Latitude,50][167,Longitude,170]"
&file=/data/modis/MOD04_L2.A2015021.0020.051.NRT.hdf
&file=/data/modis/MOD04_L2.A2015021.0025.051.NRT.hdf
&file=/data/modis/MOD04_L2.A2015021.0030.051.NRT.hdf
```

The command is sent using *'curl* as before:

``` bash
edamame:demo jimg$ curl -X POST -d @short_names/d3_csv_subset.txt http://test.opendap.org/opendap/aggregation > d3.csv
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4141    0  3870  100   271   5150    360 --:--:-- --:--:-- --:--:--  5153
```

However, the response is now an ASCII table:

``` bash
edamame:demo jimg$ more d3.csv
Dataset: function_result_MOD04_L2.A2015021.0020.051.NRT.hdf
table.Latitude, table.Longitude, table.Image_Optical_Depth_Land_And_Ocean
49.98, 169.598, -9999
49.9312, 169.82, -9999
49.9878, 169.119, -9999
49.9423, 169.331, -9999
49.8952, 169.548, -9999
49.8464, 169.77, -9999
49.7958, 169.998, -9999
49.9897, 168.659, -9999
49.9471, 168.862, -9999
...
```

### Potential Extensions to the Service

This service was purpose-built for the NASA CMR system, but it could be
extended in several useful ways.

- Support general DAP2 and DAP4 constraint expressions, including
  function calls (functions are used behind the scenes already)
- Increased parallelism.
- Support for the **tar.gz** return type.