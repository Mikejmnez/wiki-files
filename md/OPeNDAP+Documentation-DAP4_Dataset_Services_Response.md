[\<-- back to OPULS Development](OPULS_Development "wikilink")

Author: [ndp](User:Ndp "wikilink")

## Background

Legacy DAP2 servers provide a number of dataset level responses
(services). These include DAP2 services such as the DDS, DAS, and DDX,
plus other services that have been added over time such as the RDF and
ISO responses. There is no method defined by the DAP2 protocol for a
user (or software agent) to discover which of these many services might
be available for a particular dataset. While THREDDS catalogs do provide
a mechanism for determining which services are associated with which
datasets, they don't actually provide a mechanism for discovering the
details of the various service URLs. A THREDDS catalog might identify a
DAP2 service, but the URL that can be assembled from the THREDDS catalog
for that service is limited to just the base dataset URL. Typically
dereferencing this URL will provide one of two things - either the
underlying data file (for example a netcdf file) or an HTTP 403 error if
direct access to the underlying files has been disabled in the server
configuration.

**Example:**

This URL points to a dataset on a DAP2 server: <font size="2">

[`http://test.opendap.org:8080/opendap/data/nc/fnoc1.nc`](http://test.opendap.org:8080/opendap/data/nc/fnoc1.nc)

</font>

Dereferencing the link will not connect you to a DAP2 service or DAP2
response. It will (likely) return the underlying netcdf file. The only
way to know that DAP2 services exist for this is by reading the URL path
and recognizing that it is probably an instance of a DAP2 server based
on the composition of the URL. In fact, there is no guarantee that
dereferencing this URL will even yield the underlying file, as that
behavior is a configuration option for some server implementations. If
that access is not allowed in the configuration, then dereferencing the
URL will simply return an HTTP 403 (forbidden) error.

## Dataset Services Response

The Dataset Services Response provides a 'Services' or 'Capabilities'
response for DAP4. Dereferencing an unadorned DAP4 dataset resource URL
will return a document describing the DAP services available for the
dataset. This is a REQUIRED response and the only valid response for an
'unadorned' dataset resource URL. We will refer to this response as the
'Dataset Services Response' or 'DSR' in the 'DAP4 Web Services'
specification and the remainder of this document.

At minimum the DSR document MUST provide, for each service available for
the dataset:

- A human readable title of that service (e.g., The Dataset Metadata
  Response service for the dataset)
- One or more links that can be dereferenced to get the various
  representations of the response for the dataset.
- An unambiguous, unique to the service type, resource role ID that
  serves as a way to clearly identify what each service does. (Note:
  *Resource roles for all DAP4 services are defined, as well as for
  several other legacy DAP2 related services in the 'DAP4 Specification,
  Volume 2 Web Services'. Defining a resource role for a new service
  will be the responsibility of the service creator.*)
- A brief description of the service

The response should also include:

- A link to a complete, human readable, description of the service.
- A reference to an XSLT that a browser would use to render the
  description into HTML for a presentation view.
- Descriptions and syntax of server side functions available for the
  dataset

### Service Access

A service is accessed by dereferencing one of the access URLs held in
the <font size="2">**`href`**</font> attribute of a
<font size="2">**<dsr:link>**</font> elements, which is typically
constructed by adding some type of suffix to the dataset's referent (aka
base) URL. The way in which the query string (aka constraint expression)
is used is defined by each service, and there is no requirement for
inter-service query string API conformity.

A more extensive discussion of the various services that might appear in
a DatasetServices document can be found in [DAP4 Volume 2, Web
Services](DAP4:_Specification_Volume_2 "wikilink").

**Note:** If a client understands how to modify/augment the dataset
resource URL (as described in the [DAP4 Volume 2, Web
Services](DAP4:_Specification_Volume_2 "wikilink") ) such that it
becomes a DAP response URL then it need never obtain the Dataset
Services response.

### Dataset Services Response Encoding

c.f. [DAP4 Volume 2, Web
Services](DAP4:_Specification_Volume_2 "wikilink")

*In this section the namespace prefix <font size="2">**`dsr`**</font> is
associated with the namespace
**<font size="2">`http://xml.opendap.org/ns/DAP/4.0/dataset-services#`</font>**.*

#### dsr:DatasetServices element

The Dataset Services Response MUST contain a top level
<font size="2">**<dsr:DatasetServices>**</font> element. This
<font size="2">**<dsr:DatasetServices>**</font> element MUST contain:

- An <font size="2">**`xml:base`**</font> attribute whose value is the
  resource URL that was dereferenced to request the DSR response.
- A list of DAP versions supported by server

  These appear as one or more child
  <font size="2">**<dsr:DapVersion>**</font> elements of the
  <font size="2">**<dsr:DatasetServices>**</font> element.
- The implementation version of the server that produced the DSR.

  This appears as a single
  <font size="2">**<dsr:ServerSoftwareVersion>**</font> child element of
  the <font size="2">**<dsr:DatasetServices>**</font> element.
- A list of all available DAP4 services for the dataset

  This is represented as 3 or more
  <font size="2">**[<dsr:Service>](#Service_Element "wikilink")**</font>
  elements. (There are 3 required services for a DAP4 server.)
- A list of supported extensions
  - Resource type extensions
  - Media type extensions
  - Server-side function extensions

The <font size="2">**<dsr:DatasetServices>**</font> element SHOULD
contain:

- A <font size="2">**`title`**</font> attribute whose value is a human
  readable title for the dataset.

The <font size="2">**<dsr:DatasetServices>**</font> element MAY contain:

- A <font size="2">**<dsr:Description>**</font> element whose value is a
  human readable description of the dataset.

#### dsr:DapVersion Element

The <font size="2">**<dsr:DapVersion>**</font> element contains a single
DAP version value as a text string. The DAP version is represented as
two integer values separated by a period, MM.mm where the first value
(MM) is the major version of the DAP protocol, and the second value (mm)
is the minor version. Multiple
<font size="2">**<dsr:DapVersion>**</font> elements are used to
represent that server can support multiple versions of the DAP protocol,
one instance for each supported version.

Example: <font size="2">

``` xml
<DapVersion>4.0</DapVersion>
<DapVersion>3.2</DapVersion>
<DapVersion>2.0</DapVersion>
```

</font>

#### dsr:ServerSoftwareVersion element

The <font size="2">**<dsr:ServerSoftwareVersion>**</font> element has no
attributes and may contain as little as a simple text string (e.g., "TDS
4.3.57" or "Hyrax 1.7.45"), or it may contain any well-formed XML
content not in the <font size="2">**`dsr`**</font> namespace that the
server implementer sees fit to use to describe the software version of
their server implementation.

#### dsr:Service element

Each DAP4 web service is described by a
<font size="2">**<dsr:Service>**</font> element with:

- An optional <font size="2">**`title`**</font> attribute whose value is
  a simple human readable title for the service.
- An required <font size="2">**`role`**</font> attribute whose value is
  a unique to the service type, resource role ID (a URI) that serves as
  a way to clearly identify what each service does. This is intended to
  allow people and software to unambiguously identify specific services,
  irrespective of their human readable title. The idea is that that the
  role attribute tells you what is going to happen when you dereference
  the URL held in the <font size="2">**<dsr:link>**</font> element. Each
  service has a single role and all of the links (alternate
  representations) must fulfill the same role.
- An optional <font size="2">**<dsr:Description>**</font> element .
- One or more <font size="2">**<dsr:link>**</font> elements.

##### Regarding the <font size="3">**`role`**</font> attribute

With regards to multiple representations of a service response and the
<font size="2">**`role`**</font> attribute: While there may be many
alternate representations of a response, not all will fufill the same
<font size="2">**`role`**</font>. Representations fulfilling the same
<font size="2">**`role`**</font> should be bundled together in their own
**Service** with the appropriate <font size="2">**`role`**</font> value.
For example the DMR can be mapped to the ISO19115 metadata space, but
IS0-19115 responses are clearly outside of the
<font size="2">**`role`**</font> of the regular DMR service, which
returns a DAP4 metadata response. One could view it as the two roles
described different domains.

#### dsr:Description element

Each <font size="2">**<dsr:Description>**</font> element MUST contain:

- An optional <font size="2">**`href`**</font> attribute whose value is
  a URL string that points to a human readable document describing the
  service.
- The required text content of the
  <font size="2">**`dsr:Description`**</font> element MUST be a human
  readable description of the service.

#### dsr:link element

Each <font size="2">**<dsr:link>**</font> element MUST contain:

- A required <font size="2">**`type`**</font> attribute whose value is
  the media-type for the normative representation returned by that URL
  held in the <font size="2">**`href`**</font> attribute.
- A required attribute called <font size="2">**`href`**</font> whose
  value is a URL that when dereferenced will return the service response
  in the media type described by the value of the
  <font size="2">**`type`**</font> attribute.
- An optional attribute called <font size="2">**`description`**</font>
  whose value is a human readable string that provides a brief
  description of the <font size="2">**<dsr:link>**</font> elements
  semantics.
- Zero or more <font size="2">**<dsr:alt>**</font> elements, one for
  each and every alternate representation of the response that can be
  requested using [HTTP server-driven content
  negotiation](http://www.w3.org/Protocols/rfc2616/rfc2616-sec12.html#sec12.1)
  in conjunction with the <font size="2">**`href`**</font> URL. Each
  <font size="2">**<dsr:alt>**</font> element MUST contain:
  - A required <font size="2">**`type`**</font> attribute whose value is
    the MIME type that would be used in the [HTTP **Accept**
    header](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.1)
    to request that representation from the server.

### Extensions

Server extensions are simple descriptions of server behaviors that fall
outside of the services descriptions provided so far. There are three
types of extensions: <font size="2">**`function`**</font>,
<font size="2">**`functionGroup`**</font>, and
<font size="2">**`extension`**</font>.

A linear regression, a re-gridding operation, and a coordinate
re-projection are all examples of a
<font size="2">**`function`**</font>. A collection of functions that
allows the user to perform geocentric manipulations of the data (such as
re-gridding operations, reprojection operations) taken together could
define a <font size="2">**`functionGroup`**</font>. An
<font size="2">**`extension`**</font> is a server behavior that does not
operate directly on the data values, but may provide other types of
services and functionality for the client.

It is beyond the scope of this specification to attempt to describe the
syntax of usage of a <font size="2">**`function`**</font> in some
general way. Rather we have chosen to provide a simple mechanism (the
<font size="2">**`role`**</font> attribute) to alert client software
implementations that the server supports specific additional behaviors
they can utilize, along with more descriptive elements through which
humans can be alerted to the presence of server features and where to
find out more about them.

#### dsr:function

A function extension always describes an operation that applies to the
Data Response and the Dataset Metadata Response (because some functions
might alter the syntacic structure of the data the change would be
previewed by applying the function to the DMR).

Each <font size="2">**<dsr:function>**</font> MUST contain:

- A <font size="2">**`name`**</font> attribute whose value is the name
  of the function as it would be used in a DR or DMR request.
- A <font size="2">**`role`**</font> attribute whose value is a URI that
  universally defines the function.
- A <font size="2">**<dsr:Description>**</font> element whose value is a
  human readable description of the function and a link to detailed
  documentation of the function and its usage.

A common example of a function would be the DAP2 (and DAP4) geogrid
function that allows users to be sub-sampled gridded data using
georeferenced values.

#### dsr:functionGroup

A function group identifies that the server supports a group of
functions. Rather than enumerating a large list of related functions,
the functionGroup provides the user with the information that the server
supports a collection of related functions.

Each <font size="2">**<dsr:functionGroup>**</font> MUST contain:

- A <font size="2">**`name`**</font> attribute whose value is the human
  readable name of the collection of functions.
- A <font size="2">**`role`**</font> attribute whose value is a URI that
  universally defines the function group.
- A <font size="2">**<dsr:Description>**</font> element whose value is a
  human readable description of the function group and a link to
  detailed documentation regarding the functions in the function group
  and their usage.

An example of a function group would be a server that implemented a
functional syntax for all of the functions found in the
[Ferret](http://ferret.wrc.noaa.gov/Ferret/) application.

#### dsr:extension

An extension is a mechanism for indicating that the server supports a
certain behavior. This behavior MUST NOT be an operation/computation on
the data (other wise it would be a
<font size="2">**`dsr:function`**</font>or a
<font size="2">**`dsr:functionGroup`**</font>), but rather some other
behavior that the server can undertake.

Each <font size="2">**<dsr:extension>**</font> MUST contain:

- A <font size="2">**`name`**</font> attribute whose value is the human
  readable name of the extension.
- A <font size="2">**`role`**</font> attribute whose value is a URI that
  universally defines the extension.
- A <font size="2">**<dsr:Description>**</font> element whose value is a
  human readable description of the extension and a link to detailed
  documentation regarding the extension and it's behavior and usage.

An example of an extension would be the DAP4 asynchronous response
support. Some servers will support this functionality, and the
functionality is not defined as an operation on the data itself, but in
terms of the transaction procedures for accessing the data.

### Example: A Minimal DSR

This example contains only the required components of the DSR document
from a minimal DAP4 server implementation.

<font size="2">

``` xml

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<DatasetServices ns="http://xml.opendap.org/ns/DAP/4.0/dataset-services#"
    xml:base="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml">

    <DapVersion>4.0</DapVersion>

    <ServerSoftwareVersion>Hyrax-2.7.9</ServerSoftwareVersion>

    <Service title="DAP4 Dataset Services" role="http://services.opendap.org/dap4/dataset-services">
        <link type="application/vnd.opendap.org.dataset-services+xml"
              href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml">
              <alt type="text/xml"/>
        </link>
        <link type="text/xml"  href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.xml"/>
    </Service>

    <Service title="DAP4 Dataset Metadata"  role="http://services.opendap.org/dap4/dataset-metadata">
        <link type="application/vnd.org.opendap.dap4.dataset-metadata+xml"
              href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.dmr">
              <alt type="text/xml"/>
        </link>
        <link type="text/xml" href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.dmr.xml"/>
    </Service>

    <Service title="DAP4 Data" role="http://services.opendap.org/dap4/data">
        <link type="application/vnd.org.opendap.dap4.data" href="http://test.opendap.org:8090/opendap/hyrax/ECMWF_ERA-40_subset.ncml.dap" />
    </Service>


</DatasetServices>
```

</font>

### Example: A fully featured and annotated DSR document

This example contains all of the optional components for a hypothetical
DAP4 server that also supports DAp2 requests, server side functions, and
asynchronous transactions.

<font size="2">

``` xml

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<?xml-stylesheet type="text/xsl" href="/opendap/xsl/serviceDescription.xsl"?>
<DatasetServices ns="http://xml.opendap.org/ns/DAP/4.0/dataset-services#"
                 xml:base="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc">

    <Description>Our friend fnoc1.nc</Description>

    <!-- ##################################################################################### -->
    <!--       DAP versions this server supports                                               -->
    <!-- ..................................................................................... -->
    <DapVersion>4.0</DapVersion>
    <DapVersion>3.2</DapVersion>
    <DapVersion>2.0</DapVersion>

    <!-- ##################################################################################### -->
    <!--  The software version of the server that is providing the DAP4 Dataset Services       -->
    <!--   response.                                                                           -->
    <!-- The ServerSoftwareVersion element may contain text, or any XML element content as     -->
    <!-- long as the XML is not in the document namespace                                      -->
    <!-- ..................................................................................... -->
    <ServerSoftwareVersion>Hyrax-2.7.9</ServerSoftwareVersion>


    <!-- ##################################################################################### -->
    <!--       Required DAP4 Services                                                          -->
    <!-- ..................................................................................... -->

    <Service title="DAP4 Dataset Services" role="http://services.opendap.org/dap4/dataset-services">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Dataset_Services_Description_Service">An index of the Services available for this data resource.</Description>

        <link description="Normative form of the DSR"
              type="application/vnd.opendap.org.dataset-services+xml"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc">
              <alt type="text/html"/>
              <alt type="text/xml"/>
        </link>

        <link description="HTML representation of the DSR"
              type="text/html"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.html"/>

        <link description="Normative DSR with generic Content-Type"
              type="text/xml"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.xml"/>

    </Service>

    <Service title="DAP4 Dataset Metadata"  role="http://services.opendap.org/dap4/dataset-metadata">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Dataset_Service_-_The_metadata">The DAP4 metadata content for this data resource..</Description>

        <link description="Normative form of the DMR"
              type="application/vnd.org.opendap.dap4.dataset-metadata+xml"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dmr">
              <alt type="text/html"/>
              <alt type="text/xml"/>
              <alt type="application/rdf+xml"/>
        </link>

        <link description="Data Request Form"
              type="text/html"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dmr.html"/>

        <link description="Normative DMR with generic Content-Type"
              type="text/xml"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dmr.xml"/>

        <link description="RDF representation of DMR"
              type="application/rdf+xml"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dmr.rdf"/>
    </Service>

    <Service title="DAP4 Data" role="http://services.opendap.org/dap4/data">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Data_Service">DAP4 Data object for this data resource.</Description>
        <link description="The normative form of the DAP4 Data Response"
              type="application/vnd.org.opendap.dap4.data"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dap" >
              <alt type="text/plain"/>
              <alt type="text/xml"/>
              <alt type="application/x-netcdf"/>
        </link>

        <link description="A comma separated values (CSV) representation of the DAP4 Data Response object."
              type="text/plain"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dap.ascii" />
        <link description="XML representation of the DAP4 Data Response object."
              type="text/xml"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dap.xml" />
        <link description="NetCDF-3 representation of the DAP4 Data Response object."
              type="application/x-netcdf"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dap.nc" />
    </Service>

    <!-- ##################################################################################### -->
    <!--   Optional DAP4 Related Services                                                      -->
    <!-- ..................................................................................... -->

    <Service title="ISO-19115 Metadata Service" role="http://services.opendap.org/dap4/iso-19115">
        <link description="Dataset metadata as ISO-19115"
              type="text/xml"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dmr.iso">
              <alt type="text/html"/>
        </link>
        <link description="ISO-19115 conformance score"
              type="text/html"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dmr.rubric">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_ISO_Conformance_Score_Service">ISO-19115 conformance score for the DMR.</Description>
    </Service>

    <Service title="File Access" role="http://services.opendap.org/dap4/file">
        <link type="text/xml ## This value depends on the file being accessed."
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.file">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Native_File_Access_Service">Access to dataset file.</Description>
    </Service>



    <!-- ##################################################################################### -->
    <!--       DAP2 Services                                                                   -->
    <!-- ..................................................................................... -->
    <Service title="DAP2 Data" role="http://services.opendap.org/dap2/dods">
        <link type="application/octet-stream"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dods">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_Data_Service">DAP2 Data Object.</Description>
    </Service>

    <Service title="DDX" role="http://services.opendap.org/dap2/ddx">
        <link type="text/xml"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.ddx">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_DDX_Service">OPeNDAP Data Description and Attribute XML Document.</Description>
    </Service>

    <Service title="DDS" role="http://services.opendap.org/dap2/dds">
        <link type="text/plain"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.dds">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_DDS_Service">OPeNDAP Data Description Structure.</Description>
    </Service>

    <Service title="DAS" role="http://services.opendap.org/dap2/das" >
        <link type="text/plain"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.das">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_DAS_Service">OPeNDAP Dataset Attribute Structure.</Description>
    </Service>


    <Service title="INFO" role="http://services.opendap.org/dap2/info">
        <link type="text/html"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.info">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP2:_Info_Service">OPeNDAP Dataset Information Page.</Description>
    </Service>

    <Service title="Server Version" role="http://services.opendap.org/dap4/version">
        <link type="text/xml"
              href="http://test.opendap.org:8090/opendap/hyrax/data/fnoc1.nc.ver">
        <Description href="http://docs.opendap.org/index.php/DAP4_Web_Services#DAP4:_Server_Version_Service">An XML document containing information about the software version of the server..</Description>
    </Service>


    <!-- ##################################################################################### -->
    <!--       Server Side Functions                                                           -->
    <!-- ..................................................................................... -->


    <function name="geogrid" role="http://services.opendap.org/dap4/server-side-function/geogrid">
        <Description  href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#geogrid">Allows a DAP Grid variable to be sub-sampled using georeferenced values.</Description>
    </function>

    <function name="grid" role="http://services.opendap.org/dap4/server-side-function/grid">
        <Description  href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#grid">Allows a DAP Grid variable to be sub-sampled using the values of the coordinate axes.</Description>
    </function>

    <function name="linear_scale" role="http://services.opendap.org/dap4/server-side-function/linear-scale">
        <Description  href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#linear_scale">Applies a linear scale transform to the named variable.</Description>
    </function>

    <function name="version" role="http://services.opendap.org/dap4/server-side-function/version">
        <Description  href="http://docs.opendap.org/index.php/Server_Side_Processing_Functions#version">Returns version information for each server side function.</Description>
    </function>

    <!-- ##################################################################################### -->
    <!--       Server Side Function Group                                                      -->
    <!-- ..................................................................................... -->


    <functionGroup name="ferret" role="http://pmel.noaa.gov/dap4extension/ferret" >
        <Description href="http://ferret.pmel.noaa.gov/Ferret/documentation">This server supports ferret functions.</Description>
    </functionGroup>

    <!-- ##################################################################################### -->
    <!--       Extensions                                                                      -->
    <!-- ..................................................................................... -->

    <extension name="async" role="http://opendap.org/extension/async">
        <Description href="http://docs.opendap.org/index.php/DAP4:_Asynchronous_Request-Response_Proposal_v3">The server supports asynchronous transactions.</Description>
    </extension>


</DatasetServices>
```

</font>

## Rationale for the solution

The solution provides DAP4 servers with an interface that allows for
both server-driven and agent driven content negotiation as described in
[section 12 of the HTTP 1.1
specification](http://www.w3.org/Protocols/rfc2616/rfc2616-sec12.html).
Providing the Dataset Services Response in XML allows many downstream
applications to ingest the content and leaves open the option for server
implementers to add XSLT references to the document so that clients like
browsers can easily build a presentation view of the information.

## Discussion

Here is one idea for the HTML version of the DSR; it could be generated
by the server using XSLT on the XML or by the browser when the user
explicitly requests the *.xml* variant of the response (by embedding a
reference to the XSL transform in that response document):

<figure>
<img src="ServiceDescriptionPrototype-01.png"
title="File:ServiceDescriptionPrototype-01.png" />
<figcaption><a
href="File:ServiceDescriptionPrototype-01.png">File:ServiceDescriptionPrototype-01.png</a></figcaption>
</figure>

## Appendix: Dataset Services Response XML schema (<font color="orange">Under Construction</font>)

This schema works, but it rigidly enforces the order of the content. We
may want to rewrite to be more lenient.

<font size="2">

``` xml

<?xml version="1.0" encoding="UTF-8"?>
<xs:schema
    targetNamespace="http://xml.opendap.org/ns/DAP/4.0/dataset-services#"
    xmlns:xs="http://www.w3.org/2001/XMLSchema"
    xmlns:xml="http://www.w3.org/XML/1998/namespace"
    xmlns:dsr="http://xml.opendap.org/ns/DAP/4.0/dataset-services#"
    ns="http://xml.opendap.org/ns/DAP/4.0/dataset-services#"
    elementFormDefault="qualified"
    attributeFormDefault="unqualified">

    <xs:import  namespace="http://www.w3.org/XML/1998/namespace" schemaLocation="http://www.w3.org/2001/03/xml.xsd"/>
    <!--

    -->
    <xs:element name="DatasetServices" type="DatasetServicesType"/>
    <xs:element name="DapVersion" type="DapVersionType"/>
    <xs:element name="ServerSoftwareVersion" type="AnyContentType"/>
    <xs:element name="Service" type="ServiceType"/>
    <xs:element name="Description" type="DescriptionType"/>
    <xs:element name="link" type="linkType"/>
    <xs:element name="alt" type="altType"/>
    <xs:element name="function" type="ExtensionType"/>
    <xs:element name="functionGroup" type="ExtensionType"/>
    <xs:element name="extension" type="ExtensionType"/>
    <!--

    -->
    <xs:complexType name="DatasetServicesType">
        <xs:annotation>
            <xs:documentation>DatasetServices root element type</xs:documentation>
        </xs:annotation>
        <xs:sequence>
            <xs:element ref="DapVersion" minOccurs="1" maxOccurs="unbounded"/>
            <xs:element ref="ServerSoftwareVersion" minOccurs="1" maxOccurs="1"/>
            <xs:element ref="Description" minOccurs="0" maxOccurs="1"/>
            <xs:element ref="Service" minOccurs="3" maxOccurs="unbounded"/>
            <xs:element ref="function" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element ref="functionGroup" minOccurs="0" maxOccurs="unbounded"/>
            <xs:element ref="extension" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
        <xs:attribute ref="xml:base"  use="required"/>
    </xs:complexType>
    <!--

     -->
    <xs:complexType name="ServiceType">
        <xs:annotation>
            <xs:documentation>DatasetServices Service type</xs:documentation>
        </xs:annotation>
        <xs:sequence>
            <xs:element ref="Description" minOccurs="0" maxOccurs="1"/>
            <xs:element ref="link" minOccurs="1" maxOccurs="unbounded"/>
        </xs:sequence>
        <xs:attribute name="title" type="xs:string" use="optional"/>
        <xs:attribute name="role" type="xs:anyURI" use="required"/>
    </xs:complexType>
    <!--

    -->
    <xs:simpleType name="DapVersionType">
        <xs:restriction base="xs:string">
            <xs:pattern value="\d{1,}.\d{1,}"></xs:pattern>
        </xs:restriction>
    </xs:simpleType>
    <!--

    -->
    <xs:complexType name="DescriptionType">
        <xs:simpleContent>
            <xs:extension base="xs:string">
                <xs:attribute name="href" type="xs:anyURI"/>
            </xs:extension>
        </xs:simpleContent>
    </xs:complexType>
    <!--

    -->
    <xs:complexType name="altType">
        <xs:attribute name="type" type="xs:string"/>
    </xs:complexType>
    <!--

    -->
    <xs:complexType name="linkType">
        <xs:annotation>
            <xs:documentation>DatasetServices link type</xs:documentation>
        </xs:annotation>
        <xs:sequence>
            <xs:element ref="alt" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
        <xs:attribute name="href" type="xs:string" use="required"/>
        <xs:attribute name="type" type="xs:string" use="required"/>
        <xs:attribute name="description" type="xs:string" use="optional"/>
    </xs:complexType>
    <!--

    -->
    <xs:complexType  name="AnyContentType" mixed="true">
        <xs:annotation>
            <xs:documentation>
                An element of this type may contain arbitrary XML or text content.
                The resulting content is may be ignored by DAP software. Other software
                might find the information useful. The XML elements must
                satisfy the requirements for 'lax' processing under schema 1.0.
                In practice, that means just about anything.
                ( <xs:any/>+ )
            </xs:documentation>
        </xs:annotation>
        <xs:sequence>
            <xs:any namespace="##any" minOccurs="0" processContents="lax"/>
        </xs:sequence>
        <xs:anyAttribute processContents="lax" namespace="##any"/>
    </xs:complexType>
    <!--

    -->
    <xs:complexType name="ExtensionType">
        <xs:annotation>
            <xs:documentation>Server Extension Type</xs:documentation>
        </xs:annotation>
        <xs:sequence>
            <xs:element ref="Description" minOccurs="1" maxOccurs="1"/>
        </xs:sequence>
        <xs:attribute name="name" type="xs:string" use="required"/>
        <xs:attribute name="role" type="xs:anyURI" use="required"/>
    </xs:complexType>
    <!--

     -->

</xs:schema>
```

</font>

## fini

Contains cruft in comments...

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")