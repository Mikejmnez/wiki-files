<font size="+1">This is the **OPeNDAP 4 Data Server**, also known as
**Hyrax**. See our new **[Hyrax Installation and Configuration
Guide](https://opendap.github.io/hyrax_guide/Master_Hyrax_Guide.html)**.</font>

------------------------------------------------------------------------

<font color="red"> Note: The documentation on this is somewhat out of
date as of October 2017. </font>

This page leads you through implementing and configuring Hyrax. Work
through this page from top to bottom.

## Hyrax Operational Overview

<figure>
<img src="HyraxArchitecture.jpg" title="HyraxArchitecture.jpg" />
<figcaption>HyraxArchitecture.jpg</figcaption>
</figure>

Hyrax uses the Java servlet mechanism to hand off requests from a
general web daemon to DAP format-specific software. This results in
higher performance for small requests. The servlet front end, which we
call the **O**PeNDAP **L**ightweight **F**ront end **S**erver (OLFS)
looks at each request and formulates a query to a second server (which
may or may not on the same machine as the OLFS) called the **B**ack
**E**nd **S**erver (BES).

The BES is the high-performance server software from HAO. It handles
reading data from the data stores and returning DAP-compliant responses
to the OLFS. In turn, the OLFS may pass these response back to the
requestor with little or no modification or it may use them to build
more complex responses. The nature of the Inter Process Communication
(IPC) between the OLFS and BES is such that they should both be on the
same machine or be able to communicate over a very high bandwidth
channel.

Both the OLFS and the BES will run and serve test data immediately after
a default installation. Additional configuration is required for them to
serve site specific data.

<table>
<caption>Hyrax Features</caption>
<tbody>
<tr class="odd">
<td><p><strong>THREDDS Catalog Support</strong></p></td>
<td><p>Hyrax supports the THREDDS catalogs. It can serve user supplied
static catalogs and it will dynamically generate THREDDS catalogs of
it's internal holdings.</p></td>
</tr>
<tr class="even">
<td><p><strong>Dataset Aggregation</strong></p></td>
<td><p>Collections of related data resources can be collected into a
single dataset using the aggregation features. Typically these are
formed for geographic tiles, time series, etc.</p></td>
</tr>
<tr class="odd">
<td><p><strong>Adding/modifying dataset content</strong></p></td>
<td><p>Datasets can be modified by the server without having to actually
change the underlying files. These views are independently accessible
from the original data. Both dataset metadata and data values may be
added or changed.</p></td>
</tr>
<tr class="even">
<td><p><strong>Multiple source data formats</strong></p></td>
<td><p>Server can ingest source data stored as HDF4, HDF4-EOS, HDF5,
HDF5-EOS, NetCDF-3, NetCDF-4, CEDAR, FITS, Comma Separated Values, and
raw ASCII and Binary formats. Because of Hyrax's extensible design, it's
easy to add new source data formats.</p></td>
</tr>
<tr class="odd">
<td><p><strong>Multiple Return Formats For Data rRetrieval in multiple
return formats</strong></p></td>
<td><p>Hyrax is able to return data in DAP, DAP4, NetCDF-3, NetCDF-4,
JSON, CSV, and ASCII formats, Or, you can add your own response
types.</p></td>
</tr>
<tr class="even">
<td><p><strong>Gateway Service</strong></p></td>
<td><p>Hyrax supports a gateway service feature that allows it to
provide DAP (and other Hyrax) services for remotely held datasets that
are stored in any of Hyrax's source data formats.</p></td>
</tr>
<tr class="odd">
<td><p><strong>RDF</strong></p></td>
<td><p>Hyrax provides RDF descriptions of it's data holdings. These can
enable semantic web tools to operate upon the metadata content held in
the server.</p></td>
</tr>
<tr class="even">
<td><p><strong><a href="Server_Side_Processing_Functions"
title="wikilink">Server side functions</a></strong></p></td>
<td><p>Hyrax supports a number of server side functions out of the box
including (but not limited to):</p>
<ul>
<li><em><a href="Server_Side_Processing_Functions#geogrid.28.29"
title="wikilink">geogrid</a></em>: Subset applicable DAP Grids using
latitude and longitude values.</li>
<li><em><a href="Server_Side_Processing_Functions#grid.28.29"
title="wikilink">grid</a></em>: Subset any DAP Grid object using the
values of it's map vectors.</li>
<li><em><a href="Server_Side_Processing_Functions#linear_scale"
title="wikilink">linear_scale</a></em>: Apply a linear equation to the
data returned, including automatic use of CF attributes.</li>
<li><em><a href="Server_Side_Processing_Functions#version.28.29"
title="wikilink">version</a></em>: The version function provides a list
of the server-side processing functions available.</li>
<li>New ones are easy to add.</li>
</ul></td>
</tr>
<tr class="odd">
<td><p><strong>Extensible WebStart functionality for data
clients</strong></p></td>
<td><p>Hyrax provides WebStart functionality for a number of Java based
DAP clients. It's simple to add new clients to the list that Hyrax
supports.</p></td>
</tr>
<tr class="even">
<td><p><strong>Extensible WebStart functionality for data
clients</strong></p></td>
<td><p>Hyrax provides WebStart functionality for a number of Java based
DAP clients. It's simple to add new clients to the list that Hyrax
supports.</p></td>
</tr>
<tr class="odd">
<td><p><strong>Extensible/Configurable web interface</strong></p></td>
<td><p>The web interface for both Hyrax and the administrator's
interface can be customized using CSS and XSL. You can add your
organizations logo and specialize the colors and fonts in the
presentation of data sets.</p></td>
</tr>
<tr class="even">
<td><p><strong>Administrator's interface</strong></p></td>
<td><p>Control and dynamically update Hyrax from a convenient web
interface. See <a href="Hyrax_-_Administrators_Interface"
title="wikilink">the Admin interface documentation</a>.</p></td>
</tr>
<tr class="odd">
<td><p><strong>WMS services</strong></p></td>
<td><p><a href="Hyrax_WMS" title="wikilink">Hyrax now supports WMS
services via integration with ncWMS.</a></p></td>
</tr>
<tr class="even">
<td><p><strong>JSON responses</strong></p></td>
<td><p><a href="Hyrax_JSON" title="wikilink">Both metadata and data are
now available in a JSON encoding.</a></p></td>
</tr>
<tr class="odd">
<td><p><strong>JSON responses</strong></p></td>
<td><p><a href="Hyrax_JSON" title="wikilink">Both metadata and data are
now available in a JSON encoding.</a></p></td>
</tr>
<tr class="even">
<td><p><strong>w10n</strong></p></td>
<td><p>Hyrax comes with a complete w10n service stack. W10n navigation
is supported through the default catalog where all datasets and
"structure" variables appear as graph nodes. Data can be acquired for
atomic types or arrays of atomic types in a number of formats.</p></td>
</tr>
</tbody>
</table>

Hyrax Features

Feature Request
Is there a feature you would like to see but don't? Let us know:
<support@opendap.org> or <opendap-tech@opendap.org> (You need to
[subscribe](http://mailman.opendap.org/mailman/listinfo/opendap-tech)
first)

## Downloading and Installing

The download and installation instructions are kept together. For the
latest release look at <font size="2">[the Hyrax downloads
page](https://www.opendap.org/index.php/software/hyrax-data-server)</font>.

### Building And Installing From Source

[Getting and Building The Code From
GitHub](Hyrax_GitHub_Source_Build "wikilink")

## Server Configuration

At this point, you should have a running Hyrax server, but it will not
be configured for your needs. In this section you will configure your
Hyrax server. To do this, you will need to configure BES and OLFS;
optionally, you can configure THREDDS catalogs to provide a custom
virtual view of the servers holdings.

- [Hyrax Configuration Instructions](Hyrax_-_Configuration "wikilink")
  - [BES Configuration](Hyrax_-_BES_Configuration "wikilink")
  - [OLFS Configuration](Hyrax_-_OLFS_Configuration "wikilink")
  - [THREDDS Catalog
    Configuration](Hyrax_-_THREDDS_Configuration "wikilink")

### Apache Integration

This is an optional step after configuring BES and OLFS. Integrating
Hyrax with Apache allows you to integrate your Hyrax server with
existing web infrastructure and is the recommended path for enabling
user authentication and authorization.

- [Hyrax integration with the Apache Web
  Server](Hyrax_-_Apache_Integration "wikilink")

#### User Authentication

- - [Hyrax User Authentication and
    Identification](Hyrax_-_User_Identification_(Authentication) "wikilink")
    Configure Hyrax to support user authentication. Explains how to
    integrate Hyrax with LDAP, Earthdata Login, Shibboleth, etc.
  - [DAP Clients -
    Authentication](DAP_Clients_-_Authentication "wikilink") Instruction
    to configure various DAP (and other generic HTTP clients) to work
    with the various authentication schemes

### And then there's more...

- [Customizing Hyrax](Hyrax_-_Customizing_Hyrax "wikilink"). Here you
  will find instructions on how to change the colors, fonts, and logo
  images presented in the Hyrax web pages.
- [System Administrators
  Workshop](Australian_BOM_System_Administrator's_Agenda_and_Presentations "wikilink")
  Here are slides from a system administrators workshop we gave to the
  Australian BOM on how to manage a Hyrax instance.
- [Hyrax - Administrators
  Interface](Hyrax_-_Administrators_Interface "wikilink") Here are the
  instruction for using the Hyrax Admin Interface, a web UI that allows
  one to view logs, restart, reconfigure, and otherwise fiddle with a
  Hyrax instance.

## Modules

Hyrax has a number of modules that provide the actual functionality of
the server: Reading data files, building different kinds of responses
and performing different kinds of server processing operations. Most of
these modules work with the BES but some are part of the front (web
facing) part of the server.

### BES modules

- [NetCDF data handler](BES_-_Modules_-_The_NetCDF_Handler "wikilink")
- [HDF4 data handler](BES_-_Modules_-_The_HDF4_Handler "wikilink")
- [HDF5 data handler](BES_-_Modules_-_The_HDF5_Handler "wikilink")
- [FreeForm data handler](The_FreeForm_Data_Handler "wikilink")
- [NcML data handler](BES_-_Modules_-_NcML_Module "wikilink")
  - [Variable and Metadata
    modification](http://docs.opendap.org/index.php/BES_-_Modules_-_NcML_Module#Functionality)
  - [Aggregated
    Datasets](http://docs.opendap.org/index.php/BES_-_Modules_-_NcML_Module#Aggregation_Tutorials)'')
- [Gateway handler](BES_-_Modules_-_Gateway_Module "wikilink")
  (Interoperability between Hyrax and other web services)
- [CSV handler](BES_-_Modules_-_CSV_Handler "wikilink")
- [GeoTiff, GRiB2, JPEG2000
  hander](BES_-_Modules_-_GeoTiff,_GRIB2,_JPEG2000_Handler "wikilink")
- [NetCDF File Response
  handler](BES_-_Modules_-_FileOut_Netcdf "wikilink")
- [GDAL (GeoTIFF, JPEG2000) File Response
  handler](BES_-_Modules_-_FileOut_GDAL "wikilink")

### Additional Java Modules that use the BES

- [WMS](Hyrax_WMS "wikilink") - Web Mapping Service via integration with
  ncWMS.
- [Aggregation enhancements](Aggregation_enhancements "wikilink")

Unsupported:

- [SQL handler](BES_-_Modules_-_SQL_Hander "wikilink")

## Technical Support

**Technical Support:**

- <support@opendap.org>
- <opendap-tech@opendap.org> (You need to
  [subscribe](http://mailman.opendap.org/mailman/listinfo) first)

**Hyrax Java Development:**

- ndp <at> opendap <dot> org

**Hyrax C++ Development:**

- pwest <at> ucar <dot> edu (*bes*)
- jgallagher <at> opendap <dot> org (*libdap*)

## Sponsorship

OPeNDAP Hyrax development is sponsored by:

<img src="Nasa-logo.jpg" title="Nasa-logo.jpg" width="95"
alt="Nasa-logo.jpg" /> **[National Aeronautics and Space
Administration](http://www.nasa.gov)**

<img src="Nsf-logo.png" title="Nsf-logo.png" width="95"
alt="Nsf-logo.png" /> **[National Science
Foundation](http://www.nsf.gov)**

This material is based on work supported by the National Science
Foundation under Grant No. 0430822. Any opinions, findings and
conclusions or recommendations expressed in this material are those of
the author(s) and do not necessarily reflect the views of the National
Science Foundation (NSF).

<img src="Noaa-logo.jpg" title="Noaa-logo.jpg" width="90"
alt="Noaa-logo.jpg" /> **[National Oceanic and Atmospheric
Administration](http://www.noaa.gov)**