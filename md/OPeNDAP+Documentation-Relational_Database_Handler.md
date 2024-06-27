## Summary

To extend the existing metacat-based search system that Kepler uses so
that it can be used to find DAP servers, we will:

1.  Use the [AIS capabilities](AIS_Using_NcML "wikilink") and
    [Aggregation](BES_Aggregation_using_NcML "wikilink") to be developed
    for Hyrax 1.6 and/or already present in TDS to add specific
    information to the Ocean Use Case data sets.
2.  Build a module that can transform the DDX returned by these servers
    into an EML document
3.  Store those EML documents in an instance of Metacat that Kelper can
    serach
4.  Modify Kepler to search over EML that contains a <spatialRaster>
    element.

### Related designs/projects

- [NcML AIS Handler](AIS_Using_NcML "wikilink")
- [NcML Aggregation Handler](BES_Aggregation_using_NcML "wikilink")

See also a page that documents the Current state of the [THREDDS
Crawler](THREDDS_Crawler "wikilink") on which this depends.

## Use Cases

### [Setup the catalog infrastructure](Setup_the_catalog_infrastructure "wikilink")

### [Add information about a data set to the catalog](Add_information_about_a_data_set_to_the_catalog "wikilink")

#### Background information

[Sample EML for CZCS data](Sample_EML_for_CZCS_data "wikilink")

Parts of an EML document that must be built:

- Title
- Optional: Abstract
- Creator
- KeywordSet
- Contact
- For Ocean Use-Case data sets: SpatialRaster

##### Regarding the origin of the SpatialRaster element in EML

The SpatialRaster element in EML has it's origin in the FGDC CSDGM
([Content Standard for Digital Geospatial
Metadata](http://www.fgdc.gov/metadata/csdgm/)). However, it has a close
relative in the ISO 19115 standard and XSLT can be used to transform
between the EML, FGDC/CSDGM and ISO 19115 elements (or 'elements' in the
case of a Content Standard).

The vector metadata information in EML is based on ISO 19115.

### [Search the catalog](Search_the_catalog "wikilink")

*Needs significant work*

## Definitions

## Background

For systems like the ones which use DAP the biggest data location
problems are getting valid metdata that is sufficiently uniform and
making a smooth transition from the initial process of location to the
selection of individual parts of a chosen data set.

### The problem of heterogeneous configuration

The first problem is the really the problem of finding data sets in the
vernacular sense of *finding* and *data set* while the second is the
problem of taking a number of ad hoc heterogeneous storage and
organizational configurations and mapping them into a (mostly) uniform
access mechanism. While both problems present a significant challenge,
the second can be effectively addressed by *aggregating* the discrete
elements of the actual data set's configuration (e.g., 20,000 images in
a file system where different years and months are each in nested
directories) into a single logical entity so that the different discrete
parts are accessed in one operation. An example will make this clearer:
Imagine the collection of 20,000 images stores data in directories named
*1999*, *2000*, ... and within each of those there are sub-directories
named *01*, *02*, ..., *12* and within those there are files named
following the pattern YYYYMMDDD where DDD is the day number. Lets also
assume that this data set is served by Hyrax and thus each of these
files can be accessed by a single URL. If a person wants to read data
from the fourteen files from Jan 25th to Feb 7th, they have to know this
structure and apply it to the data set to figure out which URLs to use
with their client, then get the data from the URLs and probably assemble
it into a three dimensional data structure. If all collections were
organized like this, clients could be built to perform those operations
and the problem would be solved. But the variability among data set
storage patterns is actually very high - a handful of data sets are
stored as described by this example, but most are stored in other ways
and there are enough of those 'other ways', and new 'other ways' keep
emerging every day, to make customizing clients impracticable.

A solution is to aggregate the different URLs into a *single*
three-dimensional data object and provide a data server than can operate
on it without revealing its true composition. In this scenario, the
person who wants data from the Jan 25th to Feb 7th asks for it by
accessing the data set using the uniform data access operations
supported by *every* data server within the system. For 100 different
data sets made up of images taken over time, each can represented as a
single three-dimensional data set and each can be accessed using the
same operators (and thus operations) even though they are actually
stored using 100 different configurations of files and databases. This
is how the process of aggregation can be used to solve the data location
problems that arise from heterogeneous configurations of data sets.

The downside to this approach is that it applies only to collections
that can be aggregated. If a dataset is composed of many files both
those cannot be aggregated using one of the schemes provided by NcML,
then this technique won't solve the problems associated with making the
transition from finding a dataset to selecting elements of its
inventory.

> <em> The THREDD Data Server (TDS) supports both DAP and aggregation
> using NcML and clearly shows that this approach works well in a wide
> variety of cases</em>

### The problem of *finding* data sets

This is the problem of matching a group of data sets to a schema that
uniformly describes their variability. The entries that conform to this
schema (aka records) cover both taxonomies and values for sets of
predefined parameters. The problem here is one of heterogeneity too.
Clients are optimized to search for information specific to a fixed set
of problem areas (e.g., Ocean data from satellites; Biological Ocean
data from fixed locations) and because of this, are built to work with
specific search information. Each data set is described by a record and
the collection of records is stored in a data base that can be searched.
There's nothing particularly hard about this and it works well with one
caveat: The form and context of the records tends to be very specific to
a problem domain. Of course, information about multiple domains can be
included in the same record; it's just as easy for a data base to search
the expanded records as for the focused ones. However, the problem lies
in the making of those records. If the information they contain is very
tightly focused so that only one problem domain is described, they are
easiest to write but useless when searching within other domains. If the
information is universal the records are essentially impossible to
write. The middle ground is always a compromise between breadth of
coverage and manageability. In systems built using DAP servers, there's
never a requirement to add specific metadata to start serving data so
the caliber of information useful for searching is highly variable. We
chose this limitation because it was the best way to get the most data
served. (And because there are many cases where network access to data
is desirable within a group where many metadata parameters are well
known).

The problem of metadata development needs to be separated from the
production of the data themselves because as the data age their
potential audience tends to widen. At first only those most closely
connected to the data are interested in it, but over time interest often
broadens until people who initially know very little about the data
become potential users. As this widening of scope takes place the need
for more general metadata increases. So too does the variety of clients
which might be used to operate on the data. In other words, as the pool
of users widens, so does the kinds of uses and as that happens the
problem domains where the data may be used widens.

The challenge for a good searching system is to accommodate these
changes over time. The system must support building additional metadata
into the network presentation of data over time. To do this effectively,
the system needs to build different types of records using more generic
'building blocks' which can be shared between several different types of
records used by different types of clients.

The AIS- and Aggregation-based system described here is designed to
solve both of these problems.

## Design

![](REAP_Cataloging_Deployment.png "REAP_Cataloging_Deployment.png") The
Kepler workflow client used in the REAP project has the ability to
access data using DAP servers but has no way to find those servers. Data
search systems built for DAP servers don't have a very good track record
because such systems (for the most part) do not address the twin needs
of working with a fluid pool of data servers and data sets and fitting
in with the basic requirement of DAP-based systems - that impact on a
data provider be absolutely minimal.

In order for the impact of hosting a DAP server to be minimal, the
typical data documentation (i.e., *metadata*) required for most
searching systems is not required for data served using DAP. As a
result, interfacing DAP servers to such systems is a daunting task
involving lots of manual metadata entry. This effort is frustrated not
only by the often baroque nature of metadata standards (e.g., FGDC) but
also because the data sources being described *move* from place to place
frequently, something the begs for automated discovery and cataloging -
exactly the opposite of what is provided by hand-written metadata
records.

Complicating this typical scenario is the nature of most searching
systems: They tend to be tailored to a specific client system. Those
data providers remaining who were not deterred by the issues of
complexity and frequent manual updates of metadata often are when they
see that the additional metadata will satisfy the needs of only one
client. It is virtually impossible to get data providers to write these
metadata records, since the providers will have to write
differently-formatted information for each such client. This would
normally be remedied by adopting a standard and then modifying all
clients to use that standard. However, standards with enough breadth to
satisfy the semantic needs of a wide spectrum of clients are, as
previously described, very complex.

Since it's unlikely that a 'magic' standard will appear anytime soon or
that clients will drop many of their requirements for metadata vis-a-vis
searching, we need a solution that will provide a way to build a
*uniform* set of metadata at the servers which can then be assembled by
different clients according to their diverse needs. It might be that
some desired information is missing from some servers or some
superfluous information is present at others, but the clients can build
their choice of metadata records using what is present.

The companion technologies of XML and XSLT combined with a system to
provide *ancillary* information for data sets served using DAP suggest
one solution to this problem.

The system described here will use Kepler as an example client. It will
build metadata records using EML, where most of the really important
metadata is actually a series of XML micro documents made up of
information described by ISO 19115:2003, 19115-2:2009 and other
standards. The system will use a server-side solution (technology
leveraged from other projects and thus more likely to be in use) to
augment data sets with this information. The EML documents will be built
using XSLT - EML won't be directly returned by the DAP servers and the
same geo-spatial information can be used for other things (e.g., WCS
1.x). The EML will be scavenged by Metacat, which Kepler already knows
how to use (with some caveats). Metacat doesn't know how to crawl DAP
servers, but it can be fed URLs and we may employ TPAC's crawler to feed
Metacat with URLs or DDX/EML objects.

This solution is not ideal. Data providers will still initially have to
write metadata records, albeit smaller, more concise ones. However,
automated crawling of servers and automated harvesting of the discovered
URLs means that data set and server movement can be accommodated more
effectively than with designs based on static documents.

A problem with this design is that data providers will need to know the
collection of 'micro documents' needed to support one or more different
client systems. We could mitigate this risk by surveying clients to find
out how diverse their needs really are - not in terms of formats but in
terms of content. We know from current experience with XSLT and related
technologies that we can transform information stored in XML fairly
easily, so supporting different textual formats is not nearly as much of
a concern as differing *content* requirements.

### Kelper Search Interface

<figure>
<img src="THREDDS-Search.png" title="THREDDS-Search.png" />
<figcaption>THREDDS-Search.png</figcaption>
</figure>

In order to build a complete end-to-end system to search for DAP data
within Kepler, and the Ocean Use Case in particular, Kepler must be
modified to search over EML's <SpatialRaster> element (or the
equivalent) and it must have an interface that will provide a way for
users to make those searches. The mock up to the right is one way to do
that.

The interface shows that the criteria needed for the Ocean Use Case are:

1.  Geographical location, as a box in latitude and longitude
2.  Time range
3.  Resolution
4.  Parameter, where the list of parameters is:

:\* SST, SSH, Chlorophyll, precipitation, Color, Vector Wind, Vector
Stress

### Data server URLS for the REAP Ocean Use Case

#### GHRSST data at NODC

There are a total of 16 data sets (I think). Many of these are updated
daily or at least often.

Top level; there are a number of products under this.
<http://data.nodc.noaa.gov/opendap/ghrsst/>

- Within .../ghrsst:

:\* L2P/GOES11

:\* L2P/GOES12

:\* L2P/SEVIRI_SST

:\* L2P_GRIDDED

:\* L4

*Results:* 150,000, 31 classes using the three-rule classifier.

#### URI 1-km data sets

- <http://satdat1.gso.uri.edu/opendap>

*Results:* 159,206 DDX URLs; 15h 26m; 41 classes using the three-rule
classifier.

#### Goddard SFC Servers

- <http://acdisc.sci.gsfc.nasa.gov/opendap/>
- <http://airspar1u.ecs.nasa.gov/opendap/>
- <http://airscal1u.ecs.nasa.gov/opendap/>
- <http://airscal2u.ecs.nasa.gov/opendap/>
- <http://goldsmr1.sci.gsfc.nasa.gov/opendap/>
- <http://goldsmr2.sci.gsfc.nasa.gov/opendap/>
- <http://goldsmr3.sci.gsfc.nasa.gov/opendap/>
- <http://disc3.nascom.nasa.gov/opendap/>
- <http://disc2.nascom.nasa.gov/opendap/>

#### PMEL

Hankin's site: <http://ferret.pmel.noaa.gov/thredds/dodsC/data/PMEL/>
Not sure how you really crawl this site, but since I don't exactly what
your crawler does maybe it is easy or you can figger it out.

#### PFEL

Mendelssohn's site:
<http://oceanwatch.pfeg.noaa.gov/thredds/catalog.html>

#### Hawaii

site: <http://apdrc.soest.hawaii.edu:80/dods/public_data/>. Again, this
one is very well organized, but may be in a form that precludes crawling
although I'm pretty sure that they are using a TDS.

#### IFREMER

I don't know how to access this site. Here's a URL for some data in the
site:
<http://www.ifremer.fr/thredds3/standard/dodsC/ODYSSEA-SST-NRT_VIEW_BEST_ESTIMATE.html>,
but I can't go up a level. Not sure what's up here. And then there's the
MyOcean site. I can send a message off to Jean-François Pioli if you
like?

#### HyCOM

For now we will pass on the HyCOM data sets because the server seems
broken.

#### CSIRO

I'm not sure if the use-case uses data from CSIRO, but here's one URL to
data they have: <http://opendap.csiro.au/opendap/>

## Risks

1.  In order for this to work, Kepler's search over the EML records has
    to be modified so that it includes using the information in a
    *<spatialRaster>* element.
2.  This design does not address how Kepler users would actually perform
    a search (it does not specify a User Interface). Since the existing
    Kepler search over EML records appears to return a set of results
    where each data set is a different actor, it's not clear how to make
    this mesh with the DAP actor, where a specific data set (i.e., URL)
    is a parameter to the Actor. Maybe have the search return a set of
    Actor 'objects' each of which has the URL parameter bound to a
    value?
3.  Even though the design tries to minimize the amount of new metadata
    to be written, the reduction might not be enough to be a viable
    solution.
4.  One aspect of the problem identified in the design overview is that
    data sources tend to 'move' over time and any general solution will
    need the ability crawl host/domain names and find moved data
    sources. This design lacks a crawling component. That should not be
    an issue for the Ocean Use-Case data sources, but it limits the
    solution in the general case. A potential solution exists in the
    TPAC crawler.

## Deliverables

1.  The Guide: A manual for the DP that provides information about what
    fragments to add to a data set to support a specific metadata record
    type. Initially this will support EML records that can be used in
    Kepler
2.  A modification of Kepler so that it will search for geo-spatial
    data.
3.  The NcML-AIS (implemented as a module for the BES).
    <font color="red">Done 10/1/2009</font>
4.  The NcML-Aggregator (implemented as a module for the BES, likely
    combined with the AIS) <font color="red">Partially complete
    1/1/2010. We can perform aggregations of Grids using the JoinNew
    strategy and NcML's dataset scan feature is mostly implemented but
    lacks the 'date' feature. Likely completion is 2/1/2010.</font>
5.  A modification to the DAP (implemented in libdap++) that provides a
    way to insert XML into the DAP variable attributes
    <font color="red">Done 3/2/2009</font>
6.  A crawler to harvest DDXs that will be transformed into EML.
    <font color="red">Partly completed 3/5/10. The code is described
    above under [THREDDS Crawler](THREDDS_Crawler "wikilink")</font>
7.  A tool that can produce a metadata record of type *X* for a given
    data set (DAP URL). Likely based on XSLT
8.  A tool, bundled with the Guide, to test the acceptability of the
    metadata generated by the system (i.e., is the resulting EML
    document not only valid EML but will it work in the Kepler client).

## Period of use

This will be initially developed for use by the REAP project, which has
a period of performance that ends in May 2010.

Some elements will be part of a production version of the BES (the
NcML-AIS/Aggregator) while other parts are primarily proof-of-concept
and will not be useful beyond the REAP project (the Guide sections
regarding EML). Hopefully the XSLT will survive and prove useful in
other applications.

## Appendix

### Setting up MetaCat on OS/X

**Using OS/X 10.5.8**

#### Prerequisites

##### Java 5.0

This is present on OS/X 10.5.8. Set *JAVA_HOME* to
*/System/Library/Frameworks/JavaVM.framework/Versions/1.5/Home*. If java
1.5 is the default, then the shorter version
*/System/Library/Frameworks/JavaVM.framework/Home* will also work. Set
JAVA_HOME in both *.bashrc* and *environment.plist*

Here's a reference on stuff about *JAVA_HOME*:
<http://www.oreillynet.com/mac/blog/2001/04/mac_os_x_java_wheres_my_java_h.html>
and Apple's technical documentation on Frameworks:
<http://developer.apple.com/mac/library/documentation/MacOSX/Conceptual/BPFrameworks/Frameworks.html>

##### Postgres

Get Postgres from <http://www.postgresql.org/download/macosx> and
install. The only trick here is that the Mac is setup with too little
shared memory for postgres to work well, so you need to create a
*sysctl.conf* in */etc/* and restart the machine. Here's what I put in
mine:

    kern.sysv.shmmax=1610612736
    kern.sysv.shmall=393216
    kern.sysv.shmmin=1
    kern.sysv.shmmni=32
    kern.sysv.shmseg=8
    kern.maxprocperuid=512
    kern.maxproc=2048

##### Ant

This is needed only if you will be building Java from source. But it is
really easy, so ...

Get Ant from Apache, install it somewhere sensible like /usr/local and
set ANT_HOME to point there in both *.bashrc* and *environment.plist*.

##### Tomcat

Same as Ant above, but set the environment variable *CATALINA_HOME*.

I installed both 5.5 and 6.0, just to see if metacat will work with 6.0.

##### Apache

OS/X comes with this. Here's a set of tutorials from the most basic
starting point: <http://onlamp.com/pub/ct/49>. Short version: Apple menu
--\> Preferences --\> Sharing --\> Web Sharing (turn it on).

The command *httpd -V* returns a fair amount of information; The root
directory of the server is */etc/apache2*; the modules are in
*/usr/libexec/apache2*; and the document root for the machine is
*/Library/Webserver/Documents/*.

<font color="red">First get metacat running with postgres & tomcat and
then look at apache. Likely we can use the proxy and rewrite modules in
place of mod_jk, thus avoiding the mod_jk hassles like
compilation.</font>