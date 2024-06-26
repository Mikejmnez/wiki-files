## Overview

The StaticRDFCatalog uses semantic web technologies to create mappings
between DAP data sets and WCS Coverages. A WCS Coverage is a very
specific data type and is much more constrained than the more general
DAP data model. Thus, only certain DAP data sets will be representable
as WCS Coverages. Evaluating which ones can be represented as WCS
Coverages requires the semantic analysis of the metadata associated with
each data set. Since the DAP has no actual semantic metadata
requirements for metadata it is necessary (at least for the moment) to
look only at DAP data sets that have metadata that conforms to some well
known metadata convention or standard. Since the semantics of the
convention are know it is then possible to write inferencing rules that
relate pieces of information in one convention/standard to the
equivalent information in another convention/standard.

Even when using a DAP data set that has metadata that conforms to a well
known metadata convention (such as CF-1.0) the existing metadata in the
DAP framework may be inadequate to make a complete 1:1 mapping into the
representation of a WCS Coverage. In that case we rely on the Hyrax
[NcML
Module](http://docs.opendap.org/index.php/BES_-_Modules_-_NcML_Module)
to allow us to add metadata components to the DAP metadata that will
allow the semantic engine to complete the construction of a wcs:Coverage
metadata element.

[**Semantic Generation Of WCS Catalogs**: A more detailed discussion of
the semantic processing may be found
here.](Semantic_Generation_Of_WCS_Catalogs "wikilink")

## What makes a DAP data set a WCS Coverage?

At its simplest, a DAP data set can also be served as a WCS Coverage
when it has a Grid variable that can be geolocated (a more detailed
analysis can be found on the [WCS Data Model
Mapping](WCS_Data_Model_Mapping "wikilink") page). The DAP does not
require a data set to include georeferencing information, however many
DAP data sets already contain metadata that observes one or more
metadata conventions. By writing inferencing rules that leverage
existing metadata, we can find what we need to support the WCS web
service. Information missing from the data set can be added using the
[NcML handler](BES_-_Modules_-_NcML_Module "wikilink"), so the extra
metadata needed can be made available without actually modifying the
orignal data set's file(s).

### Required Conventions

DAP data sets that use these metadata conventions and that have the
required data structures can be served as WCS Coverages.

#### [CF-1.x Convention](http://cf-pcmdi.llnl.gov/)

The [Climate and Forecast convention](http://cf-pcmdi.llnl.gov/) is
heavily used at UCAR and NOAA and many DAP data sets are available that
conform to this convention.

#### [*Unidata Dataset Discovery v1.0* convention](http://www.unidata.ucar.edu/software/netcdf-java/formats/DataDiscoveryAttConvention.html)

The [*Unidata Dataset Discovery v1.0*
convention](http://www.unidata.ucar.edu/software/netcdf-java/formats/DataDiscoveryAttConvention.html)
is used to embed dataset discovery metadata inside a netcdf file. In
particular, it has explicit geospatial and temporal limits which
corresponds well with information needed to define a WCS coverage.

### Augmenting Dataset Metadata with NcML

As previously mentioned it will most likely be the case that existing
DAP metadata, even that which follows the CF-1.0 convention, will be
missing information required to form a complete wcs:CoverageDescription
of the data set. We will use the new Hyrax NcML Module to add metadata
to the data set in such a manner that the semantic engine may work upon
it to produce a happy result. The most direct way of doing this is to
directly add the missing WCS information to the data set components by
placing the WCS XML content to DAP Attributes of type '**'OtherXML**.

For example the CF convention alone does specify all of the metadata
necessary to "promote" a data set to a WCS coverage. We would need to
add at minimum the information about the geospatial extents of the data
set.

We can do that using NcML and the OtherXML attribute type. This NcML
file adds the wcs:Domain to the data set
/netcdf/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc metadata:

<font size="2">

``` xml
 <netcdf ns="http://www.unidata.ucar.edu/namespaces/netcdf/ncml-2.2"
        location="/netcdf/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc">
 <b>
   <attribute name="WcsMetadata" type="OtherXML">
       <Domain ns="http://www.opengis.net/wcs/1.1"
           xmlns:ows="http://www.opengis.net/ows/1.1"
           xmlns:gml="http://www.opengis.net/gml">
           <SpatialDomain>
               <ows:BoundingBox crs="urn:ogc:def:crs:EPSG::4326">
                   <ows:LowerCorner>-97.8839 21.736</ows:LowerCorner>
                   <ows:UpperCorner>-57.2312 46.4944</ows:UpperCorner>
               </ows:BoundingBox>
           </SpatialDomain>
           <TemporalDomain>
               <gml:timePosition>2008-03-27T16:00:00.000Z</gml:timePosition>
           </TemporalDomain>
       </Domain>
       <SupportedCRS ns="http://www.opengis.net/wcs/1.1">urn:ogc:def:crs:EPSG::4326</SupportedCRS>
       <SupportedFormat ns="http://www.opengis.net/wcs/1.1">netcdf-cf1.0</SupportedFormat>
       <SupportedFormat ns="http://www.opengis.net/wcs/1.1">dap2.0</SupportedFormat>
   </attribute>
 </b>
 </netcdf>
```

</font>

In this example we can also see that the wcs:SupportedCRS (Coordinate
Reference System) are supplied. The CF convention does not include CRS
metadata, the WCS metadata requires them.

[The resulting DDX is
here.](StaticRDFCatalog_NcML_Example_DDX "wikilink")

The wcs:SupportedFormat elements are there because the server is not yet
able to determine this on the fly. (*See ticket
[1466](https://scm.opendap.org/trac/ticket/1466%7CTicket)*)

Read about the NcML Module here:

- [NcML Module
  Manual](http://docs.opendap.org/index.php/BES_-_Modules_-_NcML_Module)
- [Use Case: Adding metadata using the NcML
  Module](http://docs.opendap.org/index.php/AIS_Using_NcML)
- [Use Case: Aggregating DAP data sets using the NcML
  Module](http://docs.opendap.org/index.php/BES_Aggregation_using_NcML)

## Configuration

As part of the WCS Service the configuration of the StaticRDFCatalog is
held in the <font face="Courier">wcs_service.xml</font> file located the
WCS Service's content directory. StaticRDFCatalog reads it's
configuration from the content of the WcsCatalog element in the
WcsService configuration.

Catalog Implementation Class Name
opendap.semantics.IRISail.StaticRDFCatalog

Usage

<font size="2">

``` xml
 <WcsCatalog className="opendap.semantics.IRISail.StaticRDFCatalog">
     .
     .
     .
 </WcsCatalog>
```

</font>

### Adding Data Sets To The Catalog

In order for the WCS service to work it must know which DAP data sets
are to be served as WCS coverages. Datasets can be added to the catalog
in the following ways.

#### dataset

A **dataset** element identifies a single DAP data set that is to be
served as a WCS coverage. The **dataset** element must contain the fully
qualified data access URL for a DAP data set. This URL will be examined
and the software will attempt to get the RDF version of the data set's
DDX from the DAP server.

Usage

<font size="2">

``` xml
 <WcsCatalog>
   <dataset>http://localhost:8080/opendap/data/nc/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.ddx</dataset>
 </WcsCatalog>
```

</font>

#### ThreddsCatalog

A **ThreddsCatalog** element identifies a THREDDS catalog which contains
DAP data access URLs that point to DAP data sets that will be served as
WCS coverages. Each DAP data set in the catalog will be served as a
separate wcs:Coverage. The **recurse** attribute determines if the
software will follow **catalogRef** links in the THREDDS catalog to
ingest the entire catalog hierarchy starting at the node identified by
the URL.

Usage

<font size="2">

``` xml
 <WcsCatalog>
   <ThreddsCatalog recurse="false" >http://test.opendap.org:8080/opendap/coverage/catalog.xml</ThreddsCatalog>
 </WcsCatalog>
```

</font>

#### RdfImport

In addition to adding references to DAP data sets additional RDF
documents (ontologies, crosswalks, etc.) may be added to the inferencing
repository using the **RdfImport** element.

An **RdfImport** element identifies a single RDF file to load directly
into the semantic repository. This is a mechanism for loading additional
OWL ontologies and inference rules into the system at start-up. The
RdfImport element must contain the fully qualified URL for an RDF file.

Usage

<font size="2">

``` xml
 <WcsCatalog>
   <RdfImport>http://iri.columbia.edu/~benno/opendaptest/daptestAll.owl</RdfImport>
 </WcsCatalog>
```

</font>

### Overriding Default Paths

StaticRdfCatalog relies on two local paths for its operation. Normally
these are determined at runtime by the WCS Servlet and passed to the
catalog. However, there may be times when it is useful/necessary to
override the defaults encoded into the software.

#### CacheDirectory

The **CacheDirectory** element is an optional element used to inform the
software where it can write things to the local disk, either as a
scratch space or as a way to persist state. If omitted it defaults to:
*\$CATALINA_HOME/content/opendap/<prefix>/StaticRDFCatalog* where
<prefix> is specified in the WCS DIspatchHandler configuration.

Usage

<font size="2">

``` xml
 <WcsCatalog>
   <CacheDirectory>/Users/ndp/OPeNDAP/Projects/Hyrax/swdev/ioos/apache-tomcat-6.0.14/content/opendap/WCS/StaticRDFCatalog</CacheDirectory >
 </WcsCatalog>
```

</font>

#### ResourcePath

The **ResourcePath** element is an optional element used to inform the
software where it find documents (such as XSLT files) that it relies on
to function. If omitted it defaults to:
*\$CATALINA_HOME/webapps/opendap/<context>* where <context> is defined
by the deployment context of the WCS Servlet. ***This is a debugging
option and should be omitted for normal operations.***

Usage

<font size="2">

``` xml
 <WcsCatalog>
   <ResourcePath>/Users/ndp/OPeNDAP/Projects/Hyrax/swdev/ioos/apache-tomcat-6.0.14/webapps/opendap/WCS/</ResourcePath>
 </WcsCatalog>
```

</font>

### Controlling Catalog Update Behavior

The **useUpdateCatalogThread** element is used to control the way in
which the StaticRDFCatalog updates its semantic repository and Coverage
catalog. Using this element will cause StaticRDFCatalog to spawn a
worker thread that will update the semantic repository (and thus the WCS
catalog holdings) in the background. If the **useUpdateCatalogThread**
element is omitted StaticRDFCatalog will not spawn a worker thread, and
will attempt to update it's holdings at startup. This attempt may fail,
and the update will not be made.

The **firstUpdateDelay** attribute controls how long (in seconds) the
worker thread will wait before making the first update. The
**updateInterval** attribute is used to specify how frequently (in
seconds) the catalog should be updated.

Usage

<font size="2">

``` xml
 <WcsCatalog className="opendap.semantics.IRISail.StaticRDFCatalog">
   <useUpdateCatalogThread updateInterval="3600" firstUpdateDelay="60"/>
 </WcsCatalog>
```

</font>

### Example Configuration (wcs_service.xml)

<font size="2">

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<WcsService>
    <WcsCatalog className="opendap.semantics.IRISail.StaticRDFCatalog">
    <useUpdateCatalogThread updateInterval="3600" firstUpdateDelay="60" />

    <ThreddsCatalog>http://test.opendap.org:8080/opendap/coverage/catalog.xml</ThreddsCatalog>
    <ThreddsCatalog>http://motherlode.ucar.edu:8080/thredds/idd/satellite.xml</ThreddsCatalog>

    <dataset>http://localhost:8080/opendap/data/nc/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.ddx</dataset>
    <dataset>http://localhost:8080/opendap/data/nc/examples/ECMWF_ERA-40_subset.nc</dataset>
    <dataset>http://localhost:8080/opendap/data/nc/examples/ECMWF_ERA-40_subset.nc.rdf</dataset>

    <RdfImport>http://iri.columbia.edu/~benno/opendaptest/daptestAll.owl</RdfImport>
    </WcsCatalog>

</WcsService >
```

</font>

[Category:WCS](Category:WCS "wikilink")