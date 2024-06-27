There are three main scenarios for aggregation that we have encountered
so far in addition to the simple situation where a group of otherwise
discrete variables are combined in a Structure to be manipulated as a
single variable. Those cases are tiling, combination of parameters held
in separate files (Union) and grouping M N-dimensional variables into a
single N+1-dimensional variable (JoinNew). A fourth form of aggregation
which has emerged is 'tiling in time.' That is, it is essentially tiling
but it is useful to separate tiling in the abstract sense from two
common cases: tiling over latitude and longitude and tiling over time.
Both latitude and longitude are periodic, so tiling needs to take this
into account. Time, on the other hand, is generally not periodic
(although climatologies could be stored in separate files and tiled
along their time dimension).

In all cases several discrete data sets (e.g., files) are combined into
a single data set; The discrete data sets are still capable of being
referenced using their DAP URLs, but the result of the aggregation is a
new DAP URL which references the aggregate.

Building such a data set makes it possible to effectively query for
certain data sets using the DAP constraint expression, making the
query-selection indistinguishable from a data access operation. In most
cases, data access replaces a two step operation where first specific
data sets are chosen and then each is individually sampled and the
results combined within a client. Thus, aggregation really solves two
different problems. First, choosing from among many discrete items when
those are more appropriately viewed as a logical whole. It addresses
this by mapping the data set selection problem into a data access
problem. Secondly, when a client has to work with many different data
sets that are made up of many files/URLs, the increased client
complexity needed to manage the differing ways of organizing the data
must be managed somehow. Typically a client builds its own model of a
'logical data set' and fits all of the large, multi-file/multi-URL sites
into it. With aggregation the model for a logical data set becomes the
DAP data model and by providing a relatively small number of ways to
aggregate files/URLs, the number of different models is kept to a
minimum, simplifying the job of a client by relieving it of the task of
developing and implementing the model(s).

## Overview

There are at least three, and perhaps four (or more), aggregations we
will need to implement, listed in expected order of implementation:

- Union
- JoinNew
- JoinExisting
- ForecastModelRunCollection (FMRC)

We give a few more notes and basic overview on each in turn.

### Combining parameters (NcML's Union)

In this case each parameter (e.g., salinity) is stored in a separate
file. That is, each file is essentially a column in a table and the
aggregation operation brings them together so that they can be accessed
with their relations made explicit.

It effectively is considering the containing parent dataset (DDS for the
containing netcdf node) to be an unnamed Structure, with all the
variables listed in the aggregation files added to it at the top level .
The netcdf elements are processed in order, so that if a variable or
attribute is found again in a later element, it is ignored, with the
first named entity being the only one stored.

Also note that for the other join's, the union is the default for data
**not specified as a join dimension**. So we'll have to make union be
the default thing to do on any aggregation, with special processing for
joinExisting and joinNew added on afterwards.

### Increasing dimensionality (NcML's JoinNew)

The most common example of this is the combination of a large set of
satellite images, all of which are regular with respect to one another
in latitude and longitude, which span some time range. The image are not
generally equally spaced in time, however. The result is a N+1
dimensional grid variable with a new Grid Map that must be 'synthesized'
using date/time information from some source (an attribute stored in the
image file or the file name itself).

This should be implemented second since it is suspected to be very
useful in practice and will suffer less from speed concerns than
joinExisting since the cardinality of the new dimension is simply the
number of netcdf nodes in the aggregation (or alternatively the number
of files matched by a scan directive).

### Tiling (NcML's JoinExisting)

There are two common cases of tiling: Tiling images over lat and long
extents and tiling periodic measurements taken over time. In each case
the data are broken down into parts (tiles) for storage and/or access
efficiency reasons (although this is less and less a compelling reason
given that storage formats like HDF4/5 and NetCDF 4 support internal
compression and tiling and updates). In the case of measurements taken
over time, it might be that a months data are stored in a single file
but that the entire data set spans several years.

#### Implementation Issues

Speed is known to be an issue with this aggregation since the size of
the resulting joined dimension depends on the cardinalities of this
variable in all the specified files, which need to be queried to figure
out their size. This is especially important for the scan directive,
where the **'ncoords** hint attribute cannot be used to specify the
dimension sizes in the NcML itself.

### Forecast Model Run Collection (FMRC)

This is simply an extension to joinNew which does some extra processing
for the particular structure required by the FMRC. In particular, it
assumes time is 2D, having a **runtime** (the initial condition time for
the forecast) as well as a **forecast time** since the forecast at a
specific time depends on when the model's initial conditions (runtime)
were chosen. Please see
<https://www.unidata.ucar.edu/software/thredds/current/netcdf-java/ncml/FmrcAggregation.html>
for more information on this model. The required structure of a 2D time
variable as specifying the final aggregation is the main difference
making FMRC a special case.

A few notes on the examples in the URL:

- Example 1 in the above URL shows that a
  **forecastModelRunCollection**' aggregation for the basic case is very
  similar to a JoinNew for files with the separate runtimes, but with
  the added constraint that the time variable needs to be bumped up a
  dimension as well to handle the runtime.

<!-- -->

- Example 2 uses the JoinExisting aggregation in a nested fashion to
  prepare more finely split datafiles into the form expected by example
  1, implying that we need to implement JoinExisting as a prerequisite
  for this case.

<!-- -->

- Example 3 specifies a new aggregation type called
  **forecastModelRunSingleCollection** and a new scan element called
  **scanFrmc** which are used for the case of forecast data being split
  into one timestep per file with the runtime and forecast time
  coordinates being able to be inferred from the filenames. This case
  will involve implementing these special cases.

## Use Cases

1.  Installing the NcML Handler: This is the same as the [matching use
    case](Add_the_NcML_handler_to_the_BES "wikilink") in the [AIS
    project](AIS_Using_NcML "wikilink")
2.  [Specifying an aggregation using
    NcML](Specifying_an_aggregation_using_NcML "wikilink")
3.  [Making an aggregation visible using the directory browsing
    features](Making_an_aggregation_visible_using_the_directory_browsing_features "wikilink")
    See also: [Using the NcML Handler to get
    information](Using_the_NcML_Handler_to_get_information "wikilink")
4.  [Aggregating several URLs where attributes' values
    change](Aggregating_several_URLs_where_attributes'_values_change "wikilink")

## Definitions

NcML
A markup language used by Unidata's TDS to describe aggregations and to
provide features similar to our AIS software.

<!-- -->

Aggregation
The combination of the variables in two or more DAP URLs to a new
variable/s that can be accessed using a single DAP URL. This definition
includes the three types of aggregation described above plus the
'Structure' aggregation already built into the BES but which can be
accessed only for netCDF files.

## Background

Aggregation has long been known to be an important feature
remote/distributed access systems.

Here are links that describe NcML 2.2:

- [NcML 2.2
  Tutorial](https://www.unidata.ucar.edu/software/thredds/current/netcdf-java/ncml/Tutorial.html)
  This starts out with a page on NcML and then goes onto a page about
  aggregation. It's from the latter that we can see the parallels with
  our stuff here.
- [Annotated Schema for
  NcML-2.2](https://www.unidata.ucar.edu/software/thredds/current/netcdf-java/ncml/AnnotatedSchema4.html)

Notes:

1.  NcML 2.2 is based on the CDM and thus includes Groups. We will want
    to elide that feature until DAP4 is done and well supported.
2.  We will probably want to use NcML as a replacement for the AIS code
    now in libdap.
3.  We need to decide how to treat dimensions - which are a type in the
    CDM. Are they Grid Maps? I think so and we should have Grid Maps be
    the only thing we allow to be called a dimension, even though this
    might initially limit the aggregations we can do. DAP 3.3 or 3.4
    will introduce the idea of a dimension type.

## Design

### Design Principles

**Encapsulation**: A key principle will be to encapsulate the
aggregation engine code from the NCMLParser itself. In other words, keep
the code that does the aggregation work on libdap objects in a separate
class or even its own self-contained (with a dependency only down to
libdap and not up to the ncml_module). This will allow for the
possibility of reuse of the aggregation code in other locations. Note
that this will include the code for the scan element --- the parser
should have a use dependency for it, but it should not have a back
dependency to the parser. In this way the new aggregation code could
potentially be its own shared library or be rolled into libdap and will
not be tied to the NCML Module itself.

### Order of Implementation

The implementation of aggregation is expected to proceed as follows:

- Implementation of "union" aggregation on explicitly listed files (i.e.
  explicit <netcdf> nesting)
- Implementation of "joinNew" aggregation on explicitly listed files
- Implementation of "joinExisting" aggregation on explicitly listed
  files
- Implementation of the <scan> element for directory scanning
  - Testing of scan for union
  - Testing of scan for joinNew
  - Testing of scan for joinExisting
- Optimization Evaluation to decide if needed

TBD: Does FMRC fit into the scope and if so at what point is it added?

### Design Docs

- [Union Aggregation -- Explicit
  Netcdf](Union_Aggregation_--_Explicit_Netcdf "wikilink")

## General Design Issues, Constraints and Thoughts

Design Issues:

- DOM vs. SAX
- Remote Data
- Speed
- Scan and Caching

### DOM vs SAX

One issue to be aware of is that the NcML docs specify that the
<aggregation> element, if it exists within a <netcdf>, is processed
**first**, then the other elements. Unfotunately, this context-dependent
grammar means that our SAX parser won't work unless we restrict the NcML
to list the aggregation element first so it is processed first by the
SAX parser. I don't think this is a large restriction (forcing the
aggregation element to be listed first). We can actually relax this
constraint somewhat by saying that only elements which MODIFY DATA IN
THE AGGREGATION must come after the aggregation element. Attributes and
variables which are not shadowed in the aggregation anywhere may be
specified first.

Either way, we **cannot** throw out elements as we close them as in a
straight-up SAX parser since we need them to do the actual aggregation.
In particular, all of the <netcdf> nodes within an <aggregation> will
need to be maintained in an ordered container within the aggregation
element until the aggregation itself is closed. At this point, the
aggregation processing can occur and all the <netcdf> nodes finally
released. So the parser will need to maintain some small portions of the
parser state in memory to handle aggregation. This implies a few changes
in the memory management of the current SAX dispatcher implementation,
which assumes it can delete elements once they are closed.

### Remote Data

Remote data brings with it a bunch of design issues of which we need to
be aware. For this reason, the initial aggregation implementation will
**not** handle remote data, but only locally served files. For
completeness, we will document the expected issues with remote data
here:

#### Security

First, we are planning to make remote *netcdf@location* capability be a
compile-time and/or run-time (i.e. bes.conf entry) option to allow
servers with security models that allow only local requests to still use
the local file support without compromising security while allowing
sites that require it to have access to it.

#### Speed Implications

Second, remote files will also have a **huge** impact on speed since a
request must be made for each location in the aggregation. If these
requests are made serially then an aggregation could take an extremely
long time to compute. We will need to add concurrency support to the BES
and/or NcML Module to allow for parallel requests via a thread pool or
some similar concurrency pattern. This concurrency will have to be
carefully demarcated to avoid the contagious concurrency problem where
semaphores start leaking out all over the codebase.

#### Load Request Thread Pool in the BES

My current thought is that the NcML Module itself will send off a bunch
of requests to the BES which will use a singleton request pool which
internally will spawn threads to load the requested objects (probably
max threads \<= number of requests at any time, where the pool will pull
new requests off a queue as thread resources become available). The
module will pass itself as an abstract interface class which will define
a return callback for the request pool to hand back loaded objects (or
error objects). This return callback will be implemented with a blocking
semaphore lock on a container for the returned data, keeping the
concurrency contained. The module itself can sleepy poll (i.e. loop with
a microsleep until timeout) the container (also via a semaphore wrapped
critical section) until all requests have returned as errors or with
data (perhaps we can call cancel requests on all unreturned requests if
we get any error). Once all requests have returned, the NcML Module can
continue forward with its parsing.

### Speed

One issue that seems to be a thorn in the TDS implementation is speed.

#### Background

From Nathan's notes from Matt Arnott:

> Matthew Arrott: TDS scans aggregation holdings to build aggregated
> components. Can we make scanning activity state based? I.E. only
> rebuild the aggregation content when the underlying storage changes?
> An "active filesystem" can trigger an event that the BES can catch to
> initiate a rebuilding of the aggregation. (What is the signal?)

Also from Roy.Mendelssohn@noaa.gov:

> TDS has a scour time for aggregation. The way this works is that when
> an request to an aggregated data set is made, TDS checks to see f the
> scour time has passed. If not, it takes its information from cache, if
> so, it rescans the files in the aggregation for the aggregate info.
> The problem with this is if there a lot of files being aggregated for
> a dataset that changes frequently, a user waits while the data files
> are re-aggregated, this can be a minute or two. preferable to us would
> be some way to tell the server to reaggregate the dataset when the
> data has changed or been added to (our use case), so that the
> reaggregation will occur when likely a user is not waiting

#### Design Thoughts on Caching

Two thoughts come to mind:

- Author Initiated Caching
- BES Daemon Initiated Caching

Basically, in the first approach, the author of an aggregation file
would use, say, a new BES command in the NcML module to tell it to
"please go process and cache this file now since I changed something".
Here "something" could be the NcML file itself or perhaps the author
added a new file to a directory being scanned for aggregation. The
author knows it happened and can force the cache flush and recompute.
Notice that in the case of a file being added that a modification time
on the NcML file itself fails to signal a cache dirty bit since it
doesn't know a new file arrived (or was removed). This is likely one
reason for the scour time in TDS. This active recaching request forces
the recompute when its needed. Note that an automated system could also
send this command to the BES for a given NcML file if it "knew" that the
file referred to a file it just added (i.e. it adds to a directory being
scanned by the NcML aggregation).

In the second approach, if the NCML Module is added to the BES, it would
somehow also start a "Content Watching Daemon" that would somehow "know"
the complete manifest for a given aggregation file and would
periodically (like scour time above, but decoupled from a user request)
recompute the manifest for each served NcML file. If the manifest
changed, it would initiate the cache dirty operation mentioned above for
that file to recache it. Since the manifest doesn't require knowing
anything except for how to read referenced files, we may be able to
compute it with a stripped down version of the parser in the ncml
module. Basically, it would need to find all referenced datasets in the
top level aggregation and add them to the manifest. It would need to
implement the directory scanning portion of the scan element. It would
need to recurse on referenced datasets in the case that those are ncml
as well and could contain nested aggregations. At the end of this
closure, we would have the fully closed manifest for a given NcML file.
Perhaps we could make this manifest be a new command for the module to
compute for an NcML file. Downstream consumers might be interested in
the actual closed set of raw original datafiles which a given NcML
refers to, so this may be a new Use Case on its own. Also, the manifest
could be used to spawn concurrent requests for all the data in a given
NcML file before any other processing takes place. Then the parser can
just use the cached, loaded versions of all referenced locations.

The nice thing about the second approach over the scour time is that it
is decoupled from the user request and so acts like a "double buffer."
The user always gets the last cached dataset when they request the
aggregation, and the Cache Watcher daemon simply checks every so often
if it needs to recompute the cache and does so. Since swapping in the
new cache will be atomic, the user never has to wait for the response,
with the known caveat that the data may be as old as the Watcher
daemon's recompute timeout. And given approach one above, if the user
REALLY wants the latest data, they can spawn the recache themselves
before asking for the data, with the recache command not returning from
the BES until the data is fully recached so a subsequent request will be
up-to-date as of the recache command and will return ASAP.

### Cache Interactions

This issue was addressed in the Speed section, but I will reiterate it
here. The BES will need to be aware that the modification time on the
NcML file is not a sure-fire way to know if a cache is dirty because of
the possibility of a scan element. The file doesn't change, but simply
adding a file to a directory will invalidate the cache. We will need to
find SOME way to solve this issue. I mention a few thoughts above.

## Deliverables

A BES with the capability to perform the three aggregations discussed
above. We might implement this using a new module which can aggregate
any DAP data source that can be accessed by the BES and we might choose
to code the aggregations using NcML which would make our code a drop in
replacement for the TDS.

## Period of use

This code will be used by the IOOS and REAP projects and will hopefully
have a use in general far beyond that time.

[BES Aggregation using NcML](Category:Development "wikilink") [BES
Aggregation using NcML](Category:Hyrax_Development "wikilink")