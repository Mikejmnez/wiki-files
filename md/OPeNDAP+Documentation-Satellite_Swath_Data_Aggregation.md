[\<-- Back](Use_cases_for_swath_and_time_series_aggregation "wikilink")

**'Point Of Contact:** *James*

## Description

Satellite_Swath_1

## Goal

The user wants to get data from several granules of a many-granule data
set using a single request. The user does not want to iterate over
several response objects/files; all the data should be contained in a
single entity. However, this aggregation operation must work with Swath
data from a satellite, which poses special problems. Furthermore, the
aggregation should require little or no effort by the data provider to
configure.

## Summary

Principal actor: A person who wants to access level 2 satellite swath
data from a number of granules using a single URL. The person may be an
end user who issues a data request or they may be the developer of a
system that will build a URL to return to an end user. In either case,
an end-user dereferences the URL to get data.

Goal: Eliminate users having to use many URLs to get these data.

The data are Satellite Swath data (e.g., MODIS, level2) where each
granule contains a number of dependent variables are matched to lat and
lon arrays, all of which are 2D (this is the general case for a 2D
discrete coverage). The data set is made up of a number of these
granules, organized in a hierarchy of directories by date. Each of the
granules holds one 'pass' of the satellite from one pole to another.
When a user searches for data, they use a spatial and time box to choose
values from the whole data set. Currently the response to that search is
a list of granules, where each contains *some* of the data in the users
query. The complete granules, or subset versions, may be downloaded. In
the either case, the user must access/download a number of files and
then the values combined somehow - the exact mechanism being left up to
the end user, including reading the files that contain the data.

In this use case, the user instead uses a single URL to access all of
the data that match the selection criteria. Those values will be
returned using the CSV format, so there is virtually no file decoding
burden put on the user. The URL will be one that a system developer can
easily build in software as well as one that a person could write by
hand, at least in many cases.

## Actors

A developer building a 'response URL' from a user's data search request
This person must write software that will translate the search request
into the Hyrax server's (to-be developed) aggregation request syntax.

A person making a data request to the Hyrax server
This actor will need to a similar understanding of the new aggregation
mechanism as the 'developer' but will be writing the URLs 'by hand'.

The Hyrax data server
This is an important actor because the server will have to be updated to
realize the new software's benefits.

Level 2 satellite swath data, stored in multiple files (file == granule == 'pass')
This use case is limited to a particular kind of data, although the
resulting software might be useful for Time Series data too.

## Preconditions

The data to be accessed are served using Hyrax
Yup

We may require that the *CF* option of the HDF4 handler be turned on
This will make the data values much easier to use because access will be
as if we're working with a netCDF3 file - no hierarchy to translate, no
weird Sequence of Structure variables that correlate to dependent var
dimensions.

The Hyrax server has been updated to include the aggregation function
Ditto, but this will require that system admins install the code.

The user's software understands the structure of the returned data values
Again, Yup. In this use case that means the data are in returned in a
CSV table, encoded using ASCII characters. Excel can read these kinds of
responses, e.g., but it's kind of an unusual way to package satellite
data.

The data are indexed by a search system
The main use-case - the scenario where a search system would normally
return a list of URLs instead returns a single URL that will call the
aggregation function that will return all of the granules' data in one
shot.

The user invoking the aggregation function understands the organization of the data
This is actually a variant of the main use-case because the in the
variant scenario the user builds the URL that calls the aggregation
function and not a search system

## Triggers

- A user searches for data and the result indicates that the data they
  want are spread over a number of granules.
- A user knows they want data that are (or may be) spread over a number
  of granules in a dataset.

## Basic Flow

A user (the actor that initiates the use case) performs a search using
EDSC and the result set contains two or more granules. The search client
would normally return a list of URLs to the discrete granules that make
up the result set. However, in this use case, the client has been
programed to recognize this situation and will respond by forming a URL
that will run the aggregation server-side operation and request the
aggregated data be returned as a list of CSV data points.

The server's internal software will build the response's table encoded
as a DAP Sequence; the server will transform that into CSV if that is
part of the request URL.

To formulate a request, the client will need to provide three pieces of
information:

- The granules to consider when building the aggregation -
  <font color="red">explicitly enumerated</font>;
- The (dependent) variables within those granules to include in the
  aggregation;
- The space and time bounds (the *independent* variables) that will be
  used to constraint values of the *dependent* variables; and
- <font color="red">Assumption: The independent and dependent variables
  are all present in the granules</font>.~~where to find those
  independent variables (in the granules' variables, attributes or
  filenames - we should probably limit the first iteration to just
  variables if we can, see below).~~

These parameters will be passed to the server using some sort of a
constraint. ~~If the granules to aggregate can be specified using a
regex, then it should be possible to use HTTP's GET verb.~~
<font color="red">Since each granule must be listed separately, POST
will be used to make the request</font>. Because we know that the
aggregation operation will be be working with Level 2 satellite swath
(geospatial and temporal) data, some optimizations can be made. We know
that latitude, longitude and time are encoded in these granules for each
sample point. Thus the cases where time is encoded in an attribute or
the granule name don't need to be addressed. (They might be addressed by
a future version of the code, however.)

Because of variations in time representation, we may adopt ISO8601 as
the only way to specify time.

<font color="red">The web service end point used to access the
aggregation will take a Request Document using HTTP POST. The request
will contain the list of granules in its body, one granule per line. The
remaining parameters will be passed in using the HTTP Query String
(i.e., as keyname-value pairs).

Using this web service will look something like
http://host/server/aggregator?d4_func=table(vars)&d4_ce=constraints&return_as=csv

where *vars* and *sub-expressions* are:

vars
A list of variables as they are named in the granules (e.g.,
Cloud_Mask_QA, Mass_Concentration_Land)

constraints
A list of strings that denote the bounds of values that limit the
request in space and time. (e.g., "table\|-120 \<= Longitude \< 20"
where *Longitude* is an independent variable in the dataset)</font>.

The response from the request will be a CSV table listing the dependent
variables followed by the values of the dependent vars. For example, the
response would typically look like:

    Latitude, Longitude, Scan_Start_Time, Cloud_Mask_QA, Mass_Concentration_Land
    45, -120, 2010/10/01, 14, 127
    .
    .
    .

where each granule would have 203 \* 135 (25,375) rows (one for each
cell along and across the swath) maximum; fewer given space/time
constraints.

The CSV tabular response is received as a text/plain type HTTP/HTML
response by the client software.

<font color="red">

#### Aside: The granule list

Because the search tool is used to build the list of granules and it
performs 'point-in-box' (or point-in-polygon, which subsumes PIB)
selection of granules, this web service will assume that every granule
in the request document contains *some* data that should be included in
the response</font>.

#### Aside: data organization

NB: This is discussed in more detail below in the Notes section.

The Independent and dependent vars are all (with one important caveat)
two-dimensional arrays. The mapping between a Lat/lon/time tuple and a
dependent variable's value is made by using the same (i,j) indices to
access both the independent and dependent variables.

For example, a granule might contain these arrays: independent vars:
Lat\[5\]\[7\]; Lon\[5\]\[7\]; time\[5\]\[7\] dependent vars:
SST\[5\]\[7\]; Wind_Speed\[5\]\[7\]

To get the wind speed at a given time, lat and lon, find those values in
the independent vars, note the indices and then access the Wind_Speed
array at those index values.

The **caveat** is that most of the dependent vars in a L2 MODIS granule
have three dimensions where the additional dim is some other independent
variable. There are two ways we can accommodate that, but both require
some moderately detailed knowledge on the part of the user:

- We can accommodate dependent vars with an extra dimension like
  \[MODIS_band\]\[\]\[\] by using something like "MODIS_Band == 440"
- We can allow a dependent var to be specified using a partial dimension
  list and use that as a subset. For example, given
  *Mean_Reflectance_Land\[MODIS_Band_Land = 7\]\[Cell_Along_Swath =
  203\]\[Cell_Across_Swath = 135\]* where *Cell_Along_Swath* and
  *Cell_Across_Swath* match the indices of Latitude, Longitude and
  ...time..., the dependent variable can be given using
  *Mean_Reflectance_Land\[0\]* or in general
  *Mean_Reflectance_Land\[**projection expression**\]*

To make this work, the value(s) associated with the 'extra dimension'
will be turned into columns in the response table.

##### Regarding time

Level 2 data contain samples at various lat/lon points made over time.
Each granule has a start time and end time, as does any
temporally-contiguous set of granules. If we think about a request for
all data that fall within two points on a time line, then the set of
potential files with data can be thought of as having three kinds of
elements: The file that contains the starting time, along with zero or
more previous times; the file that contains the ending time, along with
zero or more later times; and the 'interior' files where all of the data
are within the selection bounds.

## Alternate Flow

There are a number of alternate flows involving errors, all of which
involve invalid parameters or granules that fail in some way.

A significant alternate flow is that a user can build the URL that makes
the request themselves. Nothing about the request or the response
changes, however. The significant difference is that a computer program
does not have to figure out how to make the URL. In the main flow of
this use-case, it does.

## Post Conditions

The client will have the data values, in CSV-encoded tabular form.

## Activity Diagram

\[skipped\]

## Notes

Where to find sample data for this use case: MODAPS (MODIS):

- FTP server is here: <ftp://nrt1.modaps.eosdis.nasa.gov/allData/1/>
- Log in with your URS credentials (create an account here:
  <https://urs.earthdata.nasa.gov>)
- Folders ending in _L2 contain level 2 data.

### Information about the data

**NB:** This is how the files look when the CF option is turned on for
the HDF4 handler. They are more messy without it.

There are 83 variables in the HDF4 files. I downloaded three files, all
were identical in the variables they held - same names and types. I
determined this by looking at the DDS responses for the variables.

In the files, there are three independent variables that are the same
'shape':

- Float64 Scan_Start_Time\[Cell_Along_Swath = 203\]\[Cell_Across_Swath =
  135\]
- Float32 Longitude\[Cell_Along_Swath = 203\]\[Cell_Across_Swath = 135\]
- Float32 Latitude\[Cell_Along_Swath = 203\]\[Cell_Across_Swath = 135\]

Of the 83 variables, 71 (including the three above) have the dimensions
*\[Cell_Along_Swath = 203\]\[Cell_Across_Swath = 135\]*.

Of those 71, 27 (including Lat, ...) have only the dimensions
*\[Cell_Along_Swath = 203\]\[Cell_Across_Swath = 135\]*. They are:

        Int16 Aerosol_Type_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Angstrom_Exponent_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Cloud_Fraction_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Cloud_Fraction_Ocean[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int32 Cloud_Mask_QA[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Corrected_Optical_Depth_Land_wav2p1[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Deep_Blue_Aerosol_Optical_Depth_550_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Deep_Blue_Aerosol_Optical_Depth_550_Land_STD[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Deep_Blue_Angstrom_Exponent_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Fitting_Error_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Image_Optical_Depth_Land_And_Ocean[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Float32 Latitude[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Float32 Longitude[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Float32 Mass_Concentration_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Number_Pixels_Used_Ocean[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Optical_Depth_Land_And_Ocean[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Optical_Depth_Ratio_Small_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Optical_Depth_Ratio_Small_Land_And_Ocean[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int32 Quality_Assurance_Crit_Ref_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135][QA_Byte_Land = 5];
        Int32 Quality_Assurance_Land[Cell_Along_Swath = 203][Cell_Across_Swath = 135][QA_Byte_Land = 5];
        Int32 Quality_Assurance_Ocean[Cell_Along_Swath = 203][Cell_Across_Swath = 135][QA_Byte_Ocean = 5];
        Float64 Scan_Start_Time[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Scattering_Angle[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Sensor_Azimuth[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Sensor_Zenith[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Solar_Azimuth[Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Solar_Zenith[Cell_Along_Swath = 203][Cell_Across_Swath = 135];

The other ones, which have other dimensions, are (sorted and grouped by
the additional dimension, which always comes first):

        Int16 Mean_Reflectance_Land[MODIS_Band_Land = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 STD_Reflectance_Land[MODIS_Band_Land = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Asymmetry_Factor_Average_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Asymmetry_Factor_Best_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Backscattering_Ratio_Average_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Backscattering_Ratio_Best_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Effective_Optical_Depth_Average_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Effective_Optical_Depth_Best_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Mean_Reflectance_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Optical_Depth_Large_Average_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Optical_Depth_Large_Best_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Optical_Depth_Small_Average_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Optical_Depth_Small_Best_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 STD_Reflectance_Ocean[MODIS_Band_Ocean = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];

        Int16 Aerosol_Cldmask_Byproducts_Land[Num_By_Products = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Aerosol_Cldmask_Byproducts_Ocean[Num_By_Products = 7][Cell_Along_Swath = 203][Cell_Across_Swath = 135];

        Int16 Deep_Blue_Aerosol_Optical_Depth_Land[Num_DeepBlue_Wavelengths = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Deep_Blue_Aerosol_Optical_Depth_Land_STD[Num_DeepBlue_Wavelengths = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Deep_Blue_Mean_Reflectance_Land[Num_DeepBlue_Wavelengths = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Deep_Blue_Number_Pixels_Used_Land[Num_DeepBlue_Wavelengths = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Deep_Blue_Single_Scattering_Albedo_Land[Num_DeepBlue_Wavelengths = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Deep_Blue_Surface_Reflectance_Land[Num_DeepBlue_Wavelengths = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];

        Int16 Critical_Reflectance_Land[Solution_1_Land = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Error_Critical_Reflectance_Land[Solution_1_Land = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Error_Path_Radiance_Land[Solution_1_Land = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Number_Pixels_Used_Land[Solution_1_Land = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Path_Radiance_Land[Solution_1_Land = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 QualityWeight_Critical_Reflectance_Land[Solution_1_Land = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 QualityWeight_Path_Radiance_Land[Solution_1_Land = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];

        Int16 Surface_Reflectance_Land[Solution_2_Land = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];

        Int16 Corrected_Optical_Depth_Land[Solution_3_Land = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Mean_Reflectance_Land_All[Solution_3_Land = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Standard_Deviation_Reflectance_Land_All[Solution_3_Land = 3][Cell_Along_Swath = 203][Cell_Across_Swath = 135];

        Int16 Optical_Depth_Small_Land[Solution_4_Land = 4][Cell_Along_Swath = 203][Cell_Across_Swath = 135];

        Int16 Optical_Depth_by_models_ocean[Solution_Index = 9][Cell_Along_Swath = 203][Cell_Across_Swath = 135];

        Float32 Cloud_Condensation_Nuclei_Ocean[Solution_Ocean = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Float32 Mass_Concentration_Ocean[Solution_Ocean = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Angstrom_Exponent_1_Ocean[Solution_Ocean = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Angstrom_Exponent_2_Ocean[Solution_Ocean = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Effective_Radius_Ocean[Solution_Ocean = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Least_Squares_Error_Ocean[Solution_Ocean = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Optical_Depth_Ratio_Small_Ocean_0_55micron[Solution_Ocean = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Solution_Index_Ocean_Large[Solution_Ocean = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];
        Int16 Solution_Index_Ocean_Small[Solution_Ocean = 2][Cell_Along_Swath = 203][Cell_Across_Swath = 135];

Here are those 'additional dimensions' along with their sizes:

- MODIS_Band_Land = 7
- Num_By_Products = 7
- Num_DeepBlue_Wavelengths = 3
- Solution_1_Land = 2
- Solution_2_Land = 3
- Solution_3_Land = 3
- Solution_4_Land = 4
- Solution_Index = 9
- Solution_Ocean = 2

Here are the values of those dimensions - these are like DAP2 Grid Maps:

    Dataset: MOD04_L2.A2015021.0020.051.NRT.hdf
    MODIS_Band_Land, 470, 555, 659, 865, 1240, 1640, 2130
    MODIS_Band_Ocean, 470, 555, 659, 865, 1240, 1640, 2130
    Solution_1_Land, 470, 659
    Solution_2_Land, 470, 555, 659
    Solution_3_Land, 470, 659, 2130
    Solution_Ocean, 1, 2
    Solution_Index, 1, 2, 3, 4, 5, 6, 7, 8, 9
    Num_DeepBlue_Wavelengths, 0, 0, 0

### Technical approaches to the problem

**NB:** Unresolved issues in bold below.

There are two general technical solutions to this problem, both
requiring iteration over a number of granules. We can use the BES's
[*define*](BES_XML_Commands#define "wikilink") command to do this or the
capability can be built into the aggregating code.

For a basic request, for each granule:

- Read the Scan_Start_Time, Longitude and Latitude values (3 time 25,375
  values; on array of doubles and two of floats; 25375 \* 8 + 2(25375
  \* 4) = 406,000 bytes)
- Read one of more of the arrays of values. Again 25,375 elements,
  mostly Int16 arrays. It's probably not worth subsetting the read. The
  memory required for several dependent variables is going to be small
  (~ 0.5 MB for 4 values; so four dependent vars and the time/lat/lon
  values can all fit in ~ 1MB).
- For an aggregation that spans N granules in time, the result will
  include all of the time of N-2 (When N\>2) of the granules. The
  aggregator only needs to examine the time values for the 'end points'.
- **Finding the endpoints in the collection of files will be important.
  How do we generalize this? Time information is nominally in the file
  names, but...**
- Since most of the dependent vars have an extra dimension that is used
  to select a band (wavelength) or other facet, that will have to be
  accommoated. Two ideas are presented int he [*Basic
  Flow*](#Basic_Flow "wikilink") section.
- If the client requests several dependent vars they have to agree in
  all their dimensions, both number and 'kind'. This requirement keeps
  the result limited to values that can be represented in a single
  table.

#### Using the BES define command

The BES [*define*](BES_XML_Commands#define "wikilink") command can
aggregate a set of requests into a DAP2 Structure. We could
modify/extend this to include Sequences, or process a Structure or Array
of Sequences transforming that into a single Sequence. The challenges
with using the BES aggregation capability is that it is not obvious how
that would be used to build the DAP metadata responses. I think the
*return_as* capability of the BES could be used to transform the data in
CSV encoding.

#### Using a server function

The aggregation operation could be implemented using a server function,
but that function would either need to make use of the BES's dispatch
and iteration capabilities, something that the BES design never
anticipated, or provide its own, something this is potentially quite
complex. Below is a rough specification for such a function, without
addressing the issues of dispatch and iteration over the granules. The
design captures some useful information even if it is not actually used.

##### The server function interface

The aggregation server function needs to know what granules to
aggregation on, the variables that are to be returned (nominally the
returned variables are a subset of all the variables in the granules)
and the space-time constraints that data must satisfy. The return format
(DAP2 or DAP4 binary; CSV; or NetCDF file is determined using the
request extension of the data access URL.

###### Specification of the granules

The specification of granules will use a regular expression. This will
provide a way for callers of the function to limit the granules using
various information encoded in the filenames as well as specifying all
of the files in (or under) a given location in the server's file system.
For example, a user might want only ascending passes or only passes made
during daylight. Often L2 data files encode this kind of information.

One issue with this is that there's no standard way to make the more
fine-grained distinctions (e.g., passes that are on the ascending part
of the satellite's orbit), so how a user or search client would know
apply this algorithmically is hard to say.

###### Variables to include in the response

The caller will list a number of names. The function will assume that
every granule that matches the regex will contain all of the variables.
Each variable is assumed to hold 'dependent values'. For any given
granule (maybe all granules?) the listed variables may not have any
values included in the response because no values may have sampled with
the space-time constraint.

###### Space and Time constraints

Two pieces of information will be provided to specify the space time
constraint. The list of variables that contain the latitude, longitude
and time values will be given along with the constraints on their
values. To make it easier to unambiguously associated the variable with
the constraint, the limitations will be made using 'mini expressions' of
the form *value relop var* or *value relop var relop value*. If one
*var* appears in more than one of these expressions the result will be
the intersection of the values specified by the expressions.

The parameter specification is designed to be flexible enough to specify
the constraints without having to configure the function for each
dataset. The downside is that it will not take into account the
specifics of latitude, longitude or time. For example, geospatial
subsetting often takes into account that longitude values 'wrap' at
either the dateline or prime meridian. The scheme used here will not do
that, which means it can be applied to *any* independent variables'
values. For these data (level 2) that will not be a problem because the
values are returned in a table.

##### Response Structure

The response will be in the form of a table of values. The table will
have columns that list the independent variables first and then the
dependent variables. Only rows with all values will be included.

## Resources

|                           |                                |                                                                                                   |                                       |               |
|---------------------------|--------------------------------|---------------------------------------------------------------------------------------------------|---------------------------------------|---------------|
| Resource                  | Owner                          | Description                                                                                       | Availability                          | Source System |
| Hyrax server              | Data center (e.g., NSIDC, JPL) | Data server configured both for the file type and with the new extensions that enable aggregation | This should be available all the time | ?             |
| Web server/servlet engine | Data center                    | The data center must run the supporting web infrastructure                                        | All the time                          | ?             |
| Data provider             | Data center                    | A person who understands the data and can answer questions about its contents                     | Business hours                        | ?             |