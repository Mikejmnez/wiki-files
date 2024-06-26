# File Servers

The DODS and OPenDAP projects have used the OPeNDAP FreeForm ND Data
Handler to present a catalog of data files to the world as a single
dataset. In many ways this was a very successful system, providing
catalogs for multi-granule datasets that could be searched by date and
time. However, the OPeNDAP project has decided (winter 2006) to adopt
the THREDDS xml-based catalog system developed at Unidata, Inc. The
remainder of this chapter describes the 'file servers' that can be built
using the FreeForm data handler. Even though we feel it's best to adopt
the THREDDS catalogs, there are good reasons to keep existing catalog
servers running and to build new catalogs as a stop-gap measure to
support existing client software.

Normally, in the OPeNDAP argot, a "dataset" is contained in a single
file on a disk. However, this paradigm is often broken by large datasets
that may contain many thousands or tens of thousands of data files. The
OPeNDAP file server is a way to make these dicrete datasets appear to be
a single large dataset.

The OPeNDAP file server is an OPeNDAP server that returns a URL or set
of URLs in response to a query containing selection variables. For
example, a dataset organized by date and geographic location might
provide a file server that allowed you to query the dataset with a range
of dates and longitudes. This fileserver would return a list of one or
more URLs corresponding to files within that dataset that fell within
the given range.

## The Problem

Consider the following (imaginary) list of files:

    1997360.nc  1998001.nc  1998007.nc  1998013.nc ...
    1997361.nc  1998002.nc  1998008.nc  1998014.nc
    1997362.nc  1998003.nc  1998009.nc  1998015.nc
    1997363.nc  1998004.nc  1998010.nc  1998016.nc
    1997364.nc  1998005.nc  1998011.nc  1998017.nc
    1997365.nc  1998006.nc  1998012.nc  1998018.nc

These appear to be a set of netCDF files, arranged by
date[(3)](Wiki_Testing/footnotes "wikilink")

If you want data from the first week of January, 1998, it is fairly
clear which files to request. However, the OPeNDAP server provides no
way to request data from more than one file, so your request would have
to be split into 7 different requests, from
<font color='green'>1998001.nc</font> to
<font color='green'>1998007.nc</font>. This could be represented as a
set of seven DODS URLs:

    http://opendap/dap/data/1998001.nc
    http://opendap/dap/data/1998002.nc
    http://opendap/dap/data/1998003.nc
    http://opendap/dap/data/1998004.nc
    http://opendap/dap/data/1998005.nc
    http://opendap/dap/data/1998006.nc
    http://opendap/dap/data/1998007.nc

But what if you then uncover another similar dataset whose data you want
to compare to the first? Or what if you want to expand the inquiry to
cover the entire year? Keeping track of this many URLs will quickly
become burdensome.

What's more, another similar dataset could be arranged in two different
directories, <font color='green'>1997</font> and
<font color='green'>1998</font>, each with files:

    001.nc
    002.nc
    003.nc
    ...

and so on. Now you have to keep track of two large sets of URLs, in two
different forms. But you could also imagine files called:

    0011998.nc
    0021998.nc
    0031998.nc

or

    00198.nc
    00298.nc
    00398.nc

or

    1Jan98.nc
    2Jan98.nc
    3Jan98.nc

That is, the number of possible sensible arrangements may not, in fact,
be infinite, but it may seem that way to a scientist who is simply
trying to find data.

## The OPeNDAP File Server Solution

To create a system that allows data providers to assert a degree of
uniformity over wildly variable dataset organizations, OPeNDAP provides
for the installation of an OPeNDAP \new{file server}. The file server is
a server that provides access to a special dataset, containing
associations between the names of files within a dataset and some
"selectable" data values.

### Selectable Data

The concept of \new{selectable data} requires some explanation. This is
used to indicate the data variables you might ordinarily use to narrow
your search for data in the first pass at a dataset.

For geophysical data, the selectable data is often the time and location
of the data, since typical searches for data often begin by specifying a
part of the globe that bears examining, or a date of some event. For
other types of data, other data variables will seem more appropriate.
Model data, for example, which has no real location or time, might be
arranged by the parameters that varied between runs.

A comprehensive definition of selectable data has so far eluded the
OPeNDAP group, but there are some guidelines, albeit fairly vague ones:

- The selectable data is generally *not* recorded within each data file.
  However, the selectable data may often include a *range* summarizing
  some of the data within each file.
- The selectable data should help a user decide whether a particular
  data file in a dataset is useful. A temperature range might not be as
  useful as a time range, since data searches more often start with
  time. (Both would presumably be still more useful,but there is a
  trade-off between the utility of the file server and

the time spent maintaining it.)

### What It Looks Like

Consider again the set of data files shown in ([<cite>
fs,problem</cite>](http://www)). We could associate each one of these
files with a date, and this would provide the rudiments of a file server
if we then serve that data with an OPeNDAP server such as the OPeNDAP
FreeForm ND Data Handler .

    1997/360 http://opendap/dap/data/1997360.nc
    1997/361 http://opendap/dap/data/1997361.nc
    1997/362 http://opendap/dap/data/1997362.nc
    1997/363 http://opendap/dap/data/1997363.nc
    1997/364 http://opendap/dap/data/1997364.nc
    1997/365 http://opendap/dap/data/1997365.nc
    1998/001 http://opendap/dap/data/1998001.nc
    1998/002 http://opendap/dap/data/1998002.nc
    1998/003 http://opendap/dap/data/1998003.nc
    1998/004 http://opendap/dap/data/1998004.nc
    1998/005 http://opendap/dap/data/1998005.nc
    1998/006 http://opendap/dap/data/1998006.nc
    1998/007 http://opendap/dap/data/1998007.nc
    1998/008 http://opendap/dap/data/1998008.nc
    1998/009 http://opendap/dap/data/1998009.nc
    1998/010 http://opendap/dap/data/1998010.nc

This list represents a set of DAP URLs, each identified by a date, given
as a year and a serial day. The files appear to be netCDF format files,
served by an OPeNDAP netCDF server, but that is not important for this
discussion.

To use the OPeNDAP FreeForm ND Data Handler for your file server, you
could use a format description file with an input section like this:

    ASCII_input_data "File Server Example Input"
    year 1 4 short 0
    serial_day 6 8 short 0
    DODS_Url 10 46 char 0