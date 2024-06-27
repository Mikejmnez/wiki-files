<font size="+1" color="red">This is an old document that captures the
starting point of the OPULS design work. It's out of date and should be
referenced only as a baseline for the work.</font>

[\<-- back to OPULS Development](OPULS_Development "wikilink")

Author: NDP

## Overview

We intend to provide a 'Services' or 'Capabilities' response for DAP4.

Currently DAP servers provide a number of dataset level responses
(services). These include DAP services such as the DDS, DAS, and DDX,
plus other services that have been added over time such as the RDF and
ISO responses. There is currently no way for a user (or software agent)
to discover which of these many services might be available for a
particular dataset. While THREDDS catalogs do provide a mechanism for
determining which services are associated with which datasets, they
don't actually provide a mechanism for discovering the details of the
various service URLs. A THREDDS catalog might identify a DAP service,
but the URL that can be assembled from the THREDDS catalog for that
service is limited to just the base dataset URL. Typically dereferencing
this URL will provide one of two things - either the underlying data
file (for example a netcdf file) or an HTTP 403 error if direct access
to the underlying files has been disabled in the server configuration.

**Example:**

This URL points to a dataset on a DAP server: <font size="2">

[`http://test.opendap.org:8080/opendap/data/nc/fnoc1.nc`](http://test.opendap.org:8080/opendap/data/nc/fnoc1.nc)

</font>

Dereferencing the link will not connect you to a DAP service or DAP
response. It will only return the underlying netcdf file. The only way
to know that DAP services exist for this is by reading the URL path and
recognizing that it is likely an instance of a DAP server based on the
position of the 'opendap' string in the URL.

There is no guarantee that dereferencing this URL will even yield the
underlying file, as that behavior is a configuration option for the
server. If that access is not allowed in the configuration, then
dereferencing the URL will simply return an HTTP 403 (forbidden) error.

## Possible Technologies

I looked at [Web Services Description Language
(WSDL)](http://en.wikipedia.org/wiki/Web_Services_Description_Language),
[Web Application Description Language
(WADL)](http://en.wikipedia.org/wiki/Web_Application_Description_Language),
and OGC GetCapabilities as possible syntaxes to leverage. All three make
assumptions about the structure of the URL and constraint expression
(aka query string) that would require an extension to an existing
standard be written to support our existing syntax.

## Proposal

I propose that base DAP URLs should return an XML document describing
the DAP services available for the dataset.

At minimum this document should provide:

- A human readable title of the service (DDX, DDS, etc.)
- A link that can be dereferenced to get the service response for the
  dataset in question.
- An unambiguous, unique to the service, xlink:role identifier that
  serves as a way to clearly identify which service on the list purports
  to do what well know thing.
- A brief description of the service
- A link to a complete, human readable, description of the service
  response and it's semantics
- A reference to an XSLT that a browser would use to render the
  description into HTML for a presentation view.

[Jimg](User:Jimg "wikilink") 12:47, 3 February 2012 (PST) This proposed
feature is an example of [agent-driven content
negotiation](http://tools.ietf.org/html/draft-ietf-httpbis-p3-payload-16#section-5.2).
To wit:

> With agent-driven negotiation, selection of the best representation
> for a response is performed by the user agent after receiving an
> initial response from the origin server. Selection is based on a list
> of the available representations of the response included within the
> header fields or body of the initial response, with each
> representation identified by its own URI.

Additionally this dataset services description document might contain:

- Descriptions and syntax of server side functions available for the
  dataset

### Static or Dynamic Response?

As the DAP servers evolve to support user authentication and sessions
the content of the dataset services description document should become
dynamic. By this I mean that some services and server functions might be
available only to certain users. If an authenticated user asks for a
particular dataset services description document they would see all the
the services available to them, which might differ significantly from
those available to a different user. As powerful server side functions
such as re-gridding and re-projection become available we anticipate
that data providers may not wish to allow just anyone to utilize them
because of the potential burden they may place on the data center's
computing resources.

It may be that some services may not be available for all datasets. This
argues (along with the previous comment about users and roles) that the
service description needs to be generated dynamically and that it should
be context sensitive w.r.t. user, role, and dataset.

Implementation of a *dynamic* services response implies that the
software generating that response *can* get information about what is
applicable for a given user and a given dataset, where the latter could
be quite complicated. The alternative is to have all possible responses
described in the services description with the understanding that they
might return an error code under one set of circumstances. This would be
far easier for servers, and likely require no additional logic in
clients (because they will have to account that unforeseen errors will
happen in any case). The opposing argument is that presenting
information known to be false is a poor practice - and it's hard not to
agree.

I suggest that we split this into two cases:

Authenticated/privileged versus anonymous users: For this case, the response should be dynamic and reflect what the user can actually do.
Datasets for which server-side functions are/are-not applicable: For this case, however, the response should not attempt to determine what is applicable to a particular dataset, but that it optionally can indicate that a particular function *can*, with certainty, be applied.

These are two fundamentally different cases because the semantics or the
first are under our control, while those of the latter are not. In the
first cases, the server defines the classes of users and the sets that
describe their capabilities. It is possible for the server to know that
a given user can never run a regridding operation, for example, because
they lack the authorization to do so. However, for datasets, determining
which functions are applicable, for example, requires *understanding the
semantics of the metadata bound to that dataset* which can be done in
some cases, but not all.

#### How this limits the response's utility, plus a solution

The problem with not providing a list of functions that is known to be
applicable to a dataset, is that limits an important source of semantic
information about those datasets. If we know that a regridding function
can be allied to dataset X, then we know a fair amount that dataset. In
addition, it is possible that the service response could include, at
some future time, either directly or via inference, semantic information
based on server-side operations that were known to be possible.

If we extend the *Function* element of the service response so that the
element listing a function contains an attribute that indicates that the
server knows that this function can definitely be used with the dataset,
then we can support semantic systems without forcing the server to know
about the applicability of each function to each dataset. I would
suggest that we add the attribute *applicability* and support the three
values of *yes*, *no*, and *unknown* with the latter being the default
value.

## [Services](DAP4_Web_Services "wikilink")

A Service is represented by:

- A simple human readable name called 'title'.
- An access URL and an associated media-type returned by that URL.
- A unique "namespace" like id that is used to define an xlink:role
  attribute. This is intended to allow people and software to
  unambiguously identify specific services, irrespective of their human
  readable title. The idea is that that the xlink:role tells you what is
  going to happen when you go to xlink:href.
- An optional description.

A service is accessed by dereferencing its access URL, which is
typically constructed by adding some type of suffix to the dataset's
referent (aka base) URL. The way in which the query string (aka
constraint expression) is used is defined by each service, and there is
no requirement for inter-service query string API conformity.

A more extensive discussion of the various services that might appear in
a DatasetServices document can be found on the [DAP4 Web
Services](DAP4_Web_Services "wikilink") page.

## Prototype \#1

I implemented a prototype DatasetServices document for Hyrax. It is not
dynamic - all datasets and all users get the same basic response (delta
the embedded URL's that return the various service responses).

It looks like this: <font size="2">

``` xml

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<?xml-stylesheet type="text/xsl" href="/opendap/xsl/serviceDescription.xsl"?>
<DatasetServices
    xmlns:xlink="http://www.w3.org/1999/xlink"
    xml:base="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml">

    <Service title="Data Request Form" >
        <link type="text/xml"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.html"
             xlink:role="http://services.opendap.org/dap4/data-request-form#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_HTML_DATA_Request_Form_Service">OPeNDAP HTML Data Request Form for data sub-setting and access.</Description>
    </Service>

    <Service title="Dataset">
        <link type="text/xml"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.xml"
             xlink:role="http://services.opendap.org/dap4/dataset#">
        <link type="application/json"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.xml.json"
             xlink:role="http://services.opendap.org/dap4/dataset#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Dataset_Service_-_The_metadata">DAP4 Dataset Description and Attribute XML Document.</Description>
    </Service>

    <Service title="DAP4 Data">
        <link type="multipart/mixed"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.dap"
             xlink:role="http://services.opendap.org/dap4/data#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Data_Service">DAP4 Data object.</Description>
    </Service>

    <Service title="DAP2 Data">
        <link type="application/octet-stream"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.dods"
             xlink:role="http://services.opendap.org/dap2/dods#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_Data_Service">DAP2 Data Object.</Description>
    </Service>

    <Service title="ASCII Data">
        <link type="text/plain"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.asc"
             xlink:role="http://services.opendap.org/dap4/ascii#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_ASCII_Data_Service">The DAP4 Data response in ASCII form.</Description>
    </Service>

    <Service title="NetCDF-File">
        <link type="application/x-netcdf"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.nc"
             xlink:role="http://services.opendap.org/dap4/netcdf-3#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_NetCDF_File-out_Service">NetCDF file-out response.</Description>
    </Service>

    <Service title="XML Data Response">
         <link type="text/xml"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.xdap"
             xlink:role="http://services.opendap.org/dap4/xml-data#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_XML_Data_Service">An XML document containing both the dataset's structural metadata along with data values.</Description>
    </Service>

    <Service title="DDX">
        <link type="text/xml"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.ddx"
             xlink:role="http://services.opendap.org/dap2/ddx#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_DDX_Service">OPeNDAP Data Description and Attribute XML Document.</Description>
    </Service>

    <Service title="DDS">
        <link type="text/plain"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.dds"
             xlink:role="http://services.opendap.org/dap2/dds#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_DDS_Service">OPeNDAP Data Description Structure.</Description>
    </Service>

    <Service title="DAS"
        <link type="text/plain"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.das"
             xlink:role="http://services.opendap.org/dap2/das#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_DAS_Service">OPeNDAP Dataset Attribute Structure.</Description>
    </Service>

    <Service title="RDF">
        <link type="application/rdf+xml"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.rdf"
             xlink:role="http://services.opendap.org/dap4/rdf#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_RDF_Service">An RDF representation of the Dataset response (DDX) document.</Description>
    </Service>

    <Service title="INFO">
        <link type="text/html"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.info"
             xlink:role="http://services.opendap.org/dap2/info#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_Info_Service">OPeNDAP Dataset Information Page.</Description>
    </Service>

    <Service title="Server Version">
        <link type="text/xml"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.ver"
             xlink:role="http://services.opendap.org/dap4/version#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Server_Version_Service">An XML document containing information about the software version of the server..</Description>
    </Service>

    <Service title="ISO-19115">
        <link type="text/xml"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.iso"
             xlink:role="http://services.opendap.org/dap4/iso-19115-metadata#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_ISO_19115_Service">ISO 19115 Metadata Representation of the Dataset (DDX) response.</Description>
    </Service>

    <Service title="ISO-19115-Score">
        <link type="text/html"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.rubric"
             xlink:role="http://services.opendap.org/dap4/iso-19115-score#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_ISO_Conformance_Score_Service">ISO 19115 Metadata Representation conformance score for this dataset.</Description>
    </Service>

    <Service title="File Access"
        <link type="text/xml"  ## This value depends on the file being accessed.
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.file"
             xlink:role="http://services.opendap.org/dap4/file#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Native_File_Access_Service">Access to dataset file.</Description>
    </Service>

    <Service title="Dataset Services Description">
        <link type="text/xml"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml"
             xlink:role="http://services.opendap.org/dap4/dataset#">
        <link type="application/json"
             xlink:href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.json"
             xlink:role="http://services.opendap.org/dap4/dataset#">
        <Description xlink:href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Dataset_Services_Description_Service">An XML document itemizing the Services available for this dataset.</Description>
    </Service>

    <ServerSideFunctions>
        <Function name="geogrid"
          xlink:href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#geogrid">
            <Description>Allows a DAP Grid variable to be sub-sampled using georeferenced values.</Description>
        </Function>
        <Function name="grid"
          xlink:href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#grid">
            <Description>Allows a DAP Grid variable to be sub-sampled using the values of the coordinate axes.</Description>
        </Function>
        <Function name="linear_scale"
          xlink:href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#linear_scale">
            <Description>Applies a linear scale transform to the named variable.</Description>
        </Function>
        <Function name="version"
          xlink:href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#version">
            <Description>Returns version information for each server side function.</Description>
        </Function>
    </ServerSideFunctions>
</DatasetServices>
```

</font>

I also put together a simple XSLT to make this a human friendly HTML. I
added a reference to it in the XML document. When you point a browser at
the resource URL (For example
<http://localhost:8080/opendap/data/nc/fnoc1.nc> ) the Service
Description response is returned as an XML file (above), the browser
detects the xml-stylesheet reference, grabs the XSLT, uses it to convert
the XML to HTML and renders it like this:

<figure>
<img src="ServiceDescriptionPrototype-01.png"
title="File:ServiceDescriptionPrototype-01.png" />
<figcaption><a
href="File:ServiceDescriptionPrototype-01.png">File:ServiceDescriptionPrototype-01.png</a></figcaption>
</figure>

## ~~<font color="grey">Prototype \#2</font>~~

<strike>

<font color="grey"> This prototype response is based on the OGC Web
Services Common Standard (OWS) GetCapabilities response.

<font size="2">

``` xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Capabilities ns="http://www.opengis.net/wcs/1.1" updateSequence="1288821006000">
    <!--


    Service Identification


    -->
    <ows:ServiceIdentification xmlns:ows="http://www.opengis.net/ows/1.1" xmlns:xlink="http://www.w3.org/1999/xlink">
        <ows:Title>OPeNDAP DAP Service</ows:Title>
        <ows:Abstract>A server to DAP access to datasets datasets</ows:Abstract>
        <ows:Keywords>
            <ows:Keyword>OPeNDAP</ows:Keyword>
            <ows:Keyword>nectcdf</ows:Keyword>
            <ows:Keyword>DAP</ows:Keyword>
        </ows:Keywords>
        <ows:ServiceType>DAP</ows:ServiceType>
        <ows:ServiceTypeVersion>4.0.0</ows:ServiceTypeVersion>
        <ows:Fees>NONE</ows:Fees>
        <ows:AccessConstraints>NONE</ows:AccessConstraints>
    </ows:ServiceIdentification>
    <ows:ServiceProvider xmlns:ows="http://www.opengis.net/ows/1.1" xmlns:xlink="http://www.w3.org/1999/xlink">
        <ows:ProviderName>OPeNDAP Inc.</ows:ProviderName>
        <ows:ProviderSite xlink:href="http://www.opendap.org"/>
        <ows:ServiceContact>
            <ows:IndividualName>Anonymous Smith</ows:IndividualName>
            <ows:PositionName>Sys. Admin.</ows:PositionName>
            <ows:ContactInfo>
                <ows:Phone>
                    <ows:Voice>123-456-7890</ows:Voice>
                    <ows:Facsimile>234-567-8901</ows:Facsimile>
                </ows:Phone>
                <ows:Address>
                    <ows:DeliveryPoint>165 Fictitious Dr.</ows:DeliveryPoint>
                    <ows:City>AnywhereButHere</ows:City>
                    <ows:AdministrativeArea>Rhode Island</ows:AdministrativeArea>
                    <ows:PostalCode>02882</ows:PostalCode>
                    <ows:Country>USA</ows:Country>
                    <ows:ElectronicMailAddress>support[a.t]opendap[d.o.t]org</ows:ElectronicMailAddress>
                </ows:Address>
                <ows:OnlineResource xlink:href="http://your.domain.org/"/>
                <ows:HoursOfService>24x7x365</ows:HoursOfService>
                <ows:ContactInstructions>email</ows:ContactInstructions>
            </ows:ContactInfo>
            <ows:Role>Developer</ows:Role>
        </ows:ServiceContact>
    </ows:ServiceProvider>
    <!--


    Operations Metadata


    -->
    <ows:OperationsMetadata xmlns:ows="http://www.opengis.net/ows/1.1" xmlns:xlink="http://www.w3.org/1999/xlink">
        <ows:Operation name="DAP4 Data">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.dap"/>
                </ows:HTTP>
            </ows:DCP>
            <ows:Parameter name="Format">
                <ows:AllowedValues>
                    <ows:Value>application/x-netcdf-cf1.0</ows:Value>
                    <ows:Value>application/x-dap-3.2</ows:Value>
                </ows:AllowedValues>
            </ows:Parameter>
        </ows:Operation>
        <ows:Operation name="DAP2 Data">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.dods"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetDDX">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.ddx"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetDDS">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.dds"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetDAS">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.das"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetHtmlDataRequestForm">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.html"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetInfoPage">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.info"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetRDF">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.rdf"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetNetcdfFile">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.nc"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetIso">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.iso"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetIsoRubric">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.rubric"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="DataFileAccess">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc.file"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetCapabilities">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://test.opendap.org:8090/opendap/data/nc/fnoc1.nc"/>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
    </ows:OperationsMetadata>
    <!--


    Contents


    -->
    <Contents>
        <Dataset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ns="http://xml.opendap.org/ns/DAP2" name="fnoc1.nc" xsi:schemaLocation="http://xml.opendap.org/ns/DAP2 http://xml.opendap.org/dap/dap2.xsd">
            <Attribute name="NC_GLOBAL" type="Container">
                <Attribute name="base_time" type="String">
                    <value>88- 10-00:00:00</value>
                </Attribute>
                <Attribute name="title" type="String">
                    <value> FNOC UV wind components from 1988- 10 to 1988- 13.</value>
                </Attribute>
            </Attribute>
            <Attribute name="DODS_EXTRA" type="Container">
                <Attribute name="Unlimited_Dimension" type="String">
                    <value>time_a</value>
                </Attribute>
            </Attribute>
            <Array name="u">
                <Attribute name="units" type="String">
                    <value>meter per second</value>
                </Attribute>
                <Attribute name="long_name" type="String">
                    <value>Vector wind eastward component</value>
                </Attribute>
                <Attribute name="missing_value" type="String">
                    <value>-32767</value>
                </Attribute>
                <Attribute name="scale_factor" type="String">
                    <value>0.005</value>
                </Attribute>
                <Attribute name="DODS_Name" type="String">
                    <value>UWind</value>
                </Attribute>
                <Attribute name="b" type="Byte">
                    <value>128</value>
                </Attribute>
                <Attribute name="i" type="Int32">
                    <value>32000</value>
                </Attribute>
                <Attribute name="WOA01" type="Url">
                    <value>"http://localhost/junk"</value>
                </Attribute>
                <Int16/>
                <dimension name="time_a" size="16"/>
                <dimension name="lat" size="17"/>
                <dimension name="lon" size="21"/>
            </Array>
            <Array name="v">
                <Attribute name="units" type="String">
                    <value>meter per second</value>
                </Attribute>
                <Attribute name="long_name" type="String">
                    <value>Vector wind northward component</value>
                </Attribute>
                <Attribute name="missing_value" type="String">
                    <value>-32767</value>
                </Attribute>
                <Attribute name="scale_factor" type="String">
                    <value>0.005</value>
                </Attribute>
                <Attribute name="DODS_Name" type="String">
                    <value>VWind</value>
                </Attribute>
                <Int16/>
                <dimension name="time_a" size="16"/>
                <dimension name="lat" size="17"/>
                <dimension name="lon" size="21"/>
            </Array>
            <Array name="lat">
                <Attribute name="units" type="String">
                    <value>degree North</value>
                </Attribute>
                <Float32/>
                <dimension name="lat" size="17"/>
            </Array>
            <Array name="lon">
                <Attribute name="units" type="String">
                    <value>degree East</value>
                </Attribute>
                <Float32/>
                <dimension name="lon" size="21"/>
            </Array>
            <Array name="time">
                <Attribute name="units" type="String">
                    <value>hours from base_time</value>
                </Attribute>
                <Float32/>
                <dimension name="time" size="16"/>
            </Array>
            <dataBLOB href=""/>
        </Dataset>
    </Contents>
</Capabilities>
```

</font>

The first section ows:ServiceIdentification preserves the intended
semantics of GetCapabilities very well, as the section really has little
to do with any particular web service (REST or otherwise) and everything
to do with who to contact about the service instance. Populating the
Contents section with the DDX object is similarly well mapped to the
original intent: "*The Contents section of a service metadata document
normally contains metadata about the data served by this server*" (OGC
06-121r9 section 7.4.8)

However, while this document does supply the necessary links to get to
the various products associated with the DAP service it does so at the
expense of changing the semantics of the components of the
OperationsMetadata part of the response. As far as I can tell in OGC
parlance each operation in this section would be bound to the request
parameter in the constraint expression. Consider this DescribeCoverage
request:

<font size="2">` http://test.opendap.org:8090/WCS?service=WCS&version=1.1.2&`**`request=DescribeCoverage`**`&identifiers=S1/opendap/ioos/200803061600_HFRadar_USEGC_6km_rtv_SIO.ncml `</font>

Obviously this interpretation of the semantics of the ows:Operation
elements doesn't fit with the intended semantics in our example.

Also, the OWS allows for the optional inclusion of any number of
ows:Parameter and ows:Constraint elements that can be used with each
operation. In the schema these parameter and constraint objects are both
of type ows:DomainType. The DomainType contains a description of a
domain using the following terms:

- PossibleValues
  - AllowedValues - expressed as a list of allowed values and/or
    range(s) of allowed values.
  - AnyValue - Any value is allowed
  - NoValues - no values allowed
  - ValuesReference - Reference to externally specified list of all the
    valid values and/or ranges of values for this quantity.
- Meaning - Some kind of annotation about what the "meaning" of this
  parameter/constraint expression is.
- DataType (where data type is expressed as a an "xlink:href attribute
  that references a URN for a well-known data type",
- ValuesUnit - The units of the values of this parameter/constraint
  expression expressed as an "xlink:href attribute that references a URN
  for a well-known unit of measure (uom).
- Metadata - where metadata is defined as either links to metadata
  documents or simply as text embedded in ows:Metadata elements.

I am not at all clear the distinction is between the ows:Parameter and
ows:Constraint. It is my thinking that each of them represent a possible
term in the query part of a URL. For exampleL *?p=43&z=foobar* could be
seen as an ows:Parameter with name "p" and an ows:Constraint with name
"z".

In DAP our constraint expressions (CEs) are fluid\*: Every dataset has
it's own set of left-operands (the names of the variables in the
dataset) and valid parameters for server side functions (the names of
the variables in the dataset).

The projection section of the DAP CE doesn't make use of an equals sign,
but instead uses a comma separated list of these things:

- some/all the names of the variables in the dataset which may be
  followed by array/sequence subset markup on some/all variables
- server side functions which may take variable names and other explicit
  values as parameters. The acceptable range of these explicitly valued
  parameters may be a function of some variables range, or not.

to make the projection.

Since the names, types, and ranges of variables typically change from
one dataset to another the acceptable values in the query part of the
request URL is different for every dataset.

After reading over the schema and the supporting documentation at OGC I
do not see how the syntax provided for parameters and constraints can be
used to express the details of the DAP Constraint Expression.

Where does this leave us? While many developers are already familiar
with the layout and content of the OWS GetCapabilities response, I think
that the semantics of it's components don't lend themselves to
describing the existing DAP REST service. We can make a valid document
using the OGC components and schemas that contains the information that
we want, but the meaning of the parts will fail to adhere to the
meanings assigned in the OGC schemas and documentation. I fear this
would probably cause more confusion, consternation, and gnashing of
teeth than Prototype \#1 where we simply created the minimum document
needed to achieve our goal for providing a service description that
itemizes all of the data products and services available for a
particular dataset.

[Jimg](User:Jimg "wikilink") 09:29, 31 January 2012 (PST) I agree.
GetCapabilities is not a good fit for this feature.

\*[Jimg](User:Jimg "wikilink") 20:57, 14 February 2012 (PST) I started
to write an explanation of the differences between our constraint
expressions and the WCS query string, but I think the most important
aspect is that the the two use a different syntax and the description of
that series of tokens (some of which are a function of the service and
some of which are a function of the data being served - in both cases )
is heavily tied to the syntax of the URL and it's Query String
component.

</font> </strike>

## Comments

[Jimg](User:Jimg "wikilink") 16:22, 14 November 2011 (PST)


We should think about making this a JSON response, or think about having
*<http://server/file.nc>* return the XML and
*<http://server/file.nc.json>* return JSON using an XSLT transform.

[Jimg](User:Jimg "wikilink") 16:22, 14 November 2011 (PST)


Nathan and I both conclude that the XML response should include the full
URL and not do some weird hack where URLs are 'built'.

<!-- -->


While this might be specific to HTTP and the web, I think we should
include in the design that a JSON version of this document will be
returned if the client announces that it understands such thing (the
default response will be XML with an embedded XSL stylesheet that
provides a transform to HTML). The HTTP/1.1 request and response would
look like:

<!-- -->

    GET / HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.example.link_templates+json

    HTTP/1.1 200 OK
    Content-Type: application/vnd.example.link_templates+json
    Cache-Control: max-age=3600
    Connection: close


Where the request header *Accept:* is the key bit. See the post Ethan
referenced for more information about this: [Web API Versioning
Smackdown](http://www.mnot.net/blog/2011/10/25/web_api_versioning_smackdown)

[Dennis](User:dmh "wikilink") 4/12/2012


I would like to propose an (possibly off-the-wall) alternate response
format based on treating the service terminus as a DAP4 dataset more or
less like any other dataset. This means that the response would be a DDX
plus content. The DDX would encode the capabilities of the server using
the DAP4 notions of group, variable, attribute, etc. The specific values
would be returned as the associated content. This would allow us to use
our (yet to be defined) query capability to extract specific capability
values. It would also (IMO) simplify programmatic access to the server
capability information.

[ndp](User:Ndp "wikilink") 20:17, 19 April 2012 (PDT)


I stood up a prototype of the Dataset Services (a.k.a. the Service
Terminus) on test.o.o. I quickly discovered the following mixed result:
Now that the web pages contain links to all of the services, the crawler
bots find them all!

<!-- -->


This is great news because, well, the services are exposed and our
experiment in improving our servers RESTfulness succeeded.

<!-- -->


This is bad news because the bots are asking for the ASCII, NetCDF file
out, and XML data responses for everything on the server. Which is
trashing it. By which I mean all of the servers memory and CPU resources
are being consumed by bots. The server logs are full of BES client
timeouts and broken pipes. Response times are in the gutter.

<!-- -->


This is an unintended consequence to our new Dataset Services response
and I think we need to carefully consider how we might remediate this
problem.

<!-- -->


On the one hand I think we want the catalog/web pages to be crawled and
indexed. On the other we really don't want bots asking for all of the
data in all of the ways it can be transmitted.

## Issues

- This a change in server behavior that will change the response content
  of file access URLs. These URLs are not part of the regular DAP URL
  pattern and their current behavior is a function of server
  configuration.
- How might we describe the way to elicit DAP3.x and DAP4 versions of
  the responses in this description given that we currently rely on the
  XDAP-Accept HTTP header to inform the server of the version being
  requested? [Jimg](User:Jimg "wikilink") 16:18, 14 November 2011 (PST)
  We can define a new set of extensions for these responses, especially
  since there are only two (ddx4 and dap4, e.g.). There are several
  other syntaxes that put this information in the URL, but if we use
  unique URLs to reference these new responses, then we don't break
  REST. This would mean that the XDAP-Accept: header would be dropped,
  but that's a separate discussion.
- [Jimg](User:Jimg "wikilink") 10:42, 26 January 2012 (PST) I am now
  leaning toward using *.ddx* and *.dap* to be the way that DAP4
  metadata and data are accessed, period. This means dropping the idea
  of version negotiation, which I think is a poor design for a web
  service. the extensions *.das*, *.dds* and *.dods* woud return the
  tried and true DAP2 response objects and any server hat support the
  five extensions clearly support DAP2 and DAP4. No client will every
  ask for a DAP2 DDX and be surprised by getting DAP4 (which it won't be
  able to parse) because to do so would be nonsensical (and anti-causal,
  sort of...). The one hitch with this idea is that there are some
  DAP2++ clients out there that gobble up DDX responses...
- *<font color="red">I know we discussed additional issues, but I can't
  recall them. Please add them if you do!</font>*

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")