## BES XML Command syntax.

The BES will accept commands encoded in XML documents (BES XML
Commands), and provide responses in kind. Some requests specifically
indicate a non XML response (such as a DAP2 binary response) in which
case the response will be as requested.

### Requests

All elements mentioned in the following are in the
<http://xml.opendap.org/ns/bes/1.0#> namespace unless otherwise noted.

1.  Each request document **must** have a <request> root element.
2.  Each <request> **must** contain one or more BesCommand elements.
3.  A <request> **may** contain multiple BesCommands as long as zero
    more of those commands returns a response.
    Examples (expand with abbreviated xml):
    - we can do a set context, set container, define and a get das in
      the same request document as only the get das request command
      returns a response.
    - There can not be two show commands within the request document, or
      a show and a get, or multiple gets.
4.  . Each request element **must** have an attribute *reqID* the value
    of which will be used in the response document. There is no
    guarantee that the value of *reqID* be unique within the operational
    domain of the BES. (It might be unique within the software of the
    requesting client, but that's of no concern to the BES).

### Responses

Need a description and such here.

## BES Error Response

<BES>
`    `<response reqID="####">
`        `<BESError>
`            `<Type>`3`</Type>
`            `<Message>`Unable to find command for showVersions`</Message>
`            `<Administrator>`ndp@opendap.org`</Administrator>
`        `</BESError>
`    `</response>
</BES>

Where Type is one of the following:

- 1\. Internal Error - the error is internal to the BES Server
- 2\. Internal Fatal Error - error is fatal, can not continue
- 3\. Syntax User Error - the requester has a syntax error in request or
  config
- 4\. Forbidden Error - the requester is forbidden to see the resource
- 5\. Not Found Error - the resource can not be found

If debugging is enabled during build then the Error object will include
the file name and line number where the exception was thrown.

## BES Command Set

### setContext

Example:

`<setContext  name="contextName>Value`</setContext>

Changes the state of the BES for the current client connection. This
allows the client to ask the BES to utilize various response formats.

#### *name* attribute

Identifies which context value is being set.

**dap_format** context


Value:

- *Major.Minor* where both *Major* and *Minor* are integer values.

**errors** context


Current Values:

- *xml* -
- *dap2* - When error context is set to *dap2* then all errors will
  returned as DAP2 error objects (definitely **not** XML).

<!-- -->


Proposed Values:

- *dap* - When error context is set to *dap* then all errors will
  returned as DAP error objects. The version of the DAP that error must
  conform to is controlled by the dap_format context. It is possible
  (likely) that in the future DAP errors will be XML documents.
- *bes* - Returns a BES Error response XML Document:

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`       `<setContext name="errors">`dap2`</setContext>
`   `</request>

##### Response Example

Normally no response. May return a
[BESError](BES_XML_Commands#BES_Error_Response "wikilink").

------------------------------------------------------------------------

### setContainer

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`       `<setContainer name="c" space="catalog">`data/nc/fnoc1.nc`</setContainer>
`   `</request>

##### Response Example

Normally no response. May return a
[BESError](BES_XML_Commands#BES_Error_Response "wikilink").

------------------------------------------------------------------------

### define

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<define name="d" space="default">
`            `<constraint>`a valid default ce`</constraint>
`            `<container name="c1">
`                `<constraint>`a valid ce`</constraint>
`               `<attributes>`list of attributes`</attributes>
`            `</container>
`            `<container name="c2">
`                `<constraint>`a valid ce`</constraint>
`               `<attributes>`list of attributes`</attributes>
`            `</container>
`            `<aggregate handler="someHandler" cmd="someCommand" />
`        `</define>` `
`   `</request>

Note that for DAP2 the value of the *constraint* element is the value of
the complete DAP2 constraint expression. For DAP4, the constraint
expression is broken into two parts, the *dap4function* and
*dap4constraint*.

See [Hyrax - BES Client
commands](Hyrax_-_BES_Client_commands "wikilink") for complete examples.

##### Response Example

Normally no response. May return a
[BESError](BES_XML_Commands#BES_Error_Response "wikilink").

------------------------------------------------------------------------

### get

**This needs to be expanded to illuminate the missing details from the
previoius command set:**

- get 'type' for 'definition' using 'URL';

Type:

- **dds** -
- **das** -
- **dods** -
- **stream** -
- **ascii** -
- **html_form** -
- **info_page** -

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<get type="data_product" definition="def_name" returnAs="name" url="url" />
`   `</request>

##### Response Example

Explain about DAP2 responses etc...

### Multiple command example

Multiple command transaction resulting in a DDS (non XML DAP2) response:

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`       `<setContext name="error">`dap2`</setContext>
`       `<setContainer name="c" space="catalog">`data/nc/fnoc1.nc`</setContainer>
`        `<define name="d" space="default">
`            `<container name="c">
`                `<constraint>`a valid ce`</constraint>
`               `<attributes>`list of attributes`</attributes>
`            `</container>
`            `<aggregate handler="someHandler" cmd="someCommand" />
`        `</define>` `
`        `<get  type="dds" definition="d" returnAs="name" />
`   `</request>

------------------------------------------------------------------------

### Show version

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<showVersion />
`   `</request>

##### Response

Current:

`   `<showVersion>
`       `<response>
`           `<DAP>
`               `<version>`2.0`</version>
`               `<version>`3.0`</version>
`               `<version>`3.2`</version>
`           `</DAP>
`           `<BES>
`               `<lib>
`                   `<name>`libdap`</name>
`                   `<version>`3.5.3`</version>
`               `</lib>
`               `<lib>
`                   `<name>`bes`</name>
`                   `<version>`3.1.0`</version>
`               `</lib>
`           `</BES>
`           `<Handlers>
`               `<lib>
`                   `<name>`libnc-dods`</name>
`                   `<version>`0.9`</version>
`               `</lib>
`           `</Handlers>
`        `</response>
`   `</showVersion>

Proposed:

<?xml version="1.0" encoding="UTF-8"?>

`   `<response reqID="####">
`       `<showVersion>
`           `<service name="dap">
`               `<version>`2.0`</version>
`               `<version>`3.0`</version>
`               `<version>`3.2`</version>
`           `</service>
`           `<library name="bes">`3.5.3`</library>
`           `<library name="libdap">`3.10.0`</library>
`           `<module name="netcdf_handler">`3.7.9`</module>
`           `<module name="freeform_handler">`3.7.9`</module>
`       `</showVersion>
`   `</response>

------------------------------------------------------------------------

### Show help

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<showHelp />
`   `</request>

##### Response Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<response  reqID="####">
`       `<showHelp>
`           `<module name="bes" version="3.6.2">

<html ns= http://www.w3.org/1999/xhtml >

Help Information

</html>

</module>

`           `<module name="dap" version="3.10.1">`Help Information`</module>
`           `<module name="netcdf_handler" version="3.7.9">`Help Information including supported responses`</module>
`       `</showHelp>
`   `</response>

------------------------------------------------------------------------

### showProcess

This is available only if the BES is compiled in developer mode. A
'production' BES does not support this command.

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<showProcess />
`   `</request>

##### Response Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<response reqID="####">
`       `<showProcess>
`           `<process pid="10831" />
`       `</showProcess>
`   `</response>

------------------------------------------------------------------------

### showConfig

This is available only if the BES is compiled in developer mode. A
'production' BES does not support this command.

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<showConfig />
`   `</request>

##### Response Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<response reqID="####">
`       `<showConfig>
`           `<file>`/Users/pwest/opendap/chunking/etc/bes/bes.conf`</file>
`           `<key name="BES.CacheDir">`/tmp`</key>
`           ....`
`       `</showConfig>
`   `</response>

------------------------------------------------------------------------

### showStatus

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<showStatus />
`   `</request>

##### Response Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<response reqID="####">
`       `<showStatus>
`           `<status>`MST Thu Dec 18 11:51:36 2008`</status>
`       `</showStatus>
`   `</response>

------------------------------------------------------------------------

### showContainers

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<showContainers />
`   `</request>

##### Response Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<response  reqID="####">
`       `<showContainers>
`           `<store name="volatile">
`               `<container name="c" type="nc">`data/nc/fnoc1.nc`</container>
`           `</store>
`       `</showContainers>
`   `</response>

------------------------------------------------------------------------

### deleteContainer(s), deleteDefinition(s)

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<deleteContainers store="storeName" />
`        `<deleteContainer name="containerName" store="storeName" />
`        `<deleteDefinitions store="storeName" />
`        `<deleteDefinition name="defName" store="storeName" />
`   `</request>

##### Response Example

Normally no response. May return a
[BESError](BES_XML_Commands#BES_Error_Response "wikilink").

------------------------------------------------------------------------

### showDefinitions

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<showDefinitions />
`   `</request>

##### Response Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<response  reqID="####">
`       `<showDefinitions>
`           `<store name="volatile">
`               `<definition name="d">
`                   `<container name="c" type="nc" constraint="">`data/nc/fnoc1.nc`</container>
`                   `<aggregation handler="agg">`aggregation_command`</aggregation>
`               `</definition>
`           `</store>
`       `</showDefinitions>
`   `</response>

------------------------------------------------------------------------

### showContext

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID ="####" >
`        `<showContext />
`   `</request>

##### Response Example

<?xml version="1.0" encoding="UTF-8"?>

`  `<response reqID ="####" >
`       `<showContext>
`            `<context name="name1">`value1`</context>
`            `<context name="name2">`value2`</context>
`             ...`
`            `<context name="namen">`valuen`</context>
`       `<showContext>
`  `</response>

------------------------------------------------------------------------

### showCatalog

##### Request

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID="####" >
`        `<showCatalog node="[catalog:]nodeName" />
`   `</request>

The catalog name is optional, defaulting to the default catalog
specified in the BES configuration file. So if you had a catalog named
rdh you could specify node="rdh:/" and it would give you the root node
for the rdh catalog.

##### Response

<?xml version="1.0" encoding="UTF-8"?>

`   `<response reqID="####" >
`       `<showCatalog>
`           `<dataset name="nc/test" size="408" lastModified="2006-01-04T19:48:24" catalog="catalog" node="true" count="5">
`               `<dataset name="test.nc" size="12148" lastModified="2005-09-29T16:31:28" node="false">
`                   `<service>`DAP`</service>
`               `</dataset>
`               `<dataset name="testfile.nc" size="43392" lastModified="2005-09-29T16:31:28" catalog="catalog" node="false">
`                   `<service>`DAP`</service>
`               `</dataset>
`               `<dataset name="TestPat.nc" size="262452" lastModified="2005-09-29T16:31:27" catalog="catalog" node="false">
`                   `<service>`DAP`</service>
`               `</dataset>
`               `<dataset name="TestPatDbl.nc" size="2097464" lastModified="2005-09-29T16:31:28" catalog="catalog" node="false">
`                   `<service>`DAP`</service>
`               `</dataset>
`               `<dataset name="TestPatFlt.nc" size="1048884" lastModified="2005-09-29T16:31:27" catalog="catalog" node="false">
`                   `<service>`DAP`</service>
`               `</dataset>
`           `</dataset>
`       `</showCatalog>
`   `</response>

------------------------------------------------------------------------

### showInfo

##### Request

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID="####" >
`        <showInfo node="nodeName />`
`   `</request>

##### Current Response

<?xml version="1.0" encoding="UTF-8"?>

`   `<response reqID="####">
`       `<showInfo>
`           `<dataset thredds_container="true">
`               `<name>`nc/test`</name>
`               `<size>`408`</size>
`               `<lastmodified>
`                   `<date>`2006-01-04`</date>
`                   `<time>`19:48:24`</time>
`               `</lastmodified>
`               `<count>`5`</count>
`           `</dataset>
`       `</showInfo>
`   `</response>

<?xml version="1.0" encoding="UTF-8"?>

`   `<response reqID="####">
`       `<showInfo>
`           `<dataset thredds_container="false">
`               `<name>`nc/test/TestPatFlt.nc`</name>
`               `<size>`1048884`</size>
`               `<lastmodified>
`                   `<date>`2005-09-29`</date>
`                   `<time>`16:31:27`</time>
`               `</lastmodified>
`           `</dataset>
`       `</showInfo>
`   `</response>

##### Proposed Response

`   `<dataset name="testfile.nc" size="43392" lastModified="YYYY-MM-DDThh:mm:ss" catalog="catalog"
                     node="true|false" count="#ofChildDatSets">
`       `<service>`DAP`</service>
`   `</dataset>

### showServices

##### Request

<?xml version="1.0" encoding="UTF-8"?>

`   `<request reqID="####" >
`        `<showServiceDescriptions />
`   `</request>

##### Response

<?xml version="1.0" encoding="UTF-8"?>

`   `<response reqID="####">
`       `<showServices>
`           `<service name="DAP">
`               `<command name="ddx">
`                   `<description>`Words For Humans`</description>
`                   `<format name="dap2"/>
`               `</command>
`               `<command name="dds">
`                   `<description>`Words For Humans`</description>
`                   `<format name="dap2"/>
`               `</command>
`           `</service>
`       `</showServices>
`   `</response>

[BES XML Commands](Category:Development "wikilink") [BES XML
Commands](Category:Hyrax_Development "wikilink")