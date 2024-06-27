<font size="+1" color="red">This is an old document that captures the
starting point of the OPULS design work. It's out of date and should be
referenced only as a baseline for the work.</font>

[\<-- back to OPULS Development](OPULS_Development "wikilink")

Author: [Jimg](User:Jimg "wikilink"), NDP, ?

## Protocol Independence

**Proposal**: The DAP4 data model and it's persistent (over-the-wire)
representations are transport protocol agnostic. All of the information
required by the DAP must be present in the content of the DAP requests
and responses. The DAP won't uniquely embed information required by the
DAP into HTTP Headers or AMQP thingys, or other whatnot.

**Discussion**:

1.  Does this matter?
2.  Would we need to add new syntax server-side functions etc. to the
    protocol to enable this?

## Overview

This page describes the various web services that a DAP4 server must
provide. These are for the most part REST services and as such are
stateless unless noted otherwise.

The services are all defined as a modification of the service (aka
resource or base) URL. This base URL essentially becomes a prefix (and
could even be seen as a namespace) for all of the services available for
that data resource.

In practice these have traditionally been implemented over HTTP. However
they could just as easily be pushed over a different protocol, as long
as the usage of the URL components remain consistent.

## Services

A Service is represented by:

- A unique "namespace" like identifier that is used to define an
  xlink:role attribute.
- A simple human readable name called 'title'.
- An access URL.
- An optional description.

A service is accessed by dereferencing its access URL, which is
typically constructed by adding some type of suffix to the dataset's
referent (aka base) URL. The way in which the query string (aka
constraint expression) is used is defined by each service, and there is
no requirement for inter-service query string API conformity.

### Primary DAP4 Services

#### [DAP4: Dataset Services Description Service](DAP_Service_Terminus "wikilink")

The Dataset Services Service provides a listing of the various services
available for a dataset. Dereferencing the base URL for a data set
accessed through a DAP4 web server will return a *service document* that
describes the various responses possible for that data set, including
URLs that can be used to access those responses.

More information on the Service Description response can be found on the
[DAP Service Terminus](DAP_Service_Terminus "wikilink") page.

service url = <font size="2">**`dataset_url`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/dataset-services#`**</font>


#### **[DAP4: Dataset Service - The metadata](DAP4:_Dataset_Service "wikilink")**

The Dataset Service provides a metadata description of the dataset. The
Dataset response is an XML document that contains both the 'syntactic'
(structural) and 'semantic' metadata for the dataset, persisted as a
[DAP4: Data Model](DAP4:_Data_Model "wikilink") representation of the
dataset held at the server. The Dataset service accepts a query string
(constraint expression) that allows you to inspect the effects on the
data structures when sub-setting and/or server side functions are
applied. If a constraint expression has been successfully applied, the
service will returned the constrained view of the dap:Dataset object.
The constrained view may contain different data structures than the
unconstrained view as the constraint may alter the reasonable
representation of the data set. Note that [All dap:Attribute objects
have been removed from constrained dap:Dataset
objects.](DAP4:_Responses#Transmitting_Attributes_in_constrained_Dataset_documents "wikilink")

- More information on the Dataset response can be found on the [DAP4:
  Responses](DAP4:_Responses#Dataset_Response "wikilink") page.

<!-- -->

- More information on the syntax of DAP4 constraint expressions can be
  found on [DAP4: Data
  Model](DAP4:_Data_Model#Constraint_Expressions "wikilink") page.

suffix = <font size="2">**`.xml`**</font>
service url =
<font size="2">**`dataset_url + '.xml' + [?dap_constraint]`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/dataset#`**</font>


#### **[DAP4: Data Service](DAP4:_Data_Service "wikilink")**

The Data Service provides DAP4 data access to a dataset, and is the way
that DAP4 returns data to a client. The Data service accepts a query
string (constraint expression) which allows you to subset the data and
invoke server side functions. When the service is invoked it returns
over the wire as a multipart MIME document where the first MIME part
contains the *constrained* Dataset response describing the data
requested and the following MIME parts contain the data values, encoded
using XDR, followed by a checksum, for each variable in the dataset.

More information on the Data response can be found on the [DAP4:
Responses](DAP4:_Responses#Data_Response "wikilink") page.

suffix = <font size="2">**`.dap`**</font>
service url =
<font size="2">**`dataset_url + '.dap' + [?dap_constraint]`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/data#`**</font>


### Other DAP4 Services

#### [DAP4: HTML DATA Request Form Service](DAP4:_HTML_DATA_Request_Form_Service "wikilink")

The HTML DATA Request Form Service provides browser based access to the
Dataset. When invoked it returns a web-browser renderable document (in
html) that provides a form (or other UI) that can be used to constrain
and request data in accordance with the DAP4 specification as applied to
the dataset .

suffix = <font size="2">**`.html`**</font>
service url = <font size="2">**`dataset_url + .html`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/data-request-form#`**</font>


#### [DAP4: RDF Service](DAP4:_RDF_Service "wikilink")

The RDF service provides an RDF representation of the Dataset document
(DDX). The RDF response is an XML document containing an RDF version of
the [DAP4: Dataset
Response.](DAP4:_Responses#Dataset_Response "wikilink")

suffix = <font size="2">**`.rdf`**</font>
service url = <font size="2">**`dataset_url + .rdf`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/rdf#`**</font>


#### [DAP4: ISO 19115 Service](DAP4:_ISO_19115_Service "wikilink")

This service provides ISO 19115 metadata for the Dataset, if any can be
found. When invoked it returns an XML document containing ISO 19115
metadata located in the [DAP4: Dataset
Response.](DAP4:_Responses#Dataset_Response "wikilink")

suffix = <font size="2">**`.iso`**</font>
service url = <font size="2">**`dataset_url + .iso`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/iso-19115-metadata#`**</font>


#### [DAP4: ISO Conformance Score Service](DAP4:_ISO_Conformance_Score_Service "wikilink")

This service provides a browser renderable document that describes how
well the metadata held in the Dataset conforms to ISO 19115. When
invoked this service returns a browser renderable document that scores
how well the metadata held in the [Dataset
Response](DAP4:_Responses#Dataset_Response "wikilink") conforms to ISO
19115.

suffix = <font size="2">**`.rubric`**</font>
service url = <font size="2">**`dataset_url + .rubric`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/iso-19115-score#`**</font>


#### [DAP4: NetCDF File-out Service](DAP4:_NetCDF_File-out_Service "wikilink")

This service provides data responses in NetCDF-3 file format. When
invoked the regular DAP data response will be repackaged as a NetCDF 3
file.

suffix = <font size="2">**`.nc`**</font>
service url =
<font size="2">**`dataset_url + '.nc' + [?dap_constraint]`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/netcdf-3#`**</font>


#### [DAP4: ASCII Data Service](DAP4:_ASCII_Data_Service "wikilink")

This service provides data responses in ASCII format. When invoked the
regular DAP data response will be repackaged as an ASCII representation
of the data values.

suffix = <font size="2">**`.ascii`**</font>
service url =
<font size="2">**`dataset_url + '.ascii' + [?dap_constraint]`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/ascii#`**</font>


#### [DAP4: XML Data Service](DAP4:_XML_Data_Service "wikilink")

This service provides data responses in XML format. When invoked the
constrained Dataset response document (DDX) will be marked up with the
data values of the request and returned. Large requests may be denied.

suffix = <font size="2">**`.xdap`**</font>
service url =
<font size="2">**`dataset_url + '.xdap' + [?dap_constraint]`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/xml-data#`**</font>


#### [DAP4: Native File Access Service](DAP4:_Native_File_Access_Service "wikilink")

This service provides direct access to the data source file (or whatever
else) that is creating the DAP dataset resource. When invoked it returns
the "native" data from whatever store (filesystem, etc.) it may be in.

suffix = <font size="2">**`.file`**</font>
service url = <font size="2">**`dataset_url + .file`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/file#`**</font>


#### [DAP4: Server Version Service](DAP4:_Server_Version_Service "wikilink")

This service provides software versioning information. When invoked the
services returns an XML file containing a description of the version of
the server and it's components.

suffix = <font size="2">**`.ver`**</font>
service url = <font size="2">**`dataset_url + .ver`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap4/version#`**</font>


### DAP2 Services

#### [DAP2: Data Service](DAP2:_Data_Service "wikilink")

The DAP2 data service provides DAP2 data access to the data resource.

suffix = <font size="2">**`.dods`**</font>
service url =
<font size="2">**`dataset_url + '.dods' + [?dap_constraint]`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap2/dods#`**</font>


#### [DAP2: DDX Service](DAP2:_DDX_Service "wikilink")

The DAP2 DDX service provides DAP2 access to the data resource metadata.
When invoked the service returns an XML document containing both
syntactic and semantic dataset metadata in DAP2 XML format.

suffix = <font size="2">**`.ddx`**</font>
service url = <font size="2">**`dataset_url + .ddx`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap2/ddx#`**</font>


#### [DAP2: DDS Service](DAP2:_DDS_Service "wikilink")

The DAP2 DDS service provides access to the 'syntactic' metadata (aka
use or structural metadata) for the data resource. When invoked returns
a DAP2 DDS response document conforming to the DDS part of the DAP2
specification.

suffix = <font size="2">**`.dds`**</font>
service url =
<font size="2">**`dataset_url + '.dds' + [?dap_constraint]`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap2/dds#`**</font>


#### [DAP2: DAS Service](DAP2:_DAS_Service "wikilink")

The DAP2 DAS service provides access to the 'semantic' metadata (aka
domain metadata) for the data resource. When invoked returns a DAP2 DAS
response document conforming to the DAS part of the DAP2 specification.

suffix = <font size="2">**`.das`**</font>
service url = <font size="2">**`dataset_url + .das`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap2/das#`**</font>


#### [DAP2: Info Service](DAP2:_Info_Service "wikilink")

The DAP2 INFO service provides a browser renderable page that combines
both the DAP2 'syntactic' and 'semantic' metadata for the data resource
in a human readable way. When invoked this service returns a web browser
renderable document that combines both the DAP2 'syntactic' and
'semantic' metadata for the data resource in a human readable way.

suffix = <font size="2">**`.info`**</font>
service url = <font size="2">**`dataset_url + .info`**</font>
role id =
<font size="2">**`http://services.opendap.org/dap2/Info#`**</font>


[Template: ServiceTemplate](Template:_ServiceTemplate "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")