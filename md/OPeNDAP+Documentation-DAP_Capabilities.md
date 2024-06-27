## Overview

Develop a definition of what a DAP servers Capabilities response should
look like.

## What is a Capabilities response?

In the world of OGC it's a document that contains a description of the

ownership
(a.k.a ows:ServiceProvider) Describes contact information and for both
an individual and their parent organization for a specific server
instance.

service identification
(a.k.a. ows:ServiceIdentification) Provides a collection of keyword
metadata and and the name and version number of the service.

service access
(a.k.a. ows:OperationsMetadata) Provides a set of service URLs for the
OGC service. Since OGC services (all?? I'm not sure) rely on the
constraint expression syntax of the URL or POST content to identify and
select data sets, the service components are easily tied to single URLs
that identify the place in the URL space that the server will behave a s
an OGC type service.

holdings
(a.k.a. \*:Contents) Provides a catalog, generally flat, but even when
hierarchical must provided in it's entirety. No sub-setting or linking
of catalog "nodes" that require making additional queries. This provides
the convenience of one stop shopping with the disadvantage of the
potential for clients to be overwhelmed by the size of the response.

## What are we doing now that's similar?

- **Ownership** Nothing similar. Data providers can (but none that I
  have seen have) configure the OLFS to replace the standard
  documentation found through the Documentation link on every
  contents.html page. Otherwise they might get an email address about
  weho to write of the BES sends one in an error. The OLFS saves
  propagates the email address *support@opendap.org* from 14 different
  places in the code through a number of HTML responses . These could be
  modified by determined Hyrax installer, but it would be a hassle and
  wouldn't persist between installs. We have no structured response in
  this regard.
- **Service Identification** Nothing similar. In the current version of
  Hyrax we return an list of the DAP versions the server supports in a
  larger XML document containing the names and version numbers of the
  software components running on the server.
- **Service Access** Nothing similar
- **Holdings** Currently we provide a linked list of holdings through
  THREDDS catalogs. Our THREDDS catalogs do not contain any THREDDS
  metadata, (or WCS metadata for that matter)

## How might a DAP Capabilities response look

Here are some ideas for components of the response:

Just to start things off, the root element:

<dap:Capabilities>

### Ownership

`   `<dap:Contact>
`       `<dap:Email>`name@org.org`</dap:Email>
`   <dap:/Contact>`

### Holdings/Catalog

Probably this would always be a child element of a service declaration.

`         `<dap:CatalogRoot xlink:href="/opendap/catalog.xml"  xlink:type="simple">

What catalog metadata should we provide so that a complete enough
picture of the holdings is available? The intent it to make it so that
users don't need to delve into the granules to determine if the holding
contains the information that they desire. See THREDDS Catalog Metadata
section below

### Services

`   `<dap:Services>
`   `
`       `<dap:Service name="hyrax" base="/opendap/hyrax/" >
`         `<dap:ServiceType>`DAP`</dap:ServiceType>
`         `<dap:Version>`2.0`</dap:Version>
`         `<dap:Version>`3.1`</dap:Version>
`         `<dap:Version>`3.2`</dap:Version>
`         `<dap:CatalogRoot xlink:href="/opendap/catalog.xml" xlink:type="simple" />
`         `<dap:ReturnFormat>`DAP`</dap:ReturnFormat>
`         `<dap:ReturnFormat>`NetCDF`</dap:ReturnFormat>
`       `<dap:Service>
`       `
`       `<dap:Service name="hyrax-WCS" base="/opendap/WCS/" >
`         `<dap:ServiceType>`WCS`</dap:ServiceType>
`         `<dap:Version>`1.1.2`</dap:Version>
`         `<dap:CatalogRoot
              xlink:href="/opendap/WCS?service=WCS&version=1.1.2&request=GetCapabilities" >
`             xlink:type="simple" />`
`       `<dap:Service>
`       `
`   `</dap:Services>

#### Return Types/Formats

How do we advertise the different return types/formats available from
the Server?

Does each service definition announce a list of return types?

### Server-side functions.

We need to develop XML machine readable description of server side
functions.

Here's a couple of straw men for geogrid, grid, linear_scale, and
version:

With properties as elements:

`   `<dap:ServerSideFunctions>
`       `<function>
`           `<name>`grid`</name>
`           `<version>`1.0`</version>
`           `<documentation
                xlink:href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#grid"
                xlink:type="simple" />
`       `<function>

`       `<function>
`           `<name>`geogrid`</name>
`           `<version>`1.1`</version>
`           `<documentation
                xlink:href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#grid"
                xlink:type="simple" />
`       `<function>

`       `<function>
`           `<name>`linear_scale`</name>
`           `<version>`1.0b1`</version>
`           `<documentation
                xlink:href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#grid"
                xlink:type="simple" />
`       `<function>

`       `<function>
`           `<name>`version`</name>
`           `<version>`1.0`</version>
`           `<documentation
                xlink:href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#grid"
                xlink:type="simple" />
`       `<function>
`   `</dap:ServerSideFunctions>

With properties as attributes

`   `<dap:ServerSideFunctions>
`       `<function
            name="grid"
            version="1.0"
            documentation="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#grid" >
`       `<function
            name="geogrid"
            version="1.1"
            documentation="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#geogrid"/>
`       `<function
            name="linear_scale"
            version="1.0b1"
            documentation="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#linear_scale"/>
`       `<function
            name="version"
            version="1.0"
            documentation="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#version"/>
`   `</dap:ServerSideFunctions>

</dap:Capabilities>