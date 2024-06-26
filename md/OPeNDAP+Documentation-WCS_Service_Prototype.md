## Overview

This WCS service is an OLFS DispatchHandler implementation. The
implementation provides WCS services by separating the construction and
maintenance of the WCS catalog from the service activities themselves.

### The DispatchHandler

The WCS service prototype is written as a
[DispatchHandler](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration)
implementation for the OLFS (essentially a plug-in). As such it is added
to the OLFS via the
[olfs.xml](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File)
file. The WCS Service utilizes its own configuration directory and files
which are stored in a well known location within the OLFS content
directory: *\$CATALINA_HOME/content/opendap/**<prefix>***

### Catalog Implementations

One of the challenges encountered when implementing a WCS service for
Hyrax is catalog generation: *How do we generate a list of coverages
from existing holdings in Hyrax?* The DAP contains the mechanism for
transmitting the information, but lacks the explicit semantics required
to describe a WCS Coverage. Luckily many of the users of DAP servers
have provided metadata that complies to some type of metadata convention
or standard. This WCS service can build its catalog by using semantic
web technology to crosswalk from existing DAP data sets which use the
Climate Forecast (CF-1.0) metadata convention into the WCS metadata
space. It can also build a catalog from WCS document fragments stored on
the local disk.

#### [StaticRDFCatalog](StaticRDFCatalog "wikilink")

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

#### LocalFileCatalog

The LocalFileCatalog uses human written documents to build its catalog.
In this catalog every wcs:CoverageDescription is hand written and placed
in a file in a designated directory. The directory is identified in the
DispatchHandler's configuration object and at system start-up each file
in the Coverages directory is ingested (with the assumption that the
file contains a single wcs:CoverageDescription) and added to the
catalog.

Although there are many drawbacks to this approach it is something that
has a few positive qualities:

- It works.
- Although it's possible for a data provider to have large numbers of
  wcs:Coverages in their holdings, it is not practical since large
  numbers of wcs:Coverages will cause the wcs:Capabilities document to
  grow to such a size as to be indigestible by clients. Thus it seems
  that although it appears that this method is limited in its
  scalability, if a software process is developed to generate the
  wcs:CoverageDescriptions it would seem that long before the file
  system became clogged with wcs:CoverageDescription files the service
  itself would become too cumbersome for most clients.
- Many data centers with large numbers of small coverages may find that
  these coverages would be appropriately aggregated into a singe larger
  coverage. For many cases this is now possible with Hyrax, and it is
  accomplished using NcML configurations similar to the THREDDS Data
  Server.

## Requirements

------------------------------------------------------------------------

## Installation

### Install The Software

- Download and install the BES for Hyrax 1.6 including:
  - [libdap version
    x.x.x](http://www.opendap.org/download/libdap++.html)
  - [BES version x.x.x](http://www.opendap.org/download/BES.html)
  - [General Purpose Handlers version
    x.x.x](http://www.opendap.org/download/general_purpose_handlers.html)
  - [NetCDF Data Handler version
    x.x.x](http://www.opendap.org/download/nc_server.html)
  - [Fileout for NetCDF Handler version
    x.x.x](http://www.opendap.org/download/fileout_netcdf.html)
  - [NcML Handler version
    x.x.x](http://www.opendap.org/download/ncml_module.html)
- Verify that the BES is working correctly.
- [Download and install Tomcat
  6.x](http://tomcat.apache.org/download-60.cgi)
- Download the OLFS WCS Service WAR file, opendap.war
- Copy the opendap.war file to \$CATALINA_HOME/webapps
- Start Tomcat

At this point, assuming that the BES and the OLFS are running on their
default ports on the same system the Service should be running, although
in an un-configured state.

### Configure the software

The following changes will not take effect until Tomcat is restarted.

- Localize the WCS service metadata
  - [Edit \<font
    face="Courier\>\$CATALINA_HOME/content/opendap/<prefix>/ServiceProvider.xml</font>
    so that the specific information about the contact point, address
    and other information about the service reflect the needs of your
    organization.](#Service_Provider "wikilink")
  - [Edit \<font
    face="Courier\>\$CATALINA_HOME/content/opendap/<prefix>/ServiceIdentification.xml</font>
    so that the Title and Abstract sections reflect your needs. ***DO
    NOT alter the ServiceType or ServiceTypeVersion element
    content.***](#Service_Identification "wikilink")
  - [Edit \<font
    face="Courier\>\$CATALINA_HOME/content/opendap/<prefix>/OperationsMetadata.xml</font>
    so that the links to the service reflect the domain names and ports
    upon which you are running the
    service.](#Operations_Metadata "wikilink")
- [Configure the catalog for the WCS service.](#WCS_Catalogs "wikilink")
- Stop and restart Tomcat.

------------------------------------------------------------------------

## WCS Service Configuration

The WCS Service utilizes its own configuration directory and files which
are stored in a well known location within the OLFS content directory:

`$CATALINA_HOME/content/opendap/`**<prefix>**

where <font  face="Courier">**<prefix>**</font> is defined in the in the
WCS
[DispatchHandler](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration)
configuration element in the
[olfs.xml](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File)
file. In the WCS Service directory
(<font  face="Courier">CATALINA_HOME/content/opendap/<prefix></font>) is
kept various documents used by the WCS Service including:

wcs_service.xml
The <font face="Courier">wcs_service.xml</font> file identifies and
configures the WCS catalog implementation.

<!-- -->

[ServiceIdentification.xml](#Service_Identification "wikilink")
The <font face="Courier">ServiceIdentification.xml</font> file contains
WCS metadata used in the wcs:Capabilities document.

<!-- -->

[ServiceProvider.xml](#Service_Provider "wikilink")
The <font face="Courier">ServiceProvider.xml</font> file contains WCS
metadata used in the wcs:Capabilities document.

<!-- -->

[OperationsMetadata.xml](#Operations_Metadata "wikilink")
The <font face="Courier">OperationsMetadata.xml</font> file contains WCS
metadata used in the wcs:Capabilities document.

Every installation of a WCS service requires a localization step in
which much of the required metadata about the particular service
instance and its keepers is captured so that it can be returned to
clients.

From a client's perspective a WCS service is made up of three commands:

1.  wcs:GetCapabilities
2.  wcs:DescribeCoverage
3.  wcs;GetCoverage

The response for a wcs:GetCapaibilities request requires localization
information that can only be determined by the individual(s) that are
installing the service. The responses for both the wcs:DescribeCoverage
and wcs:GetCoverage requests can be generated directly from the WCS
"catalog".

The wcs:Capabilites document contains 4 sections:

- Service Identification (ows:ServiceIdentification)
- Service Provider (ows:ServiceProvider)
- Operations Metadata (ows:OperationsMetadata)
- Contents (wcs:Contents)

For any given installation of a WCS service the first three sections
must be carefully localized. Their contents cannot realistically be
ascertained by an automation. Thus, they are really part of the WCS
configuration activity that must be performed by the service installer.
The last section, Contents, can be automatically generated from the
catalog of Coverages.

In this WCS implementation the ows:ServiceIdentification,
ows:ServiceProvider and ows:OperationsMetadata sections are each stored
as a separate XML file that is loaded by the server at startup. Default
versions of these files are installed into the OLFS's persistent content
directory the first time that the WCS Service is launched. They can be
found in the directory:

`$CATALINA_HOME/content/opendap/`**<prefix>**

where <font  face="Courier">**<prefix>**</font> is defined in the in the
WCS
[DispatchHandler](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration)
configuration element in the
[olfs.xml](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File)
file.

### Service Identification

The Service Identification metadata as described in section 7.4.4 of OGC
document *[OGC
06-121r3](http://www.opengeospatial.org/standards/common)*.

Localization should include (but may not be limited to) updating the
ows:Keytwords, ows:Fees and ows:AccessConstraints

#### Example Document

    <?xml version="1.0" encoding="UTF-8"?>
    <!-- ************************************************************ -->
    <!-- * SERVICE IDENTIFICATION SECTION                           * -->
    <!-- ************************************************************ -->
    <ows:ServiceIdentification ns="http://www.opengis.net/wcs/1.1"
    xmlns:ows="http://www.opengis.net/ows/1.1"
    xmlns:xlink="http://www.w3.org/1999/xlink" >
      <ows:Title>OPeNDAP Hyrax Prototype WCS Service</ows:Title>
      <ows:Abstract>A prototype WCS server intended to provide WCS 1.1 (1.1.2) access to DAP datasets</ows:Abstract>
      <ows:Keywords>
         <ows:Keyword>Web Coverage Service</ows:Keyword>
         <ows:Keyword>OPeNDAP</ows:Keyword>
         <ows:Keyword>nectcdf</ows:Keyword>
         <ows:Keyword>DAP</ows:Keyword>
      </ows:Keywords>
      <ows:ServiceType>WCS</ows:ServiceType>
      <ows:ServiceTypeVersion>1.1.2</ows:ServiceTypeVersion>
      <ows:Fees>NONE</ows:Fees>
      <ows:AccessConstraints>NONE</ows:AccessConstraints>
    </ows:ServiceIdentification>

File Location
<font  face="Courier">\$CATALINA_HOME/content/opendap/**<prefix>**/ServiceIdentification.xml</font>
where <font  face="Courier">**<prefix>**</font> is defined in the in the
WCS
[DispatchHandler](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration)
configuration element in the
[olfs.xml](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File)
file.

### Service Provider

The Service Provider metadata as described in section 7.4.5 of OGC
document *[OGC
06-121r3](http://www.opengeospatial.org/standards/common)*.

The ows:ServiceProvider section should be localized to identify the
local operator of the system. Typically this would be the name and
contact information of the individual or group responsible for
installing and maintaing the WCS service.

#### Example Document

    <?xml version="1.0" encoding="UTF-8"?>
    <!-- ************************************************************ -->
    <!-- * SERVICE PROVIDER SECTION                                 * -->
    <!-- ************************************************************ -->
    <ows:ServiceProvider  ns="http://www.opengis.net/wcs/1.1"
    xmlns:ows="http://www.opengis.net/ows/1.1"
    xmlns:xlink="http://www.w3.org/1999/xlink">
      <ows:ProviderName>OPeNDAP Inc.</ows:ProviderName>
      <ows:ProviderSite xlink:href="http://www.opendap.org"/>
      <ows:ServiceContact>
         <ows:IndividualName>Nathan D. Potter</ows:IndividualName>
         <ows:PositionName>Senior Software Engineer</ows:PositionName>
         <ows:ContactInfo>
            <ows:Phone>
               <ows:Voice>123-456-7890</ows:Voice>
               <ows:Facsimile>234-567-8901</ows:Facsimile>
            </ows:Phone>
            <ows:Address>
               <ows:DeliveryPoint>165 Dean Knauss Dr</ows:DeliveryPoint>
               <ows:City>Narragansett</ows:City>
               <ows:AdministrativeArea>Rhode Island</ows:AdministrativeArea>
               <ows:PostalCode>02882</ows:PostalCode>
               <ows:Country>USA</ows:Country>
               <ows:ElectronicMailAddress>support[a.t]opendap[d.o.t]org</ows:ElectronicMailAddress>
            </ows:Address>
            <ows:OnlineResource xlink:href="http://ndp.opendap.org:8080/opendap/"/>
            <ows:HoursOfService>24x7x365</ows:HoursOfService>
            <ows:ContactInstructions>email</ows:ContactInstructions>
         </ows:ContactInfo>
         <ows:Role>Developer</ows:Role>
      </ows:ServiceContact>
    </ows:ServiceProvider>

File Location
<font  face="Courier">\$CATALINA_HOME/content/opendap/**<prefix>**/ServiceProvider.xml</font>
where <font  face="Courier">**<prefix>**</font> is defined in the in the
WCS
[DispatchHandler](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration)
configuration element in the
[olfs.xml](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File)
file.

### Operations Metadata

The Operations Metadata as described in section 7.4.6 of OGC document
*[OGC 06-121r3](http://www.opengeospatial.org/standards/common)*.

Careful localization of this information is crucial to the correct
function of the WCS service. Many clients will use this section of the
wcs:Capabilities document to ascertain where and how they can interact
with the service. It is required that the Get and Post URL's for each
WCS operation point to the correct terminus on your server. If the
server is behind a firewall which is port-forwarding the incoming
requests the URL's may have little to do with the local network
configuration.

<hr>

<font color="grey"> Looking at this again I am wondering if this could
be either configured or determined?


The base URL for their system could be added to the DispatchHandler
configuration and the service URL's generated from that.

OR


It is the case that the servlet request contains the URL to be
dereferenced. I think it might work to dynamically determine the URL
that brought the GetCapabilities request to the service and to pluck it
from the request and build the service URLs from it.

Either one of these would diminish the configuration burden presented to
the Hyrax user.

Comments?

[jimg](User:Jimg "wikilink") 10:54, 15 April 2009 (PDT)

What about the case you mention above where a firewall and
port-forwarding make the external and internal URLs structurally
different?

[ndp](User:Ndp "wikilink") 19:57, 14 April 2009 (PDT)

Also - the URLs for the POST and SOAP interfaces are defined in the
olfs.xml file, but not in the part of the configuration that is used by
the promary WCS DispatchHandler. Determining the values of each ones
**<prefix>** element will require some architectural redesign...

--[ndp](User:Ndp "wikilink") 07:28, 13 January 2010 (PST)

</font>

#### Example Document

    <?xml version="1.0" encoding="UTF-8"?>
    <!-- ************************************************************ -->
    <!-- * OPERATIONS METADATA                                      * -->
    <!-- ************************************************************ -->
    <ows:OperationsMetadata ns="http://www.opengis.net/wcs/1.1"
                            xmlns:ows="http://www.opengis.net/ows/1.1"
                            xmlns:xlink="http://www.w3.org/1999/xlink">
        <ows:Operation name="GetCapabilities">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://ndp.opendap.org:8080/opendap/WCS?"/>
                    <ows:Post xlink:href="http://ndp.opendap.org:8080/opendap/WCS/post">
                        <ows:Constraint name="PostEncoding">
                            <ows:AllowedValues>
                                <ows:Value>XML</ows:Value>
                            </ows:AllowedValues>
                        </ows:Constraint>
                    </ows:Post>
                    <ows:Post xlink:href="http://ndp.opendap.org:8080/opendap/WCS/soap">
                        <ows:Constraint name="PostEncoding">
                            <ows:AllowedValues>
                                <ows:Value>SOAP</ows:Value>
                            </ows:AllowedValues>
                        </ows:Constraint>
                    </ows:Post>
                </ows:HTTP>
            </ows:DCP>
        </ows:Operation>
        <ows:Operation name="GetCoverage">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://ndp.opendap.org:8080/opendap/WCS?"/>
                    <ows:Post xlink:href="http://ndp.opendap.org:8080/opendap/WCS/post">
                        <ows:Constraint name="PostEncoding">
                            <ows:AllowedValues>
                                <ows:Value>XML</ows:Value>
                            </ows:AllowedValues>
                        </ows:Constraint>
                    </ows:Post>
                    <ows:Post xlink:href="http://ndp.opendap.org:8080/opendap/WCS/soap">
                        <ows:Constraint name="PostEncoding">
                            <ows:AllowedValues>
                                <ows:Value>SOAP</ows:Value>
                            </ows:AllowedValues>
                        </ows:Constraint>
                    </ows:Post>
                </ows:HTTP>
            </ows:DCP>
            <ows:Parameter name="Format">
                <ows:AllowedValues>
                    <ows:Value>application/x-netcdf-cf1.0</ows:Value>
                    <ows:Value>application/x-dap2.0</ows:Value>
                </ows:AllowedValues>
            </ows:Parameter>
        </ows:Operation>
        <ows:Operation name="DescribeCoverage">
            <ows:DCP>
                <ows:HTTP>
                    <ows:Get xlink:href="http://ndp.opendap.org:8080/opendap/WCS?"/>
                    <ows:Post xlink:href="http://ndp.opendap.org:8080/opendap/WCS/post">
                        <ows:Constraint name="PostEncoding">
                            <ows:AllowedValues>
                                <ows:Value>XML</ows:Value>
                            </ows:AllowedValues>
                        </ows:Constraint>
                    </ows:Post>
                    <ows:Post xlink:href="http://ndp.opendap.org:8080/opendap/WCS/soap">
                        <ows:Constraint name="PostEncoding">
                            <ows:AllowedValues>
                                <ows:Value>SOAP</ows:Value>
                            </ows:AllowedValues>
                        </ows:Constraint>
                    </ows:Post>
                </ows:HTTP>
            </ows:DCP>
            <ows:Parameter name="Format">
                <ows:AllowedValues>
                    <ows:Value>text/xml</ows:Value>
                </ows:AllowedValues>
            </ows:Parameter>
        </ows:Operation>
    </ows:OperationsMetadata>

File Location
<font  face="Courier">\$CATALINA_HOME/content/opendap/**<prefix>**/OperationsMeadata.xml</font>
where <font  face="Courier">**<prefix>**</font> is defined in the in the
WCS
[DispatchHandler](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration)
configuration element in the
[olfs.xml](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File)
file.

### WCS Catalogs

The WCS service must maintain a catalog of *Coverages*. In the Hyrax WCS
service the implementation of this catalog is identified at server
start-up from the *wcs_service.xml* file located here:
<font  face="Courier">\$CATALINA_HOME/content/opendap/**<prefix>**/wcs_service.xml</font>
where <font  face="Courier">**<prefix>**</font> is defined in the in the
WCS
[DispatchHandler](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration)
configuration element in the
[olfs.xml](http://docs.opendap.org/index.php/Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File)
file.

#### [StaticRDFCatalog](StaticRDFCatalog "wikilink") Configuration

[StaticRDFCatalog configuration instructions can be found
here](StaticRDFCatalog "wikilink")

#### [LocalFileCatalog](LocalFileCatalog "wikilink") Configuration

*This is a development tool and is not expected to be used by data
providers.*

[LocalFileCatalog configuration instructions can be found
here](LocalFileCatalog "wikilink")

## WCS DispatchHandler Configuration

The Hyrax WCS service is made of a collection 4 implementations of the
opendap.coreServlet.DispatchHandler interface.

- HTTP GET: opendap.wcs.v1_1_2.DispatchHandler
- HTTP POST (For posting unadorned XML WCS requests):
  opendap.wcs.v1_1_2.PostHandler
- HTTP POST (For posting SOAP envelopes containg WCS requests):
  opendap.wcs.v1_1_2.SoapHandler
- HTTP POST (For handling WCS requests posted from an HTML form):
  opendap.wcs.v1_1_2.FormHandler

The service is enabled by creating an entry for each service component
in the olfs.xml file.

The primary DispatchHandler is opendap.wcs.v1_1_2.DispatchHandler. It is
required for the WCS service and its Handler declaration contains the
configuration information for the service. In its Handler declaration
the location of the ServiceIdentification, ServiceProvider, and
OperationsMetadata documents are identified, along with a WCS catalog
implementation to be used by the service.

A WCS catalog implementation (and its configuration) must be provided in
the WcsCatalog element.

To "turn on" the service Handler declarations must appear in the
olfs.xml opendap.wcs.v1_1_2.DispatchHandler Configuration

### Example olfs.xml

<?xml version="1.0" encoding="UTF-8"?>

` `<OLFSConfig>
`   `<DispatchHandlers>
`       `<HttpGetHandlers>
`           `<Handler className="opendap.bes.BESManager">
`               `<BES>
`                   `
`                   `<prefix>`/`</prefix>
`                   `
`                   `<host>`localhost`</host>
`                   `
`                   `<port>`10022`</port>
`                   `
`                   `<ClientPool maximum="10" />
`               `</BES>
`           `</Handler>

<b>
`           `<Handler className="opendap.wcs.v1_1_2.DispatchHandler">
`               `<prefix>`WCS`</prefix>
`           `</Handler>
` `</b>

`           `<Handler className="opendap.threddsHandler.StaticCatalogDispatch">
`               `<prefix>`thredds`</prefix>
`               `<useMemoryCache>`true`</useMemoryCache>
`           `</Handler>
`           `<Handler className="opendap.bes.DapDispatchHandler" />
`           `<Handler className="opendap.bes.DirectoryDispatchHandler" />
`           `<Handler className="opendap.coreServlet.SpecialRequestDispatchHandler" />
`           `<Handler className="opendap.bes.VersionDispatchHandler" />
`           `<Handler className="opendap.bes.FileDispatchHandler" >
`               `
`               `<AllowDirectDataSourceAccess />
`           `</Handler>
`           `<Handler className="opendap.bes.BESThreddsDispatchHandler" />
`       `</HttpGetHandlers>

`       `<HttpPostHandlers>
<b>
`           `<Handler className="opendap.wcs.v1_1_2.PostHandler" >
`               `<prefix>`WCS/post`</prefix>
`           `</Handler>
`           `<Handler className="opendap.wcs.v1_1_2.SoapHandler" >
`               `<prefix>`WCS/soap`</prefix>
`           `</Handler>
`           `<Handler className="opendap.wcs.v1_1_2.FormHandler" >
`               `<prefix>`WCS/form`</prefix>
`           `</Handler>
</b>` `

`           `<Handler className="opendap.coreServlet.SOAPRequestDispatcher" >
`               `<OpendapSoapDispatchHandler>`opendap.bes.SoapDispatchHandler`</OpendapSoapDispatchHandler>
`           `</Handler>
`       `</HttpPostHandlers>
`   `</DispatchHandlers>
</OLFSConfig>

[Category:WCS](Category:WCS "wikilink")