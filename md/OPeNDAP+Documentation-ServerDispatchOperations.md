OPeNDAP servers provide an interface through which clients can access
data held on the server. The server can provide sub-setting and, in some
cases, additional pre-processing operations on the requested data. The
protocol through which OPeNDAP servers provide these data services is
called the Data Access Protocol (DAP). The current version of the DAP is
version 2, usually referred to as DAP2.

In addition to DAP2 responses, most OPeNDAP servers provide a number of
additional services such as:

- A navigation interface for browsing the data storage hierarchy.
- File access.
- Version reporting.
- Help pages and other documentation.
- THREDDS catalog views of the data system.

Request dispatch is the process through which an OPeNDAP Server
determines what actual piece of code is going to respond to a given
incoming request. The identified piece of software will generate the
type of response the client intended to invoke on the server, or at
worst a sensible error response such as a web page or a serialized DAP2
error object.

# Anatomy of a URL

The following definitions will be used in this document:

Request URL
The complete URL as submitted by the requesting client.

<!-- -->

Constraint Expression (CE)
Everything following the "**?**" character in the request URL

<!-- -->

Datasource URL
The request URL with the CE removed (the "**?**" and everything after
it.)

<!-- -->

Projection
*The things that are being requested.* The part of the CE prior to the
first "**&**" character. This is where individual variables from a
dataset can be requested, or if no variables are specified then all of
the varaibles will be requested. The end of the projection is marked by
the end of the request URL, or by the presence of the first "**&**"
character.

<!-- -->

Selection
*The conditions that must be met for the requested things.* The
remainder of the request URL after the datasource URL and projection
have been removed.

<!-- -->

Request suffix
The collection of characters after the last occurrence of the "**.**"
characters in the datasource URL. For many requests the request suffix
may not exist, or it may be nonsensical.

<!-- -->

Datasource name
The datasource URL with the "scheme" (typically *<http://>*), the
server\[:port\], servlet context, and CE removed.

## Examples

### 1


Request URL:
**http://localhost:8080/opendap/data/nc/fnoc1.nc.dds?sst&time\<3**

CE: **sst&time\<3**

Datasource URL: **http://localhost:8080/opendap/data/nc/fnoc1.nc.dds**

Projection: **sst**

Selection: **time\<3**

Request suffix: **dds**

Datasource name: **/data/nc/fnoc1.nc**

### 2


Request URL: **http://localhost:8080/opendap/data/nc/fnoc1.nc.html**

CE:

Datasource URL: **http://localhost:8080/opendap/data/nc/fnoc1.nc.dds**

Projection:

Selection:

Request suffix: **html**

Datasource name: **/data/nc/fnoc1.nc**

### 3


Request URL: **http://localhost:8080/opendap/data/nc/**

CE:

Datasource URL: **http://localhost:8080/opendap/data/nc/**

Projection:

Selection:

Request suffix:

Datasource name: **/data/nc/**

### 4


Request URL: **http://localhost:8080/opendap/data/nc/README.txt**

CE:

Datasource URL: **http://localhost:8080/opendap/data/nc/README.txt**

Projection:

Selection:

Request suffix: **txt**

Datasource name: **/data/nc/README**

# [Hyrax](Hyrax "wikilink") Dispatch

The Hyrax front end (called the OLFS) handles each incoming request by
offering the request to a series of Java DispatchHandlers. Each
DispatchHandler instance is asked if it can handle the request. The
first DispatchHandler to say that it can is then asked to handle the
request. The OLFS creates its ordered list of DispatchHandlers by
reading its configuration file. The order of the list is significant.
Because the first DispatchHandler in the list to claim a request gets to
service it, changing the order of the DispatchHandlers can change the
behavior of the OLFS (and thus of Hyrax).

The usefulness of this dispatching scheme is that it creates
extensibility. If a third party wishes to add new functionality to Hyrax
one way is to write a DispatchHandler. To incorporate it into Hyrax they
only need to add it to the list in the configuration file and add the
java classes to the Tomcat *lib* directory.

## Default Order of Dispatch Operations In Hyrax

The default *olfs.xml* file (\$CATALINA_HOME/content/opendap/olfs.xml)
invokes the DispatchHandlers in the following order:

1.  Static THREDDS Catalog Response Handler
2.  DAP2 Response Handler
3.  Directory Response Handler
4.  Special Request Handler
5.  Version Response Handler
6.  File Response Handler
7.  BES Dynamic THREDDS Response Handler

Each DispatchHandler evaluates the incoming request by analyzing the
request URL to determine if it can handle the request.

# OPeNDAP URL Dispatch

How the request URLs are evaluated to pair them with the correct
response.

## Special Requests

Hyrax and other servers may support some special requests. These may be
a secure server administration interface, help information, server
status, or system properties. There is no clearly defined set of special
responses, and their implementation is left up to the various server
developers.

Hyrax currently supports:

- help - The URL **http://your.host:port/opendap/help** will take you to
  an help web page.

## Version Response

The version response can be invoked by setting the datasource name to
**version**, or by changing the request suffix of the request to
**ver**.

For example the URLs:


**<http://test.opendap.org/opendap-3.7/nph-dods/version>**

**<http://test.opendap.org/opendap-3.7/nph-dods/data/nc/TestPatInt.nc.ver>**

**<http://test.opendap.org:8080/opendap/version>**

**<http://test.opendap.org:8080/opendap/data/nc/TestPatInt.nc.ver>**

Will all return the version response.

The version response is provided so that clients may asses the version
of the DAP and the software running on the server.

In the past the version response was returned as simple text:

`Core software version: libdap/3.7.2`
`Server Script Revision: DAP2/3.7.1`

The current Hyrax server returns the version response as an XML
document:

<OPeNDAP-Version>
`  `<BES-Version>
`    `<BES>
`      `<prefix>`/`</prefix>
`      `<lib>
`        `<name>`bes`</name>
`        `<version>`3.5.3`</version>
`      `</lib>
`    `</BES>
`    `<Handlers>
`      `<lib>
`        `<name>`dap-server/ascii`</name>
`        `<version>`3.8.4`</version>
`      `</lib>
`      `<DAP>
`        `<version>`2.0`</version>
`        `<version>`3.0`</version>
`        `<version>`3.2`</version>
`      `</DAP>
`        `<name>`netcdf_handler`</name>
`        `<version>`3.7.8`</version>
`      `</lib>
`    `</Handlers>
`  `</BES-Version>
`  `<OLFS>
`    `<lib>
`      `<name>`olfs`</name>
`      `<version>`1.3.0`</version>
`    `</lib>
`  `</OLFS>
`  `<Hyrax>
`    `<lib>
`      `<name>`Hyrax`</name>
`      `<version>`1.3.0`</version>
`    `</lib>
`  `</Hyrax>
</OPeNDAP-Version>

## OPeNDAP Directory Response

The OPeNDAP directory response is the mechanism provided by OPeNDAP
servers for users to navigate (or "browse") the hierarchy of data sets
available on the OPeNDAP server. Each collection of data sets and child
collections will be returned as an (HTML) web paged that can be rendered
by a web browser application.

<figure>
<img src="DirectoryView.png" title="DirectoryView.png" />
<figcaption>DirectoryView.png</figcaption>
</figure>

The Directory response is invoked when one of the following conditions
is met:


1.The default directory view (see [OLFS
Configuration](Hyrax_-_OLFS_Configuration "wikilink")) is set to
**OPeNDAP** AND one of the following is true:

:\* The datasource name ends with a "**/**" character.

:\* The datasource name resolves to a collection on the server.

<!-- -->


2\. The datasource name ends with "**/contents**" and the request suffix
is equal to "**html**"

**Errors**


If the request is identified as an OPeNDAP directory request and it
cannot be fulfilled then an web page describing the problem along with
the appropriate HTTP status code will be returned to the client.

## DAP2 Service

The DAP2 service provides the DAP2 data responses as described in the
[Data Access Protocol
specification](http://www.opendap.org/pdf/ESE-RFC-004v1.1.pdf)

DAP2 service requests are identified by analysis of the URL as follows:

If the datasource name is associated with a resource that the server
recognizes as **data** (something for which it can provide DAP2 data
responses) and the request suffix is equal to:

- **ddx** - The server will attempt to return the DDX, an XML version of
  the combined DDS and DAS for the data set.
- **dds** - The server will attempt to return the Dataset Descriptor
  Structure (DDS) for the data set.
- **das** - The server will attempt to return the Dataset Attribute
  Structure (DAS) for the data set.
- **dods** - The server will attempt to return the DAP2 data object (A
  constrained DDS populated with data) for the data set.
- **asc** or **ascii** - The server will attempt to return a comma
  delimited 7 bit ASCII representation of the DAP2 data object .
- **info** - The server will attempt to return an HTML document that
  displays the data set's attributes, types and other information.
- **html** - The server will attempt to return an HTML document that
  contains the data request form for the specified data set.
- **help** - The server will return an HTML document that contains some
  help information.

**Errors**

- If the server encounters problems responding to a request for:

:\* **ddx**

:\* **dds**

:\* **das**

:\* **dods**


Then the server should attempt to return a serialized DAP2 error object.

- If the server encounters problems responding to a request for:

:\* **asc** or **ascii**

:\* **info**

:\* **html**


Then the server should attempt to return an HTML document describing the
problem along with the appropriate HTTP status code.

## File Access Responses

If the datasource name resolves to a simple file on the server that the
server does not recognize as **data**, then the server should return the
file. If possible it should set the HTTP headers to correctly reflect
the type of the returned file.

If the datasource name resolves to a simple file on the server that the
server recognizes as **data**, then the server should return the file if
and only if it is specifically enabled to allow direct access to source
data files.

**Errors**


If the server encounters an error while making or sending the file
response then it should attempt to return an HTML document describing
the problem along with the appropriate HTTP status code.

## THREDDS Responses

There are two Handlers that are used to provide THREDDS catalogs for
Hyrax. One handles local static catalog content (found on the filesystem
where the OLFS is running) and the other provides THREDDS catalogs for
BES holdings.

The THREDDS catalog response is one of the mechanisms for users to
navigate (or "browse" is you will) the hierarchy of data sets available
on the OPeNDAP server. Each collection of of data sets and child
collections will be returned as an (HTML) web paged that can be rendered
by a web browser application.

The THREDDS catalog response is invoked when one of the following
conditions is met:

- The datasource name ends with "**/catalog**" and the request suffix is
  equal to "**html**" will cause the server to return an HTML version of
  the catalog for a browser to render.

2\. The datasource name ends with "**/catalog**" and the request suffix
is equal to "**xml**" will cause the server to return the machine
readable catalog in XML form.

**Errors**


If the request is identified as an THREDDS catalog request and it cannot
be fulfilled then an web page describing the problem along with the
appropriate HTTP status code will be returned to the client.

# SOAP Request Dispatch

The SOAP dispatch process is much simpler. Currently Hyrax is the only
OPeNDAP server that supports the (prototype) SOAP interface. Since SOAP
is a messaging routine that relies on the HTTP POST command to send a
request message to the server. This messaging protocol allows the client
to ask for specific data products in a manner that doesn't require the
deconstruction of the request URL. The details of the prototype SOAP
inteface can be found
**[HERE](http://rsg.opendap.org:8090/server-4/templates/soapAPI.html)**