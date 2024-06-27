[\<-- back to OPULS Development](OPULS_Development "wikilink")

--[EthanDavis](User:EthanDavis "wikilink") 13:32, 16 April 2012 (PDT)

## Background

Providing OPeNDAP clients with information on the particular
capabilities of a given server and given dataset has been discussed many
times. Types of capabilities include supported DAP versions, supported
server-side functions. Another possible capability would be support for
multiple encodings of the various OPeNDAP services. Providing
capabilities documents can also make it easier to plan for extensibility
and the future evolution of OPeNDAP.

Negotiating versions using HTTP headers or URL keywords is described in
[DAP 4.0 Design doc](DAP4:_DAP_Versions "wikilink"). Advertising DAP
protocol services is described in the [DAP Service Terminus
document](DAP_Service_Terminus "wikilink").

## Problem addressed

The proposal attempts to address the following requirements:

- Support for multiple versions of DAP protocol in a backwards
  compatible way
- Support for extensibility and evolution of protocol
- Support for multiple encoding formats (media types) for each resource
  type
- Support description of server and dataset capabilities (e.g.,
  server-side processing capabilities)

## Proposed solution

Provide dataset capabilities documents from the raw DAP dataset URL. A
dataset's capabilities document includes links to the various OPeNDAP
services for that dataset. Each link is described with a link
relationship type and a media type. The link relationship type indicates
what type of resource will be returned. The media type indicates the
document type in which the returned resource will be encoded.

Providing a dataset capabilities document with links to both DAP2 and
DAP4 resources allows for DAP2 backward compatibility and will support
protocol extensibility and future evolution of the protocol.

### Raw DAP Dataset URLs Return Capabilities Documents

A GET request on a DAP dataset URL (e.g.,
**http://server.org/some/path/data.nc**) returns the capabilities
document for the dataset. To take advantage of web caching, capabilities
documents should try to be light weight (i.e., quick creation) and as
stable as possible.

A dataset capabilities document must contain a **server** section and a
**dataset** section.

The **server** section should include

- a link to the full server capabilities document (if applicable)
- a list of the DAP versions the server supports
- the implementation version (e.g., "TDS 4.3.57" or "Hyrax 1.7.45")
- a list of extensions the server supports

The **dataset** section should include

- a link to the full dataset capabilities document (this document)
- links to all available OPeNDAP services for the dataset
- a list of extensions supported for this dataset (links?)
- a link to a constraint expression document detailing the constraint
  supported for this dataset
- a list of the types of processing instructions supported for this
  dataset

Dataset capabilities documents can be encoded in the following media
types:

- **application/vnd.opendap.org.capabilities+xml**
- **application/vnd.opendap.org.capabilities+json**
- **application/vnd.opendap.org.capabilities+pbuf**

Definitions of these media types can be found in :

- The DAP4 Capabilities XML schema (link) which is declared in the
  namespace **http://opendap.org/ns/dap/capabilities**.
- The DAP4 Capabilities JSON definition (link).
- The DAP4 Capabilities protobuf definition (link).

A link to a Dataset Capabilities document can be identified by the
following link relationship type:

- **http://opendap.org/rel/dap/dataset**

The URL of a link with this relation type can be used to GET the dataset
capabilities document for the dataset.

An example of an HTTP request for an XML encoded dataset capabilities
document is given
[below](#Server_Capabilities_Document_Request-Response "wikilink").

### DAP4 DDX

A DAP4 DDX document describes the structure of a dataset and contains
other metadata for that dataset. It must contain a link to the DAP4 Data
and to a CE document that describes the constraints supported for this
dataset.

DAP4 DDX documents can be encoded in the following media types:

- **application/vnd.opendap.org.dap4.ddx**
- **application/vnd.opendap.org.dap4.ddx+pbuf**

Definitions of these media types can be found in:

- The DAP4 DDX XML schema (link) which is declared in the namespace
  **http://opendap.org/ns/dap4/ddx**.
- The DAP4 DDX JSON definition (link).
- The DAP4 DDX protobuf definition (link).

A link to a DAP4 DDX document can be identified by the following link
relationship type:

- **http://opendap.org/rel/dap4/ddx**

The URL of a link with this relation can be used to GET a DAP4 DDX
resource.

### DAP4 Data

A DAP4 Data resource contains a subset of data from the dataset. They
may be encoded in any of the following media types:

- **application/vnd.opendap.org.dap4.data**
- **application/vnd.opendap.org.dap4.data+ascii**
- **application/vnd.opendap.org.dap4.data+json**
- **application/vnd.opendap.org.dap4.data+ncstream**

Definitions of these media types can be found in:

- The DAP4 Data encoding specification (link).
- The DAP4 Data ASCII/UTF-8 encoding specification (link).
- The DAP4 Data JSON definition (link).
- The DAP4 Data ncstream/cdmremote definition (link).

A link to a DAP4 DDX document can be identified by the following link
relationship type:

**http://opendap.org/rel/dap4/data**

The URL of a link with this relation can be used to GET or POST a DAP4
data resource. (POST is used if a dataset session is desired, i.e., to
freeze dynamically changing datasets, or if an asynchronous response is
expected.)

### DAP4 Constraint Expression

A DAP4 Constraint Expression document contains a constraint that is to
be applied to the dataset. They may be encoded in any of the following
media types:

- **application/vnd.opendap.org.dap4.ce** (for GET, URL encoding)
- **application/vnd.opendap.org.dap4.ce+xml** (for POST, request body
  encoding)

Definitions of these media types can be found in:

- The DAP4 CE URL encoding specification (link).
- The DAP4 CE XML schema (link) which is declared in the namespace
  **http://opendap.org/ns/dap4/ce**.
- The DAP4 Data JSON definition (link).
- The DAP4 Data ncstream/cdmremote definition (link).

A link to a DAP4 CE document can be identified by the following link
relationship type:

- **http://opendap.org/rel/dap4/ce**

The URL of a link with this relation can be used to GET a DAP4
constraint expression document.

Generally constraint expression are only used as part of a data request.
This link relation type is given in case stand-alone constraint
expression resources are found useful.

> NOTE: Could this document describe the types of constraints that can
> be applied to particular variables in a dataset? Is such a thing
> needed? If so, should it be a separate resource type.

### DAP4 Server Capabilities Documents

Do we need/want a server-level capabilities document? Or is a
dataset-level one enough?

Server capabilities documents are encoded in the same media types that
are defined for the DAP4 Dataset Capabilities Documents (see
[above](#DAP4_Dataset_Capabilities_Documents "wikilink")).

A link to a Server Capabilities document can be identified by the
following link relationship type:

- **http://opendap.org/rel/dap/server**

The URL of a link with this relation type can be used to GET the server
capabilities document for the server.

### DAP2 DAS

**application/vnd.opendap.org.dap2.das**

A representation of this media type contains a DAP2 DAS document.

### DAP2 DDS

**application/vnd.opendap.org.dap2.dds**

A representation of this media type contains a DAP2 DDS document.

### DAP2 DDX

**application/vnd.opendap.org.dap2.ddx**

A representation of this media type contains a DAP2 DDX document
declared in the namespace **http://opendap.org/nc/dap2/ddx**.

### DAP2 Data

- **application/vnd.opendap.org.dap2.data**
- **application/vnd.opendap.org.dap2.data+ascii**

A representation of one of these media types contains DAP2 data. See the
DAP2 specification for details.

### DAP2 Constraint Expression

- **application/vnd.opendap.org.dap2.ce**

A representation of this media type contains a DAP2 constraint
expression. Generally used as a DAP2 data URL query string. See the DAP2
specification for details.

### Extension Resource Types

A number of extensions to the core DAP4 protocol have been discussed.
Here are a few extensions and some resource types that we might
consider:

### DAP4 Asynchronous Request/Response

See [DAP4: Asynchronous Request-Response
Proposal](DAP4:_Asynchronous_Request-Response_Proposal "wikilink")

### DAP4 Server-Side Processing Instructions

- **application/vnd.opendap.org.dap4.processing+ferret**
- **application/vnd.opendap.org.dap4.processing+grads**
- **application/vnd.opendap.org.dap4.processing+jython**
- **application/vnd.opendap.org.dap4.processing+jvm**

<!-- -->

- **http://opendap.org/rel/dap4/processing**

## Rationale for the solution

In an attempt to take advantage of the architecture of the web, this
proposal is based on some RESTful ideas. REST targets networks of
hypermedia resources connected to each other by the hyperlinks those
resources contain. The links in the network can have link types that
specify the type of resource that will be found at the end of the link.
Link types define the set of information and the links that can be
contained by a resource of that type. Each resource type can have
multiple encodings in which a resource of that type can be represented.

This architecture can make for a robust and extensible system. URLs can
change and new links can be added without breaking the system. The links
in a resource provide an application the set of next available steps the
application can take. Or another way of thinking about it, the network
of hypermedia resources represent a virtual state machine for the web
application.

By specifying all URLs in links rather than defining them in a
specification, the form of the URLs becomes an implementation detail,
determined by the server, rather than a part of the specification. This
helps remove some of the tight coupling often seen between client and
server. (This does not, however, rule out the use of URL conventions,
like file extensions to specify encoding forms. Some recommend support
for both HTTP **Accept** headers and for file extensions \[1\].)

By thinking in terms of link (resource) types and media types (encoding
formats), specifications have a natural division into separate
components.

- The specification of a link/resource type defines the set of
  information and links allowed in a resources of that type.
- The specification of a media type defines how the information and
  links allowed in the targetted resource type are encoded.
- The specification of how the network application is bound to a
  specific transport protocol (like HTTP).

To take advantage of the caching built into the web infrastructure, one
should think about commonly used resources and whether they can be made
somewhat stable and quick to return. Or at least be sure HTTP caching
headers are used properly.

The bottom line is that features of the chosen transport protocol should
be used by web applications to the fullest extent possible to gain the
advantages of the features of that transport protocol. A relevant quote
from Wikipedia's REST page \[2\]:

> `RESTful applications maximize the use of the pre-existing,`
> `well-defined interface and other built-in capabilities provided by`
> `the chosen network protocol, and minimize the addition of new`
> `application-specific features on top of it.`

and another one specific to HTTP \[2\]:

> `HTTP, for example, has a very rich vocabulary in terms of verbs`
> `(or "methods"), URIs, Internet media types, request and response`
> `codes, etc. REST uses these existing features of the HTTP`
> `protocol, and thus allows existing layered proxy and gateway`
> `components to perform additional functions on the network such as`
> `HTTP caching and security enforcement.`

## Discussion

[Jimg](User:Jimg "wikilink") 13:48, 10 May 2012 (PDT) I would like to
see this broken up into two or more papers where one specifies the
proposed HTTP implementation of the requests and responses of DAP4.
Others can specify the structure and interpretation of the Capabilities
response and how a web service will support multiple versions of DAP,
including DAP2 and DAP4 (and variants of those protocols, should we
allow that).

## Examples

### Server Capabilities Document Request-Response

Client uses DAP dataset URL, **http://server.org/dap/path/data.nc**, to
GET the dataset capabilities resource as an XML document:

<font size="2">

    GET /dap/path/data.nc HTTP/1.1
    Host: server.org
    Accept: application/vnd.opendap.org.capabilities+xml

</font>

The response might look like like:

<font size="2">

``` xml
200 OK
Content-Type: application/vnd.opendap.org.capabilities+xml;charset=UTF-8

<capabilities ns="http://opendap.org/ns/dap/capabilities">
  <server rel="http://opendap.org/rel/dap/server"
          type="application/vnd.opendap.org.capabilities+xml,
                application/vnd.opendap.org.capabilities+json,
                application/vnd.opendap.org.capabilities+pbuf"
          href="http://server/dap/capabilities">
    <protocolVersions>
      http://opendap.org/rel/dap2
      http://opendap.org/rel/dap3
      http://opendap.org/rel/dap4
    </protocolVersions>
    <implVersion>TDS 4.3.54</implVersion>
    <supportedExt>...</supportedExt>
  </server>
  <dataset rel="self"
           type="application/vnd.opendap.org.capabilities+xml,
                 application/vnd.opendap.org.capabilities+json,
                 application/vnd.opendap.org.capabilities+pbuf"
           href="http://server/dap/path/data.nc">
    <version name="dap2">

      <serviceLink rel="http://opendap.org/rel/dap2/das"
                   type="application/vnd.opendap.org.dap2.das"
                   href="http://server/some/path/data.nc.das"/>
      <serviceLink rel="http://opendap.org/rel/dap2/dds"
                   type="application/vnd.opendap.org.dap2.dds"
                   href="http://server/dap/path/data.nc.dds"/>
      <serviceLink rel="http://opendap.org/rel/dap2/ddx"
                   type="application/vnd.opendap.org.dap2.ddx+xml"
                   href="http://server/dap/path/data.nc.ddx"/>
      <serviceLink rel="http://opendap.org/rel/dap2/dods"
                   type="application/vnd.opendap.org.dap2.dods"
                   href="http://server/dap/path/data.nc.dods"/>
      <serviceLink rel="http://opendap.org/rel/dap2/dods"
                   type="application/vnd.opendap.org.dap2.dods+ascii"
                   href="http://server/dap/path/data.nc.ascii"/>
      <constraintExp ...>...</constraintExp>
      <processing ...>...</processing>
    </version>
    <version name="dap4">
      <serviceLink rel="http://opendap.org/rel/dap4/description"
                   type="application/vnd.opendap.org.dap4.description+xml,
                         application/vnd.opendap.org.dap4.description+json,
                         application/vnd.opendap.org.dap4.description+pbuf"
                   href="http://server/dap/path/data.nc.ddx4"/>
      <serviceLink rel="http://opendap.org/rel/dap4/data"
                   type="application/vnd.opendap.org.dap4.data,
                         application/vnd.opendap.org.dap4.data+ascii,
                         application/vnd.opendap.org.dap4.data+json,
                         application/vnd.opendap.org.dap4.data+ncstream"
                   href="http://server/dap/path/data.nc.data">
        <constraintExp...>...</constraintExp>
        <processing ...>...</processing>
      </serviceLink>
    </version>
  </dataset>
</capabilities>
```

</font>

### Constrained Data Request-Response

Request: <font size="2">

    GET /dap/path/data.nc?x,y,temp HTTP/1.1
    Host: server.org
    Accept: application/vnd.opendap.org.dap4.data

</font>

Response: <font size="2">

``` xml
200 OK
Content-Type: application/vnd.opendap.org.dap4.data

<ddx>...</ddx>...BINARY DATA...
```

</font>

### Create New Dataset with Constrained Data Request-Response

Request: <font size="2">

    POST /dap/path/data.nc HTTP/1.1
    Host: server.org
    Content-Type: application/vnd.opendap.org.dap4.ce+xml;charset=UTF-8

    <constraintExp>...</constraintExp>

</font>

Response: <font size="2">

``` xml
201 Created
Content-Type: application/vnd.opendap.org.dap4.capabilities+xml;charset=UTF-8

<capabilities ns="http://opendap.org/ns/dap/capabilities">
  <server ...>...</server>
  <dataset rel="self"
           type="application/vnd.opendap.org.capabilities+xml,
                 application/vnd.opendap.org.capabilities+json,
                 application/vnd.opendap.org.capabilities+pbuf"
           href="http://server/dap/path/data.nc-ABCD">
    <dap:source>
      <dataset type="application/vnd.opendap.org.capabilities+xml,
                     application/vnd.opendap.org.capabilities+json,
                     application/vnd.opendap.org.capabilities+pbuf"
               href="http://server/dap/path/data.nc" />
      <constraintExp>
        ...
      </constraintExp>
    </dap:source>
    <version name="dap2">
      ...
    </version>
    <version name="dap4">
      ...
    </version>
  </dataset>
</capabilities>
```

</font>

## Some References

\[1\] <http://stackoverflow.com/a/385216>

- A stackoverflow answer to the question "Use 'Accept' header or
  extension to identify resource type?" Answer: the ideal would be to
  implement both.

\[2\] <http://en.wikipedia.org/wiki/Representational_state_transfer>

\[3\] <http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm>