[Back to Hyrax For Developers](Hyrax#For_Developers: "wikilink")

These are the commands that the BES supports. Documented here are the
XML versions of the commands that are typed into the *bescmdln* client.
All of these have a non-XML version as well that might be easier to type
as the command line. However, if you're making command files, these are
often the easiest to use because the SQL-like syntax of the 'text'
commands can be confusing.

If you want to find documentation on the XML document that the BES
expects to receive, look at the [BES XML
Commands](BES_XML_Commands "wikilink") documentation. There you'll see
that the commands listed here are generally sent as given to the
*bescmdln* client but embedded in other XML that provides the BES with
information such as a request ID and other bookkeeping information.

## Current core commands available with BES

NB: The BES supports both XML and a SQL-like syntax. Here we attempt to
document both.

- <showHelp /> or *show help;*

:\* shows this help

- <showVersion /> or *show version;*

:\* shows the version of OPeNDAP and each data type served by this
server

- <showProcess /> or *show process;*

:\* shows the process number of this application. This command is only
available in developer mode.

- <showConfig /> or *show config;*

:\* shows all key/value pairs defined in the bes configuration file.
This command is only available in developer mode.

- <showStatus /> or *show status;*

:\* shows the status of the server

- <showContainers /> or *show containers;*

:\* shows all containers currently defined

- <showDefinitions /> or *show definitions;*

:\* shows all definitions currently defined

- <showContext /> or *show context;*

:\* shows all context name/value pairs set in the BES

- <setContainer name="container_name" space="store_name" type="data_type">real_name</setContainer>
  or *set container in catalog values **c**,**/data/nc/fnoc1.nc**;*

:\* defines a symbolic name representing a data container, usually a
file, to be used by definitions, described below

:\* the space property is the name of the container storage and is
optional. Defaults to default volatile storage. Examples might include
database storage, volatile storage based on catalog information.

:\* real_name is the full qualified location of the data container, for
example the full path to a data file.

:\* data_type is the type of data that is in the dataset. For netcdf
files it is nc. For some container storage the data type is optional,
determined by the container storage.

- <setContext name="context_name">context_value</setContext>

:\* set the given context with the given value. No default context are
used in the BES

- \<define ...\>

``` xml
 <define name="definition_name" space="store_name">
     <container name="container_name">
         <constraint>legal_constraint</constraint>
         <attributes>attribute_list</attributes>
     </container>
     <aggregate handler="someHandler" cmd="someCommand" />
 </define>
```

:\* creates a definition using one or more containers, constraints for
each of the containers, attributes to be retrieved from each container,
and an aggregation. Constraints, attributes, and aggregation are all
optional.

:\* There can be more than one container element

:\* space is the name of the definition storage. Defaults to volatile
storage. Examples might include database storage.

- <deleteContainer name="container_name" space="store_name" />

:\* deletes the specified container from the specified container storage
(defaults to volatile storage).

- <deleteContainers space="store_name" />

:\* deletes all of the currently defined containers from the specified
container storage (defaults to volatile storage).

- <deleteDefinition name="definition_name" space="store_name" />

:\* deletes the specified definition from the specified container
storage (defaults to volatile storage).

- <deleteDefinitions space="store_name" />

:\* deletes all of the currently defined definitions from the specified
container storage (defaults to volatile storage).

## Added commands for dap enabled servers

If you are serving up OPeNDAP data responses (DAS, DDS, DataDDS) then
you will have loaded the dap commands in your configuration file. Here
are the available commands in the dap module.

- <showCatalog node="node_name" /> or *show catalog;* or *show catalog
  for \<<node_name>\>;*

:\* Shows catalog information, including contents if a container. If
node is not specified then the root node information is returned. If
node is specified then that nodes information is returned.

- <showInfo node="node_name" />

:\* Shows catalog information for just that node, the root node if no
node is specified. If the node is a container the contents are not
displayed.

- <get type="das | dds | dods | ddx |  dataddx | ascii" definition="def_name" returnAs="returnAs" />

:\* dds: request the data descriptor structure. Returned as text.

:\* das: request the data attributes. Returned as text.

:\* dods: request for the data stream, this output is an octet binary
stream made up of two parts and similar to a multipart MIME document
(but not a real MPM doc). The first part is the DDS that describes the
contents of this response; the separator is the text *Data:*; and the
data make up the third part. The data are represented using XDR-encoded
binary values. There is a a one-to-one mapping between variables, name
and types in the first part and the binary values in the second part. A
library such as libdap can easily decode this response.

:\* ddx: request the data attributes and data descriptor structure
returned as an xml document

:\* dataddx: This is the 'DAP4' counterpart to the *dods* response, just
as the *ddx* is the DAP4 counterpart to the *das* and *dds* responses
from DAP2. The dataddx response is a true multipart MIME document with
the first part a text/xml section that holds the ddx that describes the
data in the response and the second part an application/octet-stream
section that holds the matching XDR-encoded values.

:\* ascii: request the data stream (i.e., dods) and then pass that
through a formatter to generate an ASCII representation of the data and
return it in a text/plain MIME document.

- <setContext name="errors">dap2 \| xml \| html \| txt</setContext>

:\* set the context 'errors' to dap2 in order to have all exceptions and
errors formatted as dap2 error messages in the response.

- <setContext name="dap_format">dap2</setContext>

:\* set the context 'dap_format' to dap2 in order to suppress the
addition of an additional structure to the DDS/DDX whose elements are
the containers named in the setContainer element.

### Complete command examples

DAP2 and DAP4 can both be retrieved from the BES using the same basic
syntax, with some twists.

##### DAP4 example with both a *function* and a *constraint*

Note: The *bes* namespace is not needed if this is being fed into the
BES command processor.

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:27:bes_request]">
  <bes:setContext name="xdap_accept">4.0</bes:setContext>
  <bes:setContext name="dap_explicit_containers">no</bes:setContext>
  <bes:setContext name="errors">xml</bes:setContext>
  <bes:setContext name="max_response_size">0</bes:setContext>

  <bes:setContainer name="c" space="catalog">/functions/tests/data/test_array_1.dmr</bes:setContainer>

  <bes:define name="d1" space="default">
    <bes:container name="c">
        <dap4function>linear_scale(x,0.1,0)</dap4function>
        <dap4constraint>x[1:3]</dap4constraint>
    </bes:container>
  </bes:define>

  <bes:get type="dap" definition="d1" />

</bes:request>
```

### DAP2 example with a *constraint*

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:27:bes_request]">
  <bes:setContext name="dap_explicit_containers">no</bes:setContext>
  <bes:setContext name="errors">xml</bes:setContext>
  <bes:setContext name="max_response_size">0</bes:setContext>

  <bes:setContainer name="c" space="catalog">/functions/tests/data/test_array_1.dmr</bes:setContainer>

  <bes:define name="d1" space="default">
    <bes:container name="c">
        <constraint>x[1:3]</constraint>
    </bes:container>
  </bes:define>

  <bes:get type="dods" definition="d1" />

</bes:request>
```

## Using the *bescmdln* client to test the BES

Here are some tricks/command sequences that are useful when you need to
test the BES without using a web browser. This section assumes that the
DAP commands have been loaded into the BES. In this section, the
examples use the older syntax because it's a bit more amenable to a
command line environment. With the XML syntax, multiple commands cab be
grouped together and sent to the BES in one shot.

Find the versions of all the installed and running modules: *show version;*
Show the status os the BES: *show status;*
Poke around in the RootDirectory to see what's actually visible to the BES: *show catalog;* will show you the root catalog; *show catalog for **"pathname"**;* will show the contents of **"pathname"** (e.g., *show catalog for "/data/nc";* will show all the stuff in the /data/nc directory).
Get the BES to return a DAP response object: You need three commands to do this:

:;bind the dataset to a container in a catalog: *set container in
catalog values **c**,**/data/nc/feb.nc**;*

:;make a definition so you can access that container: *define **d** as
**c**;*

:;a definition with a constraint: *define **d** as **c** with
**c.constraint="lat"**;*

:;request a particular response: *get **ddx** for **d**;*

\*Note that there is a *set container* command but that does not use the
default catalog while the command here explicitly binds the dataset to a
container in the default catalog (which is called *catalog*). This
pathname is rooted in the directory set using the
*BES.Catalog.catalog.RootDirectory* configuration parameter in the
*bes.conf* file. The 'plain' *set container ...* command uses pathnames
rooted in the directory name by the *BES.Data.RootDirectory* parameter,
which is often null for Hyrax installations.

[BES Client Commands](Category:Development "wikilink") [BES Client
Commands](Category:Hyrax_Development "wikilink")