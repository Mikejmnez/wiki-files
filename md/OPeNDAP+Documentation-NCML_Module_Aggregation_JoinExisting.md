Join Existing Aggregation

## Overview

A *joinExisting* aggregation joins multiple granule datasets by
concatenating the specified outer dimensional data from the granules
into the output. This results in matrices of the same number of
dimensions, but with larger outer dimension cardinality. The outer
dimension sizes of the granules may vary across granule, but any inner
dimensions for multi-dimensional data still are required to match.

The reader is also directed to a basic tutorial of this NcML aggregation
which may be found at
<http://www.unidata.ucar.edu/software/netcdf/ncml/v2.2/Aggregation.html#joinExisting>.
Note that version 1.1.0 of the module does not support all features of
joinExisting! Future versions will add more features.

**PLEASE NOTE** that our syntax is slightly different than that of the
THREDDS Data Server (TDS), so please refer to this tutorial when using
the Hyrax NcML Module! In particular, we do not process the
<aggregation> element prior to other elements in the dataset, so in some
cases the relative ordering of the <aggregation> and references to
variables within the aggregation matters.

### Content summary

This page describes the behavior of the initial implementation of
joinExisting for version 1.2.x of the NcML Module, bundled with Hyrax
1.8. It is a limited feature set described below. Please see the
Limitations section for more information.

In version 1.2.x, a joinExisting aggregation may be specified in three
ways:

- Using explicit lists of **netcdf** elements with the the *ncoords*
  attribute correctly specified for all of them.
- Leaving off the *ncoords* attribute for all of the *netcdf* elements.
- Using a **scan** element with *ncoords* specified and all matching
  granule datasets having this dimension size

Our example below will clarify this.

Future versions of the module will implement more of the joinExisting
feature set.

## Examples

Here we give an example that illustrates the functionality offered by
the current version of the aggregation. This example may also be found
on:

<http://test.opendap.org:8090/opendap/ioos/mday_joinExist.ncml>

with the data granules located in

<http://test.opendap.org:8090/opendap/coverage/mday/>

### Granules

Assume we have some number of granule datasets with a DDS the same as
the following (modulo the dataset name):

    Dataset {
        Grid {
          Array:
            Float32 PHssta[time = 1][altitude = 1][lat = 4096][lon = 8192];
          Maps:
            Float64 time[time = 1];
            Float64 altitude[altitude = 1];
            Float64 lat[lat = 4096];
            Float64 lon[lon = 8192];
        } PHssta;
    } PH2006001_2006031_ssta.nc;

### Explicit Listing of Granules

We see that here *time* is the outer dimension, which is the only
dimension we may join along (it is an error to specify an inner). Given
some number of granules with this same shape, consider the following
explicit joinExisting aggregation:

``` xml
<?xml version="1.0" encoding="UTF-8"?>

<netcdf title="joinExisting test on netcdf Grid granules">

  <aggregation type="joinExisting" dimName="time" >
    <!-- Note explicit use of ncoords specifying size of "time" -->
    <netcdf location="/coverage/mday/PH2006001_2006031_ssta.nc" ncoords="1"/>
    <netcdf location="/coverage/mday/PH2006032_2006059_ssta.nc" ncoords="1"/>
    <netcdf location="/coverage/mday/PH2006060_2006090_ssta.nc" ncoords="1"/>
  </aggregation>

</netcdf>
```

Here's the same aggregation using the *scan* element instead of
explicitly listing each file:

``` xml
<?xml version="1.0" encoding="UTF-8"?>

<netcdf title="joinExisting test on netcdf Grid granules using scan">

  <aggregation type="joinExisting" dimName="time" >
    <scan location="/coverage/mday/" suffix=".nc"/>
  </aggregation>

</netcdf>
```

First, note that the *ncoords* attribute should be specified on the
individual granules for this version of the module. In many cases the
handler will be more efficient if the *ncoords* attribute is used. Note
that we also specify the *dimName*. Any data array whose outer dimension
is called this will be subject to aggregation in the output.

Serving this from Hyrax will result in the following DDS:

``` c
Dataset {
    Grid {
      Array:
        Float32 PHssta[time = 3][altitude = 1][lat = 4096][lon = 8192];
      Maps:
        Float64 time[time = 3];
        Float64 altitude[altitude = 1];
        Float64 lat[lat = 4096];
        Float64 lon[lon = 8192];
    } PHssta;
    Float64 time[time = 3];
} mday_joinExist.ncml;
```

We see that the time dimension is now of size 3 to match that we joined
three granule datasets together.

Also notice that the map vector for the joined dimension, time, has been
duplicated as a sibling of the dataset. This is done automatically by
the aggregation and it is copied into the actual map of the Grid. This
copy is to facilitate datasets which have multiple Grid's that are to be
joined --- the top-level map vector is used as the canonical template
map which is then copied into the maps for all the aggregated Grids. In
the case of the joined data being of type Array, this vector would
already exist as the coordinate variable for the data matrix. Since this
is the source map for all aggregated Grid's, any attribute (metadata)
changes should be made explicitly on this top-level coordinate variable
so that the metadata is shared among all the aggregated Grid map
vectors.

### Using the Scan Element

The collection of member datasets in a joinExisiting aggregation can be
specified using the NcML *scan* element as described in the [dynamic
aggregation tutorial](Dynamic_Aggregation_Tutorial "wikilink").

#### NcML Dimension Cache

If the scan element is used without the *ncoords* extension (see below),
then the first time a joinExisiting aggregation is accessed (say by
requesting it's DDS) the BES process will open **every** file in the
aggregation and cache its dimension information in the NcML dimension
cache. By default the cache files are written into `/tmp` and the total
size of the cache is limited to a maximum size of 2GB. These settings
can be changed by modifying the `ncml.conf file`, typically located in
`/etc/bes/modules/ncml.conf`:

``` bash
#-----------------------------------------------------------------------#
# NcML Aggregation Dimension Cache Parameters                           #
#-----------------------------------------------------------------------#

# Directory into which the cache files will be stored.
NCML.DimensionCache.directory=/tmp

# Filename prefix to be used for the cache files
NCML.DimensionCache.prefix=ncml_dimension_cache

# This is the size of the cache in megabytes; e.g., 2,000 is a 2GB cache
NCML.DimensionCache.size=2000

# Maximum number of dimension allowed in any particular dataset.
# If not set in this configuration the value defaults to 100.
# NCML.DimensionCache.maxDimensions=100
```

The cache files are small compared to the source dataset files,
typically less than 1kb for a dataset with a few named dimensions.
However the cache files are numerous, one for each file used in a
joinExisiting aggregation. If you have large joinExisiting aggregations
it is important to be sure that the **NCML.DimensionCache.directory**
has space to contain the cache and that the **NCML.DimensionCache.size**
to an appropriately large value.

Because the first access of the aggregation triggers the population of
the NcML dimension cache for that aggregation the time for this first
access can be significant. It may be that typical HTTP clients will
timeout before that requests completes. If a client timeout occurs
dimension cache may not get fully populated, however subsequent requests
will cause the cache population to pick up where it was left off.

With only a modicum of effort one could write a shell program that
utilizes the BES standalone functionality to pre-populate the dimension
caches for large joinExisiting aggregations.

#### ncoords extension

If all of the granules are of uniform dimensional size, we may also use
the syntactic sugar provided by a Hyrax-specific extension to NcML --
adding the *ncoords* attribute to a **scan** element. The behavior of
this extension is to set the *ncoords* for each granule matching the
scan to be this value, as if the datasets were each listed explicitly
with this value of the attribute. Here's an example of using the
syntactic sugar that results in the same exact aggregation as the
previous explicit one:

    <?xml version="1.0" encoding="UTF-8"?>
    <!-- joinExisting test on netcdf granules using scan@ncoords extension-->
    <netcdf title="joinExisting test on netcdf Grid granules using scan@ncoords"
        >

      <attribute name="Description" type="string"
             value=" joinExisting test on netcdf Grid granules using scan@ncoords"/>

      <aggregation type="joinExisting"
               dimName="time" >

        <!-- Filenames have lexicographic and chronological ordering match -->
        <scan location="/coverage/mday"
          subdirs="false"
          suffix=".nc"
          ncoords="1"
          />

      </aggregation>

    </netcdf>

which we see results in the same DDS:

    Dataset {
        Grid {
          Array:
            Float32 PHssta[time = 3][altitude = 1][lat = 4096][lon = 8192];
          Maps:
            Float64 time[time = 3];
            Float64 altitude[altitude = 1];
            Float64 lat[lat = 4096];
            Float64 lon[lon = 8192];
        } PHssta;
        Float64 time[time = 3];
    } mday_joinExist.ncml;

The advantage of this is that the server does not have to inspect all of
the member granules to determine their dimensional size which allows
server to manufacture responses much more quickly.

## Limitations

The current version implements only basic functionality. If there is
extended functionality that is needed for your use, please send
\<[mailto:support@opendap.org](mailto:support@opendap.org)\> to let us
know!

### Join Dimension Sizes Should Be Explicitly Declared

As we have seen, the most important limitation to the JoinExisting
aggregation support is that the *ncoords* attribute should be specified
for efficiency reasons. Future versions will continue to relax this
requirement. The problem is that the size of the output join dimension
is dependent on checking the DDS of *every* granule in the aggregation,
which is computationally expensive for large aggregations.

### Source of Data for Aggregated Coordinate Variable on Join Dimension

This version does not allow the join dimension's data to be declared
explicitly in the NcML as the NcML tutorial page describes. This version
automatically aggregates **all** variables with the outer dimension
matching the *dimName*. This includes the coordinate variable (map
vector in the case of Grid's) for the join dimension. These data cannot
be overridden from those pulled from the files. Currently the TDS lists
about 5 ways this data can be specified in addition to pulling them from
the granules --- we only can pull them from granules now, which seems
the most common use.

### Source of Join Dimension Metadata

The metadata for the coordinate variable is pulled from the *first*
granule dataset. Modification of coordinate variable metadata is not
fully supported yet.

------------------------------------------------------------------------

[Category:Aggregation](Category:Aggregation "wikilink")
[Category:NCML](Category:NCML "wikilink")