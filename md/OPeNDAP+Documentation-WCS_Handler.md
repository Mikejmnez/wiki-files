The current implementation of the WCS gateway is hosed. I started
working on a new design.

I started working on a !WcsDispatchHandler and it made me start to think
it might be much natural to make it a module for the BES. I think I can
do it in the OLFS. In fact I am sure I can manage it.

Here's how it could work.

## URL Decomposition

Consider this URL:
**http://server:port/opendap/wcs/project/site/serviceName/coverage/date**

------------------------------------------------------------------------

### Top Level Catalog

URL Pattern:


**http://server:port/opendap/**

**http://server:port/opendap**

Corresponds to the top level catalog in the BES. Some how we need the
**show catalog for "/"** command to return a dataset element for the wcs
module (when it's installed) at the top level:

<showCatalog>
`    `<response>
`        `<dataset isData="false" thredds_collection="true">
`            `<name>`/`</name>
`            `<size>`238`</size>
`            `<lastmodified>
`                `<date>`2008-02-18`</date>
`                `<time>`15:57:53`</time>
`            `</lastmodified>
`            `<count>`1`</count>
`            `<dataset isData="false" thredds_collection="true">
`                `<name>`wcs`</name>
`                `<size>`000`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`        `</dataset>
`    `</response>
</showCatalog>

Size=000

lastModified=request_time

------------------------------------------------------------------------

### WCS Project List

URL Pattern:


**http://server:port/opendap/wcs/**

**http://server:port/opendap/wcs**

Corresponds to the project list:

<showCatalog>
`    `<response>
`        `<dataset isData="false" thredds_collection="true">
`            `<name>`/wcs`</name>
`            `<size>`238`</size>
`            `<lastmodified>
`                `<date>`2008-02-18`</date>
`                `<time>`15:57:53`</time>
`            `</lastmodified>
`            `<count>`2`</count>
`            `<dataset isData="false" thredds_collection="true">
`                `<name>`CEOP`</name>
`                `<size>`#ofSites`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`            `<dataset isData="false" thredds_collection="true">
`                `<name>`REAP`</name>
`                `<size>`ofSites`</size>
`                `<lastmodified>
`                    `<date>`Time`</date>
`                    `<time>`Time`</time>
`                `</lastmodified>
`            `</dataset>
`        `</dataset>
`    `</response>
</showCatalog>

Size=#_of_sites in that project

lastModified=request_time

------------------------------------------------------------------------

### Project Site List

URL Pattern:


**http://server:port/opendap/wcs/project/**

**http://server:port/opendap/wcs/project**

Correspond to the site list for the project called **project**.


**http://server:port/opendap/wcs/CEOP/**

**http://server:port/opendap/wcs/CEOP**

Correspond to the site list for project called **CEOP**:

<showCatalog>
`    `<response>
`        `<dataset isData="false" thredds_collection="true">
`            `<name>`/wcs/CEOP`</name>
`            `<size>`238`</size>
`            `<lastmodified>
`                `<date>`2008-02-18`</date>
`                `<time>`15:57:53`</time>
`            `</lastmodified>
`            `<count>`2`</count>
`            `<dataset isData="false" thredds_collection="true">
`                `<name>`BER`</name>
`                 `<size>`000`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`            `<dataset isData="false" thredds_collection="true">
`                `<name>`BON`</name>
`                `<size>`000`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`        `</dataset>
`    `</response>` `
</showCatalog>

size=#_of_WCSService_elements in the config file

lastModified=request_time

------------------------------------------------------------------------

### List of WCS servers

URL Pattern:


**http://server:port/opendap/wcs/project/site/**

**http://server:port/opendap/wcs/project/site**

Correspond to the list of WCS Servers available for the project called
**project** and the site called **site**..


**http://server:port/opendap/wcs/CEOP/BER/**

**http://server:port/opendap/wcs/CEOP/BER**

Correspond to the list of WCS Servers available for the project called
**CEOP** and the site called **BER**..

<showCatalog>
`    `<response>
`        `<dataset isData="false" thredds_collection="true">
`            `<name>`/wcs/CEOP/BER/`</name>
`            `<size>`238`</size>
`            `<lastmodified>
`                `<date>`2008-02-18`</date>
`                `<time>`15:57:53`</time>
`            `</lastmodified>
`            `<count>`2`</count>
`            `<dataset isData="false" thredds_collection="true">
`                `<name>`Server1`</name>
`                `<size>`000`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`            `<dataset isData="false" thredds_collection="true">
`                `<name>`Server2`</name>
`                `<size>`000`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`        `</dataset>
`    `</response>` `
</showCatalog>

size=#_of_<CoverageSummary>_elements returned by a WCS
*DescribeCoverage* command to the server

lastModified=request_time

------------------------------------------------------------------------

### Coverage List

URL Pattern:


''' http://server:port/opendap/wcs/project/site/wcs_service/

**http://server:port/opendap/wcs/project/site/wcs_service**

Corresponds to a list of the <CoverageOffering> elements returned by the
WCS server called **wcs_service** (as defined in a <WCSService> element
in the config file.) in response to the *DescribeCoverage* command.


''' http://server:port/opendap/wcs/CEOP/BER/Server1/

**http://server:port/opendap/wcs/CEOP/BER/Server1**

Corresponds to a list of the <CoverageOffering> elements returned by the
WCS server called **Server1** (as defined in a <WCSService> element in
the config file.) in response to the *DescribeCoverage* command.

<showCatalog>
`    `<response>
`        `<dataset isData="false" thredds_collection="true">
`            `<name>`/wcs/CEOP/BER/Server1/`</name>
`            `<size>`238`</size>
`            `<lastmodified>
`                `<date>`2008-02-18`</date>
`                `<time>`15:57:53`</time>
`            `</lastmodified>
`            `<count>`2`</count>
`            `<dataset isData="false" thredds_collection="true">
`                `<name>`TSurfAir`</name>
`                `<size>`000`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`            `<dataset isData="false" thredds_collection="true">
`                `<name>`GP_Surface`</name>
`                `<size>`000`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`        `</dataset>
`    `</response>` `
</showCatalog>

size=# of items returned by
*http://server:port/opendap/wcs/project/site/wcs_service/coverage/*

lastModified=request_time

It is not unreasonable to think that we can make this response using an
XSLT on the coverage description returned by the WCS server. In fact, in
the OLFS we could build the directory page that way...

------------------------------------------------------------------------

### List of dates for which is data is available

URL Pattern:


**http://server:port/opendap/wcs/project/site/wcs_service/coverage/**

**http://server:port/opendap/wcs/project/site/wcs_service/coverage**

Corresponds to the intersection of the sets:

`* list of days that the coverage called `**`coverage`**` (From the <!CoverageOffering> element in the `*`!DescribeCoverage`*` response from the WCS server.)`
`* list of days that the coverage is requested by the site called `**`site`**` (From the `<Site>` element in the configuration coverage.)`


**http://server:port/opendap/wcs/CEOP/BER/Server1/TSurfAir/**

**http://server:port/opendap/wcs/CEOP/BER/Server1/TSurfAir**

Corresponds to the intersection of the sets:

- list of days from the coverage called coverage **TSurfAir**.
- list of days that the coverage is requested by the site called **BER**
  (From the <Site> element in the configuration coverage.)

<showCatalog>
`    `<response>
`        `<dataset isData="false" thredds_collection="true">
`            `<name>`/wcs/CEOP/BER/Server1/TSurfAir`</name>
`            `<size>`238`</size>
`            `<lastmodified>
`                `<date>`2008-02-18`</date>
`                `<time>`15:57:53`</time>
`            `</lastmodified>
`            `<count>`2`</count>
`            `<dataset isData="false" thredds_collection="false">
`                `<name>`2002-10-01`</name>
`                `<size>`000`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`            `<dataset isData="false" thredds_collection="false">
`                `<name>`2004-09-30`</name>
`                `<size>`000`</size>
`                `<lastmodified>
`                    `<date>`Today`</date>
`                    `<time>`Now`</time>
`                `</lastmodified>
`            `</dataset>
`        `</dataset>
`     `</response>
</showCatalog>

size = Got me... maybe the age of the cached file if there is one
already?

lastModified = request_time

------------------------------------------------------------------------

### Data Access URL's

At this point we are down to the data URLs.

From this path
*http://server:port/opendap/wcs/project/site/wcs_service/coverage/date*

- DDS -
  http://server:port/opendap/wcs/project/site/wcs_service/coverage/date.dds
- DAS -
  http://server:port/opendap/wcs/project/site/wcs_service/coverage/date.das
- DDX -
  http://server:port/opendap/wcs/project/site/wcs_service/coverage/date.ddx
- INFO -
  http://server:port/opendap/wcs/project/site/wcs_service/coverage/date.info
- HTML -
  http://server:port/opendap/wcs/project/site/wcs_service/coverage/date.html

From this path
*[http://server:port/opendap/wcs/CEOP/BER/Server1/TSurfAir/2008-02-18](http://server:port/opendap/wcs/CEOP/BER/Server1/TSurfAir/2008-02-18)*

- DDS -
  http://server:port/opendap/wcs/CEOP/BER/Server1/TSurfAir/2008-02-18.dds
- DAS -
  http://server:port/opendap/wcs/CEOP/BER/Server1/TSurfAir/2008-02-18.das
- DDX -
  http://server:port/opendap/wcs/CEOP/BER/Server1/TSurfAir/2008-02-18.ddx
- INFO -
  http://server:port/opendap/wcs/CEOP/BER/Server1/TSurfAir/2008-02-18.info
- HTML -
  http://server:port/opendap/wcs/CEOP/BER/Server1/TSurfAir/2008-02-18.html

The WCS request needs to get this thing:

[`http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET?service=WCS&version=1.0.0&request=GetCoverage&coverage=TSurfAir&crs=WGS84&bbox=-107.375000,51.625000,-102.625000,56.375000&format=netCDF&TIME=2002-08-31&resx=0.25&resy=0.25&interpolationMethod=Nearest%20neighbor`](http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET?service=WCS&version=1.0.0&request=GetCoverage&coverage=TSurfAir&crs=WGS84&bbox=-107.375000,51.625000,-102.625000,56.375000&format=netCDF&TIME=2002-08-31&resx=0.25&resy=0.25&interpolationMethod=Nearest%20neighbor)`"`

So let's pick that apart:

**http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET** - Config file:
<ServiceURL> element in <WCSService> (REQUIRED)

**service=WCS** - Config file: <service> element in <WCSService>
(REQUIRED)

**version=1.0.0** - Config file: <version> element in <WCSService>
(REQUIRED)

**request=GetCoverage** - The command that actually gets data.
(REQUIRED)

**coverage=TSurfAir** - In the URL. (REQUIRED)

**crs=WGS84** - Config file: <crs> element in <Site> Defines coordinates
system for request and repsponse. (REQUIRED)

**bbox=-107.375000,51.625000,-102.625000,56.375000** - Config file:
<bbox> element in <Site> (REQUIRED)

**format=netCDF** - Config file: format element in <Site> (REQUIRED)

**time=2002-08-31** - In the URL (REQUIRED)

**resx=0.25** - Config file: <resx> element in <Site> (REQUIRED)

**resy=0.25** - Config file: <resy> element in <Site> (REQUIRED)

**interpolationMethod=Nearest%20neighbor** - Config file:
<interpolationMethod> element in <Site> (OPTIONAL)

Now, here's the beauty of it: WCS has a soap interface. Basically every
one of these parameters appears as an element in the SOAP request
document. The corresponding SOAP request for this URL is:

<GetCoverage version="1.0.0" service="WCS">` `
`    `<Coverage>`TSurfAir`</Coverage>
`    `<crs>`WGS84`</crs >
`    `<bbox>`-107.475000,45.935000,-102.725000,50.685000`</bbox>
`    `<format>`netCDF`</format>
`    `<time>`2002-08-31`</time>
`    `<resx>`0.25`</resx>
`    `<resy>`0.25`</resy>
`    `<interpolationMethod>`Nearest neighbor`</interpolationMethod>
</GetCoverage >` `

It seems to me that the thing to do is to put all of these things in the
configuration, and hold them to the same rules as the WCS folks. Then we
can just grab and insert the Element objects when we are working in the
code base. The formating of the content of the WCS request elements is
the same as for the KVP encoding (HTTP URL), so for using the HTTP GET
interface we still don't have to modify anything, just rearrange it.

The config file looks like this:

<WCSConfig>
`    `<WCSService name="server1" >
`        `<ServiceURL>
`            `[`http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET`](http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET)
`        `</ServiceURL>
`        `<service>`WCS`</service>
`        `<version>`1.0.0`</version>
`    `</WCSService>
`    `<WCSService name="server2" >
`        `<ServiceURL>
`            `[`http://g2dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET`](http://g2dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET)
`        `</ServiceURL>
`        `<service>`WCS`</service>
`        `<version>`1.0.0`</version>
`    `</WCSService>
`    `<Project name="CEOP">
`        `<Site name="BER">
`            `<label>`BERMS Old Black Spruce`</label>
`            `<WCSParameters>
`                `<bbox>`-107.475000,45.935000,-102.725000,50.685000`</bbox>
`                `<time>`2003-10-01/2003-11-30`</time>
`                `<format>`netCDF`</format>
`                `<resx>`0.25`</resx>
`                `<resy>`0.25`</resy>
`                `<interpolationMethod>`Nearest neighbor`</interpolationMethod>
`            `</WCSParameters>
`        `</Site>
`        `<Site name="BON">
`            `<label>`Bondville`</label>
`             `<WCSParameters>
`               `<crs>`WGS84`</crs >
`                `<bbox>`-90.665000,37.635000,-85.915000,42.385000`</bbox>
`                `<time>`2003-10-01/2003-11-30`</time>
`                `<format>`netCDF`</format>
`                `<resx>`0.25`</resx>
`                `<resy>`0.25`</resy>
`                `<interpolationMethod>`Nearest neighbor`</interpolationMethod>
`            `</WCSParameters>
`        `</Site>
`    `</Project>
</WCSConfig>