## Overview

Goal
Improved THREDDS catalog responses from dynamically generated (BES)
catalogs.

THREDDS catalogs typically contain metadata beyond the minimum required
to simply list the catalogs holdings. This additional metadata is often
comprised of *[Digital Library Metadata
Elements](http://www.unidata.ucar.edu/projects/THREDDS/tech/catalog/InvCatalogSpec.html#dlElements)*.
The Hyrax server is not currently able to produce THREDDS catalogs with
this kind of metadata. Since these metadata elements provide crucial
catalog content for dataset discovery, Hyrax needs to add this
functionality.

### The Problem

In a section of this THREDDS catalog:
<http://blackburn.whoi.edu:8081/thredds/bathy_catalog.xml> We can see
the Digital Library Metadata Elements:

`   `<dataset name="USGS Vineyard Sound Relief Model (1 arc sec)" ID="bathy/vs_1sec_20070725.nc"
             urlPath="bathy/vs_1sec_20070725.nc">
`       `<serviceName>`Compound`</serviceName>
<b>
`       `<authority>`gov.usgs.er.whsc`</authority>
`       `<dataType>`GRID`</dataType>
`       `<dataFormat>`NetCDF`</dataFormat>
`       `<documentation xlink:href="http://stellwagen.er.usgs.gov/models/grids/CGSherwo.doc"
                       xlink:title="USGS Vineyard Sound Coastal Relief Model (1 arc second)"/>
`       `<creator>
`           `<name vocabulary="DIF">`WHSC/USGS`</name>
`           `<contact url="http://www.usgs.gov/" email="rsignell@usgs.gov"/>
`       `</creator>
`       `<publisher>
`           `<name vocabulary="DIF">`WHSC/USGS`</name>
`           `<contact url="http://www.usgs.gov/" email="rsignell@usgs.gov"/>
`       `</publisher>
`       `<geospatialCoverage>
`           `<northsouth>
`               `<start>`41.0`</start>
`               `<size>`1.4`</size>
`               `<units>`degrees_north`</units>
`           `</northsouth>
`           `<eastwest>
`               `<start>`-71.2`</start>
`               `<size>`0.5`</size>
`               `<units>`degrees_east`</units>
`           `</eastwest>
`           `<updown>
`               `<start>`-277.25`</start>
`               `<size>`459.6`</size>
`               `<units>`meters`</units>
`           `</updown>
`       `</geospatialCoverage>
</b>
`   `</dataset>

A dataset element in a THREDDS catalog from Hyrax, generated from a BES
showCatalog request, contains none of the Digital Library Metadata
Elements:

`   `<dataset name="200803061600_HFRadar_USEGC_6km_rtv_SIO.ncml" ID="netcdf/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.ncml">
`     `<dataSize units="bytes">`1162`</dataSize>
`     `<date type="modified">`2010-02-22T23:19:51`</date>
`     `<access serviceName="dap" urlPath="netcdf/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.ncml" />
`   `</dataset>

However, the
[DDX](http://test.opendap.org:8080/opendap/netcdf/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.ddx)
for the dataset in question contains a significant amount of metadata.
This includes DAP attributes (associated with the Unidata Dataset
Discovery convention) that relate to geospatial extents:

`       `<Attribute name="geospatial_lat_min" type="Float32">
`           `<value>`21.73596001`</value>
`       `</Attribute>
`       `<Attribute name="geospatial_lat_max" type="Float32">
`           `<value>`46.49441910`</value>
`       `</Attribute>
`       `<Attribute name="geospatial_lon_min" type="Float32">
`           `<value>`-97.88385010`</value>
`       `</Attribute>
`       `<Attribute name="geospatial_lon_max" type="Float32">
`           `<value>`-57.23120880`</value>
`       `</Attribute>

## THREDDS Background Information

### Existing THREDDS/NCML Implementations

- [THREDDS Data Server
  (TDS)](http://www.unidata.ucar.edu/projects/THREDDS/tech/TDS.html)
- [OPeNDAP Hyrax server](http://www.opendap.org/download/hyrax.html)
- [GrADS Data Server (GDS)](http://www.iges.org/grads/gds/)
- [Ferret-TDS](http://ferret.pmel.noaa.gov/LAS/documentation/the-ferret-thredds-data-server-f-tds/)

### How are people using THREDDS?


*More Examples needed.*

[Linked Environments for Atmospheric Discovery
(LEAD)](https://portal.leadproject.org/gridsphere/gridsphere)

- [LEAD Resource Catalog](http://www.extreme.indiana.edu/rescat/) ().

SURA Coastal Ocean Observing and Prediction (SCOOP) Program

- <http://scoop.sura.org/index.html>
- <https://littlefoot.tamu.edu/>
- <http://scoop.tamu.edu/thredds.html>
- <http://scoop.tamu.edu/metadata.html>

eScience

- <http://ieeexplore.ieee.org/Xplore/login.jsp?url=http%3A%2F%2Fieeexplore.ieee.org%2Fiel5%2F4426856%2F4426857%2F04426867.pdf%3Farnumber%3D4426867&authDecision=-203>
- <http://research.microsoft.com/en-us/um/people/yoges/talks/swf08-e2escientificdatamanagement.pdf>

More...

- <http://www.alexandria.ucsb.edu/~gjanee/archive/2007/opendap-devcon.ppt>
- [A Virtual Oceanographic Data
  Center](http://csse.usc.edu/~mattmann/pubs/WWW2009.pdf)
- <http://data.aims.gov.au/extpubs/attachmentDownload?docID=3137>
- <http://www.wmo.int/pages/prog/www/WDM/Workshop_metadata/thredds.ppt>

### How does the TDS use THREDDS and NcML?

The TDS uses THREDDS catalog files (*configuration catalogs*) stored on
the local disk as a source of both catalog content and configuration
information.

From the [TDS
tutorial](http://www.unidata.ucar.edu/projects/THREDDS/tech/tutorial/):

> TDS *configuration catalogs* are THREDDS catalogs with a few
> extensions. They contain information detailing the datasets the TDS
> will serve and what services will be available for each dataset:
>
> - The **datasetRoot** and **datasetScan** elements are extensions
>   that:
>   - provide mappings between incoming URL requests and directories on
>     disk; and
>   - are used in the detailing of the datasets the TDS will serve.
> - Available services are indicated in the normal THREDDS catalog
>   manner with service name references.
>
>
> The TDS configuration catalogs represent the top-level catalogs served
> by the TDS:
>
> - The configuration information is only needed by the server.
> - The client view of the catalogs does not contain any configuration
>   information.

#### Content

The content served by a particular instance of a TDS is defined via
[*configuration
catalogs*](http://www.unidata.ucar.edu/projects/THREDDS/tech/tutorial/ConfigCatalogs.html).
The *default root catalog* is the root of this content. It may reference
other *configuration catalogs* using the catalogRef element. These
catalogs can also contain catalogRef elements, etc. These catalogs may
define dataset content, including metadata and access elements that are
passed directly (as in unchanged) to a requesting client. A dataset
declaration may also contain NcML that makes it a virtual dataset (see
below), and thredds:metadata content (containing Digital Library
Metadata Elements) which will appear in catalog response as part of the
dataset declaration. Data sets can also be defined using a
**datasetScan** element which may also include inheritable
thredds:metadata content.

#### Configuration

##### thredds:datasetRoot

Each **datasetRoot** element defines a single mapping between a URL base
path and a directory. It is used in conjunction with **dataset**
elements in the same catalog file to proved dataset access.

##### thredds:datasetScan

Each **datasetScan** element also defines a single mapping between a URL
base path and a directory. Unlike the **datasetRoot** element which
works with dataset elements to define the datasets served, the
**datasetScan** element will automatically serve some or all of the
datasets found in the mapped directory. The **datasetScan** element may
also contain a thredds:metadata block whose contents (Digital Library
Metadata Elements) may be inherited by all of the data sets located by
the datasetScan. NcML may also be used to add metadat attributes to the
data set content. This is different from the thredds:metadata block. The
thredds:metadata block only affects the content of the catalogs. The
NcML block can add moth metadata (attributes) and data directly to the
data set(s).

##### Embedded NcML

[The TDS can use embedded NcML
elements](http://www.unidata.ucar.edu/projects/THREDDS/tech/tutorial/NcML.htm)
to add new, modify existing, or aggregate collections of data sets.

Within a TDS *configuration catalog*, NcML content embedded in a TDS
**dataset** element creates a *self-contained NcML dataset*. The TDS
dataset does not refer to a data root, because the NcML contains its own
location. The TDS dataset must have a unique URL path (this is true for
all TDS datasets), but unlike a regular dataset, does not have to match
a data root.

In this excerpt from a THREDDS catalog XML file that is read by the TDS
there is an ncml:netcdf element:

`   `<dataset name="1-day" ID="satellite/PH/ssta/1day" urlPath="satellite/PH/ssta/1day">
`     `<serviceName>`all`</serviceName>
`     `<b><netcdf ns="http://www.unidata.ucar.edu/namespaces/netcdf/ncml-2.2">` `
`       `<aggregation dimName="time" type="joinExisting" recheckEvery="720 min">` `
`         `<variableAgg name="PHssta" />` `
`         `<scan location="/u00/satellite/PH/ssta/1day/" suffix=".nc" />` `
`       `</aggregation>
`     `</netcdf></b>
`   `</dataset>

The ncml:netcdf element instructs the TDS that this is a logical dataset
built from an aggregation, and contains the information need by the TDS
to create the logical dataset from files stored on disk.

NcML can also be used in a **datasetScan** element. If an NcML element
is added to a **datasetScan**, it will modify all of the datasets
contained within the DatasetScan. It is not self-contained, however,
since it gets its location from the datasets that are dynamically
scanned.

## Possible Designs

### Questions?

1.  How can we add the Digital Library Metadata Elements content to
    Hyrax THREDDS catalogs?
2.  Can we capitalize on existing metadata to build Digital Library
    Metadata Element content?
3.  How do we propagate (some subset of) dataset metadata "up" into our
    THREDDS catalogs? People are requesting this.
4.  Can we use NcML to push new metadata down into collections vis-a-vis
    the inherited metadata section of a dataset scan? I mean add
    metadata terms to the thredds catalogs using NcML. OtherXML?
    Attributes? Both?
5.  How do we add new services (say for example WCS) to the services
    listed in our THREDDS catalogs along with the appropriate data
    access links for the appropriate datasets? Is this even realistic
    given the architecture of our WCS?
6.  This is requested functionality for National Catalog Viewer in
    development by IOOS. And is needed by June 2010. Can we pull that
    off?

### Caching

It may be that because of the cost of acquiring and processing the site
metadata that we will need to cache the "enriched" catalogs. There are a
number of ways to accomplish this. One that comes to mind immediately is
to write down the catalog in a "mirror" of the site graph and use the
existing thredds d=static catalog handler to read it. That would allow
the catalog generation to be an asynchronous process taht could be
scheduled, like a cron job.

### Technologies

#### Explicit Injection and XSLT

pre-conditions:

- Write NcML for each dataset to place the desired Digital Library
  Metadata Elements XML directly into the DDX.

RunTime:

- Use XSLT transforms to extract the THREDDS namespace metadata from the
  DDX and add it to the THREDDS catalog response.
- Cache all DDX's along with the time of caching. Update only as
  required.
- Use cached DDX's to formulate subsequent responses.

#### XSLT semantic mappings

RunTime:

- Use XSLT transforms to transform metadata in different conventions
  (like CF-1.1 and UDDv1) in the DDX into THREDDS namespace metadata and
  add it to the THREDDS catalog response.
- Cache all DDX's along with the time of caching. Update only as
  required.
- Use cached DDX's to formulate subsequent responses.

#### Semantic Inferencing

pre-conditions:

- Write inferencing rules that map metadata conventions (CF-1.1 and
  UDDv1 to begin) to the THREDDS Digital Library Metadata Elements.

Operational Startup:

- Ingest all of the DDXs in Hyrax into a semantic repository.
- Run the repository rules to completion.
- Trigger asynchronous updates.

RunTime:

- Query repository for catalog node contents.
- return catalog.