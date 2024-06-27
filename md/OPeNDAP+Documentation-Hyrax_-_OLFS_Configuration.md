## Overview

This document should help you get started configuring the OLFS web
application component of Hyrax. This software package was developed,
compiled, and tested using the java 1.6.x compiler, the 1.6.x Java
Virtual Machine, and Jakarta Tomcat 7.x.x (which also provided the
javax.servlet packages).

The OLFS web application is composed of these servlets:

- **Hyrax servlet** - The Hyrax servlet provides DAP (and other)
  services for the Hyrax server. The Hyrax servlet does the majority of
  the work in the OLFS web application. It does this by providing a
  flexible "dispatch" mechanism through which incoming requests are
  evaluated by a series of DispatchHandlers (pieces of software) that
  can choose to handle the request, or ignore it. The OLFS ships with a
  standard set of DispathHandlers which handle requests for OPeNDAP data
  products, THREDDS catalogs, and OPeNDAP directories. These defalut
  DispatchHandlers can be augmented by adding custom handlers without
  the need to recompile the software. All of the DispatchHandlers used
  by the Hyrax servlet are identified in the **olfs.xml** configuration
  file.
- **Viewers servlet** - The Viewers servlet provides a service for
  datasets through which Web Services and Java WebStart applications
  that might be used with the dataset are identified. The Viewers
  servlet is configured via the **viewers.xml** file.
- **Docs servlet** - The Docs servlet provides clients access to a tree
  of static documents. By default a minimal set of documents are
  provided (containing information about Hyrax), these can be replaced
  by user supplied documents and images. By changing the images and
  documents available through the Docs servlet the data provider can
  further customize the appearance and layout of the Hyrax server web
  pages to better conform with their parent organizations visual
  identity. The Docs servlet has no specific configuration file.
- [**Admin Interface
  servlet**](Hyrax_-_Administrators_Interface "wikilink") - The Hyrax
  Administration Interface (HAI) provides server administrators with a
  GUI for monitoring, controlling, and configuring the server. [The
  Admin Interface is documented
  here.](Hyrax_-_Administrators_Interface "wikilink")
- [**Gateway servlet**](BES_-_Modules_-_Gateway_Module "wikilink") -
  Provides a gateway service that allows Hyrax to be configured to
  retrieve files (that the server recognizes as data) from the web and
  then provide DAP services for those files once they are retrieved. The
  Gateway servlet does not require additional configuration, only that
  the BES be correctly configured to perform gateway tasks. [The BES
  Gateway Module is documented
  here.](BES_-_Modules_-_Gateway_Module "wikilink")

Additionally the OLFS web application relies on one or more instances of
the [BES](http://docs.opendap.org/index.php/Hyrax_-_BES_Configuration)
to provide it with data access and basic catalog metadata.

The OLFS web application stores it's configuration state in a number of
files. The server's configuration is altered by carefully modifying the
content of one or more of these files and then restarting the web
application (or simply restarting Tomcat).

The remainder of this document is concerned with how to correctly
configure the Hyrax and Viewers servlets - the primary components of the
OLFS web application.

## OLFS Configuration Location

Configuration Location

Beginning with olfs-1.15.0 (part of hyrax-1.13.0) the OLFS will use the
following procedure to locating it's configuration:

1.  It will first look at the value of the user environment variable
    `OLFS_CONFIG_DIR`. If the variable is set and it's value is the path
    name of an existing directory that is readable by Tomcat, it is
    used. Otherwise,
2.  If the directory `/etc/olfs` exists and is readable it will use
    that. Otherwise,
3.  It will utilize the default configuration bundled with in the web
    application web archive file (opendap.war).

In this way the OLFS can start without a persistent local configuration.
If the default configuration works for your intended use then there is
no need create a persistent localized configuration. If changes need to
be made to the configuration then it is **strongly recommended** that
the user enable the use of a persistent local configuration. This way
updating the web application won't destroy your changes. This is easily
done by creating an empty directory and identifying it with the
`OLFS_CONFIG_DIR` environment variable. For example:

`export OLFS_CONFIG_DIR="/home/tomcat/hyrax"`

Alternatively, you can create the directory `/etc/olfs`, and ensure that
it is both readable and writeable by Tomcat.

Once the directory is created (and in the former case the environment
variable is set) restart the OLFS (Tomcat) this will cause the OLFS to
move a copy of it's default configuration into the empty directory and
then utilize it. You can then edit the local copy.

### Retired

In olfs-1.14.1 (part of hyrax-1.12.2) and earlier the OLFS web
application was located in the 'persistent content directory':
*\$CATALINA_HOME/content/opendap*. This caused bootstrap problems when
the OLFS tried to set itself up on a Linux system in which the Tomcat
installation had been done via RPM.

## OLFS Files

The OLFS web application gets its configuration from 4 files. In general
all of your configuration need will be met by making changes to the
first two: **olfs.xml** and **catalog.xml**



<!-- -->

**olfs.xml**
***role:*** Contains the localized OLFS configuration - location of the
BES(s), directory view instructions, etc.

***location:*** In the persistent content directory which by default is
located at *\$CATALINA_HOME/content/opendap/*

<!-- -->

**catalog.xml**
***role:*** Master(top-level) THREDDS catalog content for static THREDDS
catalogs.

***location:*** In the persistent content directory which by default is
located at *\$CATALINA_HOME/content/opendap/*

<!-- -->

**viewers.xml**
***role:*** Contains the localized Viewers configuration.

***location:*** In the persistent content directory which by default is
located at *\$CATALINA_HOME/content/opendap/*

<!-- -->

**web.xml**
***role:*** Core servlet configuration.

***location:*** The servlet's web.xml file located in the WEB-INF
directory of the web application "opendap". Typically that means
*\$CATALINA_HOME/webapps/opendap/WEB-INF/web.xml*

<!-- -->

**log4j.xml**
***role:*** Contains the logging configuration for Hyrax.

***location:*** The default location for the *log4j.xml* is in the
WEB-INF directory of the web application "opendap". Typically that means
*\$CATALINA_HOME/webapps/opendap/WEB-INF/log4j.xml* However, Hyrax can
be configured to look in additional places for the *log4j.xml* file.
[Read More About It Here](Hyrax_-_Logging_Configuration "wikilink").

## Hyrax Servlet Configuration

The Hyrax servlet is the front end (public interface) for
[Hyrax](Hyrax "wikilink"). It provides DAP services, THREDDS catalogs,
directory views, logging, and authentication services. This is
accomplished through a collection of software components called
DispatchHandlers. At startup the Hyrax servlet reads the **olfs.xml**
file which contains a list of DispatchHandlers and their configurations.
DispatchHandlers on the list are loaded, configured/initialized, and
then used to provide the aforementioned services.

### Dispatch Handlers

Request dispatch is the process in through which the OLFS determines
what actual piece of code is going to respond to a given incoming
request. This version of the OLFS handles each incoming request by
offering the request to a series of DispatchHandlers. Each
DispatchHandler is asked if it can handle the request. The first
DispatchHandler to say that it can handle the request is then asked to
do so. The OLFS creates an ordered list of DispatchHandlers objects in
memory by reading the **olfs.xml**.

The order of the list is significant. There is no guarantee that two (or
more) DispatchHandlers may claim a particular request. Since the first
DispatchHandler in the list to claim a request gets to service it,
changing the order of the DispatchHandlers can change the behaviour of
the OLFS (and thus of Hyrax). For example the URL:

[`http://localhost:8080/opendap/data/`](http://localhost:8080/opendap/data/)` `

Is recognized by both the *DirectoryDispatchHandler* and the
*ThreddsDispatchHandler* as a request for a directory view: both can
provide a such a view. However, only the *DirectoryDispatchHandler* can
be configured to not claim the request and pass it on for another
handler (in this case the *ThreddsDispatchHandler*) to pickup. The
result is that if you put the *ThreddsDispatchHandler* prior to the
*DirectoryDispatchHandler* on the list there will be no possible way to
get OPeNDAP directory view - the *ThreddsDispatchHandler* will claim
them all.

The usefulness of this dispatching scheme is that it creates
extensibility. If a third party wishes to add new functionality to Hyrax
one way is to write a DispatchHandler. To incorporate it into Hyrax they
only need to add it to the list in the **olfs.xml** and add the java
classes to the Tomcat lib directory.

### **olfs.xml** Configuration File

The **olfs.xml** file contains the core configuration of the Hyrax
servlet:

- Configures the BESManager with at least one BES to be used by the OLFS
  web application
- Identifies all of the DispatchHandlers to be used by the Hyrax
  servlet.
- Controls both view and access behaviours of the Hyrax servlet.

### OLFSConfig element

The \<*OLFSConfig*\> element is the document root and it contains two
elements that suppy the configuration for the OLFS: \<*BesManager*\> and
\<*DispatchHandlers* \>

### **<BESManager>** element (required)

The BESManager element provides configuration for the BESManager class.
The BESManager is used various parts of the OLFS web application
whenever the software needs to access BES(s) services. This
configuration is key to the function of Hyrax. In it each BES that is
connected to a Hyrax installation is defined. The following examples
will show a single BES example. **[For more information on configuring
Hyrax to use multiple BES's look
here.](Hyrax_-_Configuring_The_OLFS_To_Work_With_Multiple_BES's "wikilink")**

Each BES is identified using a seperate \<*BES*\> child element inside
of the \<*BESManager*\> element.

#### **<BES>** element (required)

The \<*BES*\> element provides the OLFS with connection and control
information for a BES. There are 4 child elements in a \<*BES*\>
element: \<*prefix*\>, \<*host*\>, \<*port*\>, and \<*ClientPool*\>

#### **<prefix>** element (required)

This child element of the \<*BES*\> element contains the URL prefix that
the OLFS will associate with this BES. This provides a mapping bewteen
this BES to the URI space serviced by the OLFS. Essentailly what this
means is that the prefix is a token that is placed between the
*host:port/context/* part of the Hyrax URL and the catalog root used to
designate a particular BES instance in the case that multiple BES's are
available to a single OLFS. For a single BES (the default configuration)
the tag MUST be designated by "/". The prefix is used to provide a
mapping for each BES connected to the OLFS to URI space serviced by the
OLFS.

1.  There **must** one (but only one) BES element in the BESManager
    handler configuration whose prefix has a value of "/" (see *example
    1*). There may be more than one \<*BES*\> but there must be at least
    that one.
2.  For a single BES (the one with "/" as it's prefix) no additional
    effort is required. However, when using multiple BES's it is
    neccesary that each BES have a mount point exposed as a directory
    (aka collection) in URI space where it's going to appear. See
    [Configuring With Multiple
    BES's](Hyrax_-_Configuring_The_OLFS_To_Work_With_Multiple_BES's "wikilink")
    for more information.
3.  The prefix string **must** always begin with the slash ("/")
    character. (see *example 2*)

*example 1:*

``` xml
 <prefix>/</prefix>
```

*example 2:*

``` xml
 <prefix>/data/nc</prefix>
```

#### **<host>** element (required)

This child element of the \<*BES*\> element contains the host name or IP
address of the BES.

*example:*

``` xml
<host>test.opendap.org</host >
```

#### **<port>** element (required)

This child element of the \<*BES*\> element contains port number on
which the BES is listening.

*example:*

``` xml
<port>10022</port >
```

#### **<timeOut>** element (optional)

This child element of the \<*BES*\> element contains the timeout time,
in seconds, for the OLFS to wait for this BESto respond. Defaults to 300
seconds.

*example:*

``` xml
<timeOut>600</timeOut >
```

#### **<maxResponseSize>** element (optional)

This child element of the \<*BES*\> element contains the maximum
response size, in bytes, allowed for this BES. Requests that produce a
larger response will receive an error response. A value of zero, *0*,
indicates that there is no imposed limit. The default value is 0.

*example:*

``` xml
<maxResponseSize>0</maxResponseSize>
```

#### **<ClientPool>** element (optional)

This child element of the \<*BES*\> element configures the behavior of
the pool of client connections that the OLFS maintains with this
particular BES. These connections are pooled for efficiency and speed.
Currently the only configuration item available is to control the
maximum number of concurrent BES client connections that the OLFS may
make, the default is 200, but the size should be optimized for your
locale by empirical testing. The size of the Client Pool is controlled
by the *maximum* attribute. The default value of *maximum* is 200.

*example:*

``` xml
<ClientPool maximum="17" />
```

If the <ClientPool> element is missing the pool size defaults to 200.

#### **<adminPort>** element (optional)

This child element of the \<*BES*\> element contains the port on the BES
system that can be used by the Hyrax Admin Interface to control the BES.
THe BES must also be configured to open and utilize this admin port.

*example:*

``` xml
<adminPort>11002</adminPort>
```

#### Example BESManager configuration element

``` xml
<BESManager>
    <BES>
        <prefix>/</prefix>
        <host>localhost</host>
        <port>10022</port>
        <timeOut>300</timeOut>
        <maxResponseSize>0</maxResponseSize>
        <ClientPool maximum="10" maxCmds="2000" />
        <adminPort>11002</adminPort>
    </BES>
</BESManager >
```

### **<CatalogCache>** element

The catalog cache element configures the OLFS memory cache of BES
catalog responses. This cache can greatly increase server performance
for small requests. It is configured by it's two child elements,
`maxEntries` and `updateIntervalSeconds`.

- The value of `maxEntries` determines the total number of catalog
  responses to hold in memory. The default value for `maxEntries` is
  10000.
- The value of `updateIntervalSeconds` determines how long the catalog
  update thread will sleep between updates. This value affects the
  servers responsiveness to changes in it's holdings. If your servers
  contents change frequently, then the `updateIntervalSeconds` should be
  set to a value that will allow the server to publish new
  additions/deletions in a timely manner. The `updateIntervalSeconds`
  default value 10000 seconds (2.7 hours).
- If for some reason you which to disable the `CatalogCache`, simply
  remove (or comment out) the `CatalogCache` element and it's children
  from the `olfs.xml` file.

### **<DispatchHandlers>** element

The \<*DispatchHandlers*\> element has two child elements:
\<*HttpGetHandlers*\> and \<*HttpPostHandlers*\>. The
\<*HttpGetHandlers*\> contains and ordered list of the DispatchHandler
classes used by the OLFS to handle incoming HTTP GET requests.

### **<HttpGetHandlers>** element

The \<*HttpGetHandlers*\> contains and ordered list of the
DispatchHandler classes used by the OLFS to handle incoming HTTP GET
requests. The list order is significant, and permutating the order will
(probably negatively) change the behavior of the OLFS. Each
DispatchHandler on the list will be asked to handle the request. The
first DispatchHandler on the list to claim the request will be asked to
build the response.

### **<HttpPostHandlers>** element

While programmatic support for POST request handlers is part of the
Hyrax servlet there are currently no HttpPostHandlers implemented for
use with Hyrax. Maybe down the road…

### **<Handler>** elements

Both the \<*HttpGetHandlers*\> and \<*HttpPostHandlers*\> contain an
orderd list of \<*Handler*\> elements. Each \<*Handler*\> must have an
attribute call *className* whose value is set to the fully qualified
Java class name for the DispatchHandler implementation to be used. For
example:

``` xml
  <Handler className="opendap.bes.VersionDispatchHandler" />
```

Names the class *opendap.bes.VersionDispatchHandler*.

Each \<*Handler*\> element may contain a collection of child elements
that provide configuration information to the DispatchHandler
implementation. In this example:

``` xml
  <Handler className="opendap.coreServlet.BotBlocker">
      <IpAddress&>44.55.66.77</IpAddress>
  </Handler>
```

The \<*Handler*\> element contains a child element \<*IpAddress*\> that
indicates to the *BotBlocker* class to block requests from the IP
address 44.55.66.77.

### HTTP GET Handlers

Hyrax uses the following DispatchHandlers to handle HTTP GET requests:

VersionDispatchHandler: Handles the version document requests.
BotBlocker: This optional handler may be used to block access to your server individual IP addressesl or groups of IP addresses.
NcmlDatasetDispatcher:
StaticCatalogDispatch: Provides static THREDDS catalog services for Hyrax.
Gateway:
DapDispatcher: Handles all DAP requests.
DirectoryDispatchHandler: Handles the OPeNDAP directory view (contents.html) requests.
BESThreddsDispatchHandler: Provides dynamic THREDDS catalogs of all BES holdings.
FileDispatchHandlerr: Handles requests for file level access. (README files etc.)

### VersionDispatchHandler (required)

Handles the version document requests. This DispatchHandler has no
configuration elements, so it will always be written like this:

#### Example Configuration Element

``` xml
<Handler className="opendap.bes.VersionDispatchHandler" />
```

### BotBlocker (optional)

This optional handler can be used to block access from specific IP
addresses and by a ranges of IP addresses using regular expressions. It
turns out that many of the web crawling robots do not respect the
robots.txt file when one is provided. Since many sites do not want their
data holdings exhaustively queried by automated software, we created a
simple robot blocking handler to protect system resources from
non-compliant robots.

#### **<IpAddress>** element

The text value of this element should be the IP address of a system
which you would like to block from accessing your service. For example:

``` xml
    <IpAddress>128.193.64.33</IPAddress>
```

Blocks the system located at 128.193.64.33 from accessing your server.
There can be zero or more <IpAddress> elements in the <BotBlocker>

#### **\< IpMatch \>** element

The text value of this element should be the regular expression that
will be used to match the IP addresses clients attempting to access
Hyrax.

For example:

``` xml
    <IpMatch>65\.55\.[012]?\d?\d\.[012]?\d?\d</IpMatch>
```

Matches all IP address beginning with 65.55 and thus block access for
clients whose IP addresses lie in that range. There can be zero or more
\< IpMatch \> elements in the Handler configuration for teh BotBlocker

#### Example Configuration Element

``` xml
    <Handler className="opendap.coreServlet.BotBlocker">

        <IpAddress>127.0.0.1</IpAddress>

        <!-- This matches all IPv4 addresses, work yours out from here.... -->
        <!--<IpMatch>[012]?\d?\d\.[012]?\d?\d\.[012]?\d?\d\.[012]?\d?\d</IpMatch> -->

        <!-- Any IP starting with 65.55 (MSN bots the don't respect robots.txt  -->
        <IpMatch>65\.55\.[012]?\d?\d\.[012]?\d?\d</IpMatch>

    </Handler>
```

### Ncml Dataset Dispatcher (required)

The Ncml Dataset Dispatcher is a specialized handler that filters NcML
content retrieved from the BES so that the path names in the NcML
documents returned to clients are consistent with the paths from the
external (to the server) perspective.

#### Example Configuration Element

``` xml
            <Handler className="opendap.ncml.NcmlDatasetDispatcher" />
```

### Static Thredds Catalog Dispatch Handler (required)

Serves static THREDDS catalogs (i.e. THREDDS catalog files stored on
disk). Provides both a presentation view (HTML) for humans using
browsers, and direct catalog access (XML).

#### **<prefix>** element (required)

Defines the path component that comes after the servlet context and
before all catalog requests. For example, if the prefix is *thredds*,
then <http://localhost:8080/opendap/thredds/> should give you the
top-level static catalog (the contents of the file
\$CATALINA_HOME/content/opendap/catalog.xml)

#### **<useMemoryCache>** element (optional)

If the text value of this element is the string 'true' this will cause
the servlet to ingest all of the static catalog files at startup and
hold their contents in memory. [See this page for more information about
the memory caching operations](THREDDS_using_XSLT "wikilink")

#### **<ingestTransformFile>** element (optional)

This is a specific development option that allows one top specify the
fully qualified path to an XSLT file that will be used to preprocess
each THREDDS catalog file read from disk. The default version of this
file (found in
\$CATALINA_HOME/webapps/opndap/xsl/threddsCatalogIngest.xsl) processes
the *thredds:datasetScan* elements in each THREDDS catalog so that they
contain specific content for Hyrax. **This is a developers option and in
general is not recommended for use in an operational server.**

#### Example Configuration Element

``` xml
<Handler className="opendap.threddsHandler.StaticCatalogDispatch">
     <prefix>thredds</prefix>
     <useMemoryCache>true</useMemoryCache>
</Handler>
```

### Gateway Dispatcher

Directs requests to the [Gateway Service](Gateway_Service "wikilink")

#### **<prefix>** element (required)

Defines the path component that comes after the servlet context and
before all gateway requests. For example, if the prefix is *gateway*,
then <http://localhost:8080/opendap/gateway/> will give you the gateway
access form page.

#### Example Configuration Element

``` xml
<Handler className="opendap.gateway.DispatchHandler">
    <prefix>gateway</prefix>
</Handler>
```

### DapDispatchHandler (required)

Handles DAP request for Hyrax. For example the DapDispatchHandler will
handle requests for all DAP2 and DAP4 products

#### **<AllowDirectDataSourceAccess>** element (optional)

The \<*AllowDirectDataSourceAccess* /\> element controls the users
ability to directly access data sources via the web interface. If this
element is present (and not commented out as in the example below) a
client can get an entire data source (such as an HDF file) by simply
requesting it through the HTTP URL interface. This is NOT a good
practice and is not recommended. By default Hyrax ships with this option
turned off and I recommend that you leave it that way unless you really
want users to be able to circumvent the OPeNDAP request interface and
have direct access to the data products stored on your server.

#### **<UseDAP2ResourceUrlResponse>** element (optional)

By default, at least for now, the server will provide the (undefined)
DAP2 style response to requests for a dataset resource URL. Commenting
out the "UseDAP2ResourceUrlResponse" element will cause the server to
return the (well defined) DAP4 DSR response when a dataset resource URL
is requested.

#### Example Configuration Element

``` xml
<Handler className="opendap.bes.dapResponders.DapDispatcher" >
    <!-- AllowDirectDataSourceAccess / -->
    <UseDAP2ResourceUrlResponse />
</Handler>
```

### DirectoryDispatchHandler (required)

Handles the OPeNDAP directory view (contents.html) requests.

#### Example Configuration Element

``` xml

<Handler className="opendap.bes.DirectoryDispatchHandler" />
```

### BES Thredds Dispatch Handler (required)

Provides dynamic THREDDS catalogs of BES data holdings.

#### Example Configuration Element

``` xml
<Handler className="opendap.bes.BESThreddsDispatchHandler" />
```

### File Dispatch Handler (required)

Handles requests for file level access. (README files etc.). This
handler only responds to requests for files that are not considered
"data" by the BES. File requests for data files are handled by the
*opendap.bes.dapResponders.DapDispatcher*.

#### Example Configuration Element

In the following example, the FileDispatchHandler is configured to deny
direct access to data sources (note that the
\<*AllowDirectDataSourceAccess* /\> element is commented out:

``` xml
<Handler className="opendap.bes.FileDispatchHandler" />
```

### HTTP POST Handlers

Hyrax does not currently support HTTP POST requests.

### Example olfs.xml file

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<OLFSConfig>

    <BESManager>
        <BES>
            <prefix>/</prefix>
            <host>localhost</host>
            <port>10022</port>

            <timeOut>300</timeOut>

            <adminPort>11002</adminPort>

            <maxResponseSize>0</maxResponseSize>
            <ClientPool maximum="200" maxCmds="2000" />
        </BES>
    </BESManager>
    <DispatchHandlers>
        <HttpGetHandlers>

            <Handler className="opendap.bes.VersionDispatchHandler" />

            <Handler className="opendap.coreServlet.BotBlocker">
                <<IpMatch>65\.55\.[012]?\d?\d\.[012]?\d?\d</IpMatch>
            </Handler>


            <Handler className="opendap.ncml.NcmlDatasetDispatcher" />

            <Handler className="opendap.threddsHandler.StaticCatalogDispatch">
                <prefix>thredds</prefix>
                <useMemoryCache>true</useMemoryCache>
            </Handler>

            <Handler className="opendap.gateway.DispatchHandler">
                <prefix>gateway</prefix>
            </Handler>



            <Handler className="opendap.bes.BesDapDispatcher" >
                <!-- AllowDirectDataSourceAccess / -->
                <UseDAP2ResourceUrlResponse />
            </Handler>

            <Handler className="opendap.bes.DirectoryDispatchHandler">
                <!--
                  If your particular authentication scheme (usually brokered by Apache httpd) utilizes
                  a particular logout or login location you can have Hyrax display links to those locations
                  as part of the generated web pages by uncommenting the "AuthenticationControls" element and
                  editing the logout and/or login locations to match your service instance.
                  -->
                <!-- AuthenticationControls>
                    <logout>loginPath?login_param=foo</logout>
                    <logout>logoutPath?logout_param=foo</logout>
                </AuthenticationControls -->
            </Handler>


            <Handler className="opendap.bes.BESThreddsDispatchHandler"/>
            <Handler className="opendap.bes.FileDispatchHandler" />
        </HttpGetHandlers>


        <!--
           If you need to accept a constraint expression (ce) that is larger than will fit in a URL query string then you
           can configure the server to accept the ce as the body of a POST request referencing the same resource.
           If the the Content-Encoding of the request is set to "application/x-www-form-urlencoded" then the server
           will ingest all of parameter names "ce" and "dap4:ce"  to build the DAP constraint expression. Otherwise
           the server will treat the entire POST body as a DAP ce.

           By default the maximum length of the POST body is limited to 2000000 characters, and may never be
           larger than 10000000 characters (if you need more then get in touch with support@opendap.org). You can adjust
           the limit in the configuration for the BesDapDispatcher.

           Configuration:
           Uncomment the HttpPostHandlers element below. Make sure that the body of the BesDapDispatcher Handler element is
           IDENTICAL to it's sister in the HttpGetHandlers element above.

           If you need to change the default value of the maximum POST body length do it by adding a
           "PostBodyMaxLength" element to the BesDapDispatcher Handler below:

           <PostBodyMaxLength>500</PostBodyMaxLength>

           The text content of which must be an integer between 0 and 10000000
        -->
        <!--
        <HttpPostHandlers>
            <Handler className="opendap.bes.dapResponders.BesDapDispatcher" >
                MAKE SURE THAT THE CONTENT OF THIS ELEMENT IS IDENTICAL TO IT'S SISTER IN THE  HttpGetHandlers ELEMENT!
                (Disregarding the presence of a PostBodyMaxLength element)
            </Handler>
        </HttpPostHandlers>
        -->


    </DispatchHandlers>

    <!--
      This enables or disables the generation of internal timing metrics for the OLFS
      If commented out the timing is disabled. If you want timing metrics to be output
      to the log then uncomment the Timer and set the enabled attribute's value to "true"
      WARNING: There is some performance cost to utilizing the Timer.
    -->
    <!-- Timer enabled="false" / -->


</OLFSConfig>
```

## THREDDS configuration **catalog.xml** file

The **catalog.xml** file contains the static THREDDS catalog
configuration for Hyrax. [Read About It
Here](Hyrax_-_THREDDS_Configuration "wikilink").

## Logging configuration (**logback.xml** file)

The **logback.xml** file contains the logging configuration for Hyrax.
[Read About It Here](Hyrax_-_Logging_Configuration "wikilink").

## **web.xml** configuration file

*We strongly recommend that you do **NOT** mess with the web.xml file.
At least for now. Future versions of Server and the OLFS may have "user
configurable" stuff in the web.xml file, but this version does not. **SO
JUST DON'T DO IT. OK?*** Having said that, here are the details
regarding the web.xml file:

#### Servlet Definition

The OLFS running in the opendap context area needs an entry in the
**web.xml** file. Multiple instances of a servlet and/or several
different servlets can be configured in the one web.xml file. For
instance you could have a DTS and a Hyrax running in from the same
**web.xml** and thus under the same servlet context. Running multiple
instances of the OLFS in a single web.xml file (aka context) will
**NOT** work.

Each a servlet needs a unique name which is specified inside a
\<*servlet*\> element in the web.xml file using the \<*servlet-name*\>
tag. This is a name of convenience, for example if you where serving
data from an ARGOS satellite you might call that servlet *argos*.

Additionally each instance of a \<*servlet*\> must specify which Java
class contains the actual servlet to run. This is done in the
\<*servlet-class*\> element. For example the OLFS servlet class name is
*opendap.coreServlet.DispatchServlet*

Here is a syntax example combining the two previous example values:

    <servlet>
        <servlet-name>hyrax</servlet-name>
        <servlet-class>opendap.coreServlet.DispatchServlet</servlet-name>
        .
        .
        .
    </servlet>

This servlet could then be accessed as:
*<http://hostname/opendap/servlet/argos>*

You may also add to the end of the web.xml file a set of
\<*servlet-mapping*\> elements. These allow you to abbreviate the URL or
the servlet. By placing the servlet mappings:

    <servlet-mapping>
        <servlet-name>argos</servlet-name>
        <url-pattern>/argos</url-pattern>
    </servlet-mapping>

    <servlet-mapping>
        <servlet-name>argos</servlet-name>
        <url-pattern>/argos/*</url-pattern>
    </servlet-mapping>

At the end of the web.xml file our previous example changes it's URL to:
*<http://hostname/opendap/argos>*

Eliminating the need for the word servlet in the URL. For more on the
\<*servlet-mapping*\> element see the Jakarta-Tomcat documentation.

#### <init-param> Elements

The OLFS uses <init-param> elements inside of each <servlet> element to
get specific configuration information.

<init-param>'s common to all OPeNDAP servlets are:

##### OLFSConfigFileName

This parameter identifies the name of the XML document file that
contains the OLFS configuration. This file must be located in the
persistent content directory and is typically called **olfs.xml**

For example:

        <init-param>
        <param-name>OLFSConfigFileName</param-name>
        <param-value>olfs.xml</param-value>
        </init-param>

##### DebugOn

This controls output to the terminal from which the servlet engine was
launched. The value is a list of flags that turn on debugging
instrumentation in different parts of the code. Supported values are:

- **probeRequest**: Prints a lengthy inspection of the
  HttpServletRequest object to stdout. *Don't leave this on for long, it
  will clog your Catalina logs.*
- **DebugInterface**: Enables the servers debug interface. This
  ineractive interface allows a user to look at (and change) the server
  state via a web browser. *Enable this only for analysis purposes,
  disable when finshed!*

*Example*:

        <init-param>
        <param-name>DebugOn</param-name>
        <param-value>probeRequest</param-value>
        </init-param>

*Default*: If this parameter is not set, or the value field is empty
then these features will be disabled - which is what you want unless
there is a problem to analyze.

#### Example of web.xml content

    <servlet>

        <servlet-name>hyrax</servlet-name>

        <servlet-class>opendap.coreServlet.DispatchServlet</servlet-class>

        <init-param>
            <param-name>DebugOn</param-name>
            <param-value></param-value>
        </init-param>

        <load-on-startup>1</load-on-startup>

    </servlet>

    <servlet-mapping>
        <servlet-name>hyrax</servlet-name>
        <url-pattern>*</url-pattern>
    </servlet-mapping>

    <servlet-mapping>
        <servlet-name>hyrax</servlet-name>
        <url-pattern>/hyrax</url-pattern>
    </servlet-mapping>

    <servlet-mapping>
        <servlet-name>hyrax</servlet-name>
        <url-pattern>/hyrax/*</url-pattern>
    </servlet-mapping>

## Viewers Servlet (**viewers.xml** file)

The Viewers servlet provides, for each dataset, and HTML page containing
links to Java WebStart applications and to WebServices (such as WMS)
that can be utilized in conjunction with the dataset. The Viewers
servlet is configured via the contents of the **viewers.xml** file
located in the persistent content directory
\$CATALINA_HOME/content/opendap.

### **viewers.xml** configuration file

#### **<JwsHandler>** elements

#### **<WebServiceHandler>** elements

#### Example Configuration

``` xml
<ViewersConfig>

    <JwsHandler className="opendap.webstart.IdvViewerRequestHandler">
        <JnlpFileName>idv.jnlp</JnlpFileName>
    </JwsHandler>

    <JwsHandler className="opendap.webstart.NetCdfToolsViewerRequestHandler">
        <JnlpFileName>idv.jnlp</JnlpFileName>
    </JwsHandler>

    <JwsHandler className="opendap.webstart.AutoplotRequestHandler" />

    <WebServiceHandler className="opendap.viewers.NcWmsService" serviceId="ncWms" >
        <applicationName>Web Mapping Service</applicationName>
        <NcWmsService href="/ncWMS/wms" base="/ncWMS/wms" ncWmsDynamicServiceId="lds" />
    </WebServiceHandler>

    <WebServiceHandler className="opendap.viewers.GodivaWebService" serviceId="godiva" >
        <applicationName>Godiva WMS GUI</applicationName>
        <NcWmsService href="http://localhost:8080/ncWMS/wms" base="/ncWMS/wms" ncWmsDynamicServiceId="lds"/>
        <Godiva href="/ncWMS/godiva2.html" base="/ncWMS/godiva2.html"/>
    </WebServiceHandler>

</ViewersConfig>
```

## Docs Servlet

The Docs (or documentation) servlet provides the OLFS web application
with the ability to serve a tree of static documentation files. By
default it will serve the files in the documentation tree provided with
the OLFS in the Hyrax distribution. This tree is rooted at
*\$CATALINA_HOME/webapps/opendap/docs/* and contains documentation
pertaining to the software in the Hyrax distribution - installation and
configuration instruction, release notes, java docs, etc.

If one wishes to replace this information with their own set of web
pages, one can remove/replace the files in the default directory.
However, installing a new version of Hyrax will cause these files to be
overwritten, forcing them to be replaced after the install (and
hopefully AFTER the new release documentation had been read and
understood by the user).

The Docs servlet provides an alternative to this. If a *docs* directory
is created in the *persistent content* directory for Hyrax the Docs
servlet will detect it (when Tomcat is launched) and it will serve files
from there instead of from the default location.

This scheme provides 2 beneficial effects:

1.  It allows localizations of the web documents associated with Hyrax
    to persist through Hyrax upgrades with no user intervention.
2.  It preserves important release documents that ship with the Hyrax
    software.

In summary, to provide persistent web pages as part of a Hyrax
localization simple create the directory:
*\$CATALINA_HOME/content/opendap/**docs***

Place your content in there and away you go. If later you wish to view
the web based documentation bundled with Hyrax simply change the name of
the directory from **docs** to something else and restart Tomcat. (or,
you could just look in the *\$CATALINA_HOME/webapps/opendap/docs*
directory)

In the Docs servlet, if a URL ends in a directory name or a "/" then the
servlet will attempt to serve the **index.html** in that directory. In
other words **index.html** is the default document.

## [Logging](Hyrax_-_Logging_Configuration "wikilink")

[Logging is a big enough subject we gave it it's own
page.](Hyrax_-_Logging_Configuration "wikilink")

## Authentication & Authorization

### Apache Web Server (httpd)

<font size="3">**If your organization desires secure access and
authentication layers for Hyrax the recommended method is to use Hyrax
in conjunction the Apache Web Server (httpd).**</font>

Most organizations that utilize secure access and authentication for
their web presence are already doing so via Apache Web Server and Hyrax
can be integrated nicely with this existing infrastructure.

More about integrating Hyrax with Apache Web Server can be found at
these pages:

- [Integrating Hyrax with Apache Web
  Server.](Hyrax_-_Apache_Integration "wikilink")
- [Configuring Hyrax and Apache for User Authentication and
  Authorization](Hyrax_-_User_Identification_(Authentication) "wikilink")

### Tomcat

Hyrax may be used with the security features implemented by Tomcat for
authentication and authorization services.

It is recommended that you read carefully and understand the Tomcat
security documentation.

For Tomcat 5.x see:

- [Tomcat 5.x
  Documentation](http://tomcat.apache.org/tomcat-5.5-doc/index.html)
  - [Section 6: Configuring/Managing User
    Realms](http://tomcat.apache.org/tomcat-5.5-doc/realm-howto.html)
  - [Section 12: Configuring
    SSL](http://tomcat.apache.org/tomcat-5.5-doc/ssl-howto.html)

For Tomcat 6.x see:

- [Tomcat 6.x
  Documentation](http://tomcat.apache.org/tomcat-6.0-doc/index.html)
  - [Section 6: Configuring/Managing User
    Realms](http://tomcat.apache.org/tomcat-6.0-doc/realm-howto.html)
  - [Section 12: Configuring
    SSL](http://tomcat.apache.org/tomcat-6.0-doc/ssl-howto.html)

And that you read chapter 12 of the [Java Servlet Specification
2.4](http://jcp.org/aboutJava/communityprocess/final/jsr154/index.html)
that decribes how to configure security constraints at the web
application level.

Tomcat security requires fairly extensive additions to the **web.xml**
file. (It is important to keep in mind that altering the \<*servlet*\>
definitions may render your Hyrax server inoperable - please see the
previous sections that discuss this.)

Examples of security content for the web.xml file can be found in the
persistent content directory of the Hyrax server, which by default is
located at *\$CATALINA_HOME/content/opendap/*.

### Limitations

Officially Tomcat security supports *context* level authentication. What
this means is that you can restrict access to the collection of servlets
running in a single web application - in other words all of the stuff
that is defined in a single **web.xml** file. You can call out different
authentication rules for different \<*url-pattern*\>'s within the web
application, but only clients which do not cache ANY security
information will be able to easily access the different areas.

For example in your **web.xml** file you might have: <font size="2">

``` xml
    <security-constraint>
        <web-resource-collection>
            <web-resource-name>fnoc1</web-resource-name>
            <url-pattern>/hyrax/nc/fnoc1.txt</url-pattern>
        </web-resource-collection>
        <auth-constraint>
            <role-name>fn1</role-name>
        </auth-constraint>
    </security-constraint>

    <security-constraint>
        <web-resource-collection>
             <web-resource-name>fnoc2</web-resource-name>
             <url-pattern>/hyrax/nc/fnoc2.txt</url-pattern>
         </web-resource-collection>
         <auth-constraint>
             <role-name>fn2</role-name>
          </auth-constraint>
    </security-constraint>

    <login-config>
        <auth-method>BASIC</auth-method>
        <realm-name>MyApplicationRealm</realm-name>
    </login-config>
```

</font>

Where the security roles fn1 and fn2 (defined in the
**tomcat-users.xml** file) have no common members.

The complete URI's would be: <font size="2">

[`http://localhost:8080/mycontext/hyrax/nc/fnoc1.txt`](http://localhost:8080/mycontext/hyrax/nc/fnoc1.txt)
[`http://localhost:8080/mycontext/hyrax/nc/fnoc2.txt`](http://localhost:8080/mycontext/hyrax/nc/fnoc2.txt)

</font>

Now - this works, for clients that aren't too smart - i.e. they don't
cache anything. However, if you access these URLs with a typical
browser, once you authenticate for one URI, then you are locked out of
the other one until you successfully "reset" the browser (purge all
caches).

I think the reason is as follows: In the exchange between Tomcat and the
client, Tomcat is sending the header:

<font size="2">`WWW-Authenticate: Basic realm="MyApplicationRealm"`</font>

And the client authenticates. When the second URI is accessed Tomcat
sends the the same authentication challenge, with the same
`WWW-Authenticate` header. The client, having recently authenticated to
this *realm-name* (defined in the \<*login-config*\> element in the
web.xml file - see above), resends the authentication information, and,
since it's not valid for that url pattern, the request is denied.

### Persistence

You should be careful to back up your modified **web.xml** file to a
location outside of the *\$CATALINA_HOME/webapps/opendap* directory as
new versions of Hyrax will overwrite it when installed. You could use an
*XML ENTITY* and an *entity reference* in the **web.xml** to cause a
local file containing the security configuration to be included in the
web.xml. For example adding the *ENTITY*:

<font size="2">
`[<!ENTITY securityConfig SYSTEM "`[`file:/fully/qualified/path/to/your/security/config.xml`](file:/fully/qualified/path/to/your/security/config.xml)`">]`
</font>

To the \<*!DOCTYPE*\> declaration at the top of the **web.xml** in
conjunction with adding an *entity reference*:

<font size="2"> `&securityConfig;` </font>

To the content of the \<*web-app*\> element would cause your external
security configuration to be included in the **web.xml** file.

Here is an example of an *ENTITY* configuration:

<font size="2">

``` xml
    <?xml version="1.0" encoding="ISO-8859-1"?>

    <!DOCTYPE web-app
        PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.2//EN"
        "http://java.sun.com/j2ee/dtds/web-app_2_2.dtd"
        [<!ENTITY securityConfig      SYSTEM "file:/fully/qualified/path/to/your/security/config.xml">]
    >
    <web-app>

        <!--
            Loads a persistent security configuration from the content directory.
            This configuration may be empty, in which case no security constraints will be
            applied by Tomcat.
        -->
        &securityConfig;

        .
        .
        .

    </web-app>

```

</font>

This will not prevent you from losing your **web.xml** file when a new
version of Hyrax is installed, but adding the *ENTITY* stuff to the new
**web.xml** file would be easier than remembering an extensive security
configuration. Of course, Y.M.M.V.

## Compressed Responses and Tomcat

Many OPeNDAP clients accept compressed responses. This can greatly
increase the efficiency of the client/server interaction by diminishing
the number of bytes actually transmitted over "the wire". Tomcat
provides native compression support for the GZIP compression mechanism,
however it is NOT turned on by default.

The following example is based on Tomcat 5.15. We recommend that you
read carefully the Tomcat documentation related to this topic before
proceeding:

- [Tomcat Home](http://tomcat.apache.org/)
- [Tomcat 5.x
  documentation.](http://tomcat.apache.org/tomcat-5.5-doc/index.html)
  (See Reference Section for the Apache Tomcat Configuration section)
- [Tomcat 5.x documentation section related to
  compression.](http://tomcat.apache.org/tomcat-5.5-doc/config/http.html)

### Details

To enable compression you will need to edit the
*\$CATALINA_HOME/conf/server.xml* file. You will need to locate the
\<*Connector*\> element associated with your server, typically this will
be the only \<*Connector*\> element whose *port* attribute is set equal
to 8080. To this you will need to add/change several attributes to
enable compression.

With my Tomcat 5.5 distribution I found this default \<*Connector*\>
element definition in my *server.xml* file: <font size="2">

``` xml
    <Connector port="8080" maxHttpHeaderSize="8192"
        maxThreads="150" minSpareThreads="25" maxSpareThreads="75";
        enableLookups="false" redirectPort="8443" acceptCount="100"
        connectionTimeout="20000" disableUploadTimeout="true"
        compression="no"
     >
```

</font> You will need to add to this four attributes:

<font size="2">

``` xml
compression="force"
compressionMinSize="2048"
noCompressionUserAgents="gozilla, traviata"
compressableMimeType="text/html,text/xml,application/octet-stream"
```

</font>

Notice that there is a list of compressible MIME types. Basically:

- **compression="no"** means nothing gets compressed.
- **compression="yes"** means only the compressible MIME types get
  compressed.
- **compression="force"** means everything gets compressed (assuming the
  client accepts gzip and the response is bigger than
  compressionMinSize)

You MUST set **compression="force"** for compression to work with the
OPeNDAP data transport.

The final result being:

<font size="2">

``` xml
    <Connector port="8080" maxHttpHeaderSize="8192"
        maxThreads="150" minSpareThreads="25" maxSpareThreads="75";
        enableLookups="false" redirectPort="8443" acceptCount="100"
        connectionTimeout="20000" disableUploadTimeout="true"
        compression="no"
        compression="force"
        compressionMinSize="2048"
        noCompressionUserAgents="gozilla, traviata"
        compressableMimeType="text/html,text/xml,application/octet-stream"
     >
```

</font>

Restart Tomcat for these changes to take effect.

<font size="4"> **NOTE: If you are using Tomcat in conjunction with the
Apache Web Server (our friend httpd) via AJP you will need to [configure
Apache to deliver compressed
responses](Hyrax_-_Apache_Integration#Apache_Compressed_Responses "wikilink")
too. Tomcat will not compress content sent over the AJP connection.**
</font>