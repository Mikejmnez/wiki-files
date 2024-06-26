Abstracts for the Winter 2007 OPeNDAP Developer's Workshop. Not all
pressentors submitted an abstract. The order here is the order of
presentation.

### Customizing and Extending Hyrax

James Gallagher and Nathan Potter

The OPeNDAP 4 Data Server, Hyrax, can be extended in several different
ways. First, because the server is composed of two interacting
processes, either of those can be replaced with components which provide
new functionality. In addition, both the front-end process, named
'OLFS', and the back-end server, named 'BES', can be extended by writing
handlers. These handlers can be used to perform many different tasks.
For example, reading a new file type or building a gateway to another
data system could be accomplished by writing a new handler for the BES.
A new network protocol could be supported by writing a new handler for
the OLFS. In this presentation, both will be covered, with an emphasis
on the OLFS.

### Harnessing the power of the Server 4 Back-End Server modular framework

Patrick West

The design and development of the OPeNDAP Back-End Server (BES) of
Server 4 was driven by two different projects whose data infrastructure
is implemented as a multi-tier client-server architecture. The first
project being Earth System Grid (ESG) and the second project being the
Coupling, Energetics and Dynamics of Atmospheric Regions (CEDAR). Within
these two projects we required a framework that was extensible and
flexible, allowing us to add new types of data responses, having the
server handle multiple types of data (netCDF and FITS for example),
being able to aggregate the data using different aggregation methods,
reporting on data access for metrics purposes, providing authentication
and/or authorization for data access, and more. This led us to the
development of the BES. In order to demonstrate the full capabilities of
the BES, how it fits in the revised Server 4 architecture and how it
differs from earlier releases of OPeNDAP servers, we present here the
Hello World module.

### Numerical Grid Computations with the OPeNDAP Back End Server(BES)

Jose H. Garcia

OPeNDAP Server 4 architecture provides an extensibility mechanism known
as OPeNDAP modules, which can be designed to serve data or to do server
side processing. This coupled with the powerful Back End Server (BES)
and the light and independent front end allows the design of new systems
that extends the realm of OPeNDAP functionality. In specific, the
Community Spectro-polarimeter Analysis Center (CSAC) projects presents a
very intense computational problem of the type known as scatter-gather.
We have developed a computer grid for the CSAC project that involves the
use of multiple OPeNDAP servers across 20 processors with a light front
end system designed to scatter the problem and to gather the individual
solutions. We present the complete module development as well as the
architecture currently servicing CSAC.

### OPeNDAP Server 4 Back-End Server Authentication and Authorization

Patrick West

The Coupling, Energetics and Dynamics of Atmospheric Regions (CEDAR) and
the Earth System Grid (ESG) projects rely on OPeNDAP Back-End Server
(BES) for the purpose of data distribution. Each of these systems
requires different forms of authentication and authorization. To
facilitate these different requirements, as presented in use-cases for
each of the systems, we have enhanced the modular design of the BES by
including security for establishing connections. In order to achieve
this, we have combined the Point to Point Transfer (PPT) protocol used
by the BES with the Secure Socket Layer (SSL) handshake protocol. The
design, implementation and resulting architectures are presented.

### Software Development and Security at NOAA

John Relph and Kenneth S. Casey

While seeking to tap into the ever-growing capabilities of
community-developed applications like OPeNDAP's Server4 and the THREDDS
Data Server, NOAA must also meet its responsibilities for strict
standards of IT security. NOAA IT security policies related to the
development and deployment of applications on NOAA web sites will be
presented and discussed. Hopefully, a clearer understanding of the
requirements will eventually result in broader and more rapid deployment
of these technologies at NOAA and an enhanced ability to meet the needs
of the user communities.

### The THREDDS Data Server and OPeNDAP security

John Caron

Unidata is implementing "restricted access" to TDS datasets served
through OPeNDAP. Our design goal is to make configuration easy for the
simple case, and also to allow anyone to plug in their own code into the
TDS to do whatever they need. We will present use cases and their HTTP
message exchanges, and try to charactorize their tradeoffs on the server
and client.

### BMRC's OPeNDAP data service, how far can it reach?

TIm Pugh

The Australian Bureau of Meteorology Research Centre (BMRC) has
identified OPeNDAP technology as a key component to its external data
services for research users and in support of the international GODAE
and Tsunami program.

The BMRC has built an external OPeNDAP service with limited storage and
number of data sets. We have a vision to extent the OPeNDAP service to
our large internal storage systems and operational data archives, as
well as to integrate with WMO information systems and APAC data grids.

However, concerns over user authentication, data access privileges, and
resource management must be addressed to provide a secure and robust
environment for our computers and users, especially to be accepted as an
operational resource in the future. How far can we allow OPeNDAP to
reach into our operational systems?

### Using an RDF framework to carry metadata for datasets

Benno Blumenthal

We have been using an RDF framework to gather and operate on dataset
metadata. The goal is to interoperate between metadata conventions that
are attached to data as they travel in different formats, as well as
translating the metadata to match search schema and concepts.

In particular, we will show how one might connect OPeNDAP/netCDF data
with CF convention metadata to MMI, SWEET, and IRI's conceptual
ontologies and how those ontologies can be used for finding data. A
similar mechanism facilitates translation between alternate metadata
conventions.

We hope this example will provide some insight into how OPeNDAP might
transport semantics with the same flexibility that it transports
numerical data.

### SWEET-- an upper level ontology for Earth and Space Sciences

Rob Raskin

### Data Access Protocol meets Python

Roberto De Almeida

Pydap is an implementation of the Data Access Protocol (DAP) written
from scratch in the Python programming language and based on the latest
DAP 2 draft specification. The module comes with a DAP client with
support for authentication and caching, allowing Python programs and
scripts to access any DAP-served dataset through array-like objects. It
also comes with a modular DAP server that runs in a variety of
environments and that can be easily extended to support different data
formats and new response types.

In this talk I will present a short introduction of how to install,
deploy and use pydap both as a client and as a server, covering typical
usage cases. I also plan to show examples of how pydap can be extended,
focusing on the existing Web Map Service (WMS) and Google Earth
interfaces.

### The Matlab OPeNDAP GUI Suite

Peter Cornillon

This presentation summarizes a set of Matlab GUIs that is being
developed for satellite-derived surface ocean properties. The surface
properties are divided into 5 groups: sea surface temperature, vector
winds and wind stress, sea surface height, precipitation and ocean
color. In addition a GUI has been developed for HYCOM data of the North
Atlantic. The GUIs all have the same look and feel and all return
variables in similar structures. A set of functions has been developed
that can be used for basic operations on the returned structures. The
GUIs are all available on the download page of the OPeNDAP web site
under "Matlab Toolbox". These GUIs all call a low level function that
generates the OPeNDAP URL, acquires the data and restructures them on
return to render them in a form that is consistent for all of the GUIs
in the set. This function can be called from scripts as well; i.e., the
data may also be acquired and restructured without human intervention.

In addition, PMEL has developed a Matlab GUI for in situ data. I assume
that they will discuss their system separately.

### OPeNDAP: User Versus Programmatic Access

John Chamberlain

### Poster: Enhancing Access to NASA Satellite Data OGC Web Services using OPeNDAP.

Denis Nadeau

In order to facilitate the remote access to geographically distributed
data sources and tools, the NASA Goddard Earth Sciences Data and
Information Services Center (GES DISC) is developing standards-based,
Open Geospatial Consortium (OGC) Web Services, e.g., Web Map Service
(WMS) for selected data sets that it archives. These services, which
give alternative ways to acquire and explore remotely sensed data, offer
a practical, cost-effective solution for integrating information
distributed among user systems. Spatial-temporal data queries are sent
directly to the OGC-compliant servers, the requested Web Services are
triggered using OPeNDAP to fetch the data, and the rendered data are
directly served back to the clients on the fly. GES DISC OGC Web
Services can be easily integrated into any OGC-compliant clients such as
Google Earth. Current data sets served at the GES DISC through its WMS
(as rendered maps or images) include Tropical Rainfall Measuring Mission
(TR! MM) Gridded Rainfall (Near Real Time, V6) and Atmospheric Infrared
Sounder (AIRS) Brightness Temperature. OPeNDAP allows WMS to be easily
extended to serve other GES DISC data sets of interest to the public, to
enhance the access to NASA satellite data.

### OPeNDAP at ERD with suggestions for future development

Roy Mendelssohn

ERD in colloboration with the West Coast node of Coastwatch is serving
over 5Tb of data online and this amout is growing weekly. Just about all
the data is accessible through OPeNDAP in some form, from gridded data
through the NetCDF handler, aggregated gridded data through the TDS, in
situ data through the OpeNDAP DRDS and through Dapper/Dchart and
in-house some new servics using Pydap.

i will present a brief overview of how we are using OPeNDAP, and based
on both our own experiences, as well as suggestions from user input,
cover features/additions/extensions that we we feel are necessary for
OPeNDAP to grow and gain wider acceptance.

### IPRC data transport, LAS and EPIC server

Jim Potemra

The International Pacific Research Center (IPRC) at the University of
Hawaii engages in climate research specific to the Asia-Pacific region.
Within the IPRC, we maintain a data center for climate data. This data
center, the Asia-Pacific Data-Research Center (APDRC), was originally
established to provide the computational, data management, and
networking infrastructure necessary to make data resources readily
accessible and usable to researchers at the IPRC. We've since expanded
this to include researchers outside the IPRC as well as more general
users. In this presentation we will give an overview of our data server
system that relies on OPeNDAP for data transport, LAS and EPIC for
web-based data viewing. We have also employed various aggregation
schemes to help serve remote data sets.

### OPeNDAP Developments at the Goddard Earth Sciences DISC

Christopher Lynnes

The Goddard Earth Sciences Data and Information Services Center (GES
DISC) archives and distributes Earth Sciences satellite data to the
research community. Beyond that, we we also provide data services
rnaging from simple subsetting up to complex server-side analysis
workflows. Though the GES DISC has been deploying DODS / OPeNDAP servers
since the turn of the century, recent developments within the GES DISC
and EOSDIS at large have elevated the importance of OPeNDAP. For
example, the GES DISC will be relying more on OPeNDAP as a key
infrastructure component beneath the Giovanni analysis tool, and to
support certain end-user communities. With increased reliance on OPeNDAP
comes an increased dependance on improvements in performance,
cataloguing, metrics reporting and customizability. Hopes are being
pinned on the new Server 4 architecture to solve some of these issues,
with collaborations and contributions among the community to aid the
rest.

### OPeNDAP in European oceanography data management

Thomas LOUBRIEU

IFREMER is involved in distributed data management through many
different European oceanography projects. These projects aim at setting
up infrastructures for European ocean observation data management
(Seadatanet, Cersat), at setting up core services for ocean monitoring
and forecasting (Mersea) or fulfilling specific requirements for
thematic usage (Interrisk, Humboldt : oil spill monitoring â¦). In these
projects OPeNDAP is a de facto standard for data access in distributed
systems. As examples we will review the effective and foreseen usages of
the OPeNDAP protocol in the Seadatanet and Mersea projects.

The Seadatanet project (http://www.seadatanet.org) aims at setting up a
pan-european network federating 48 marine data centres. In this project,
OPeNDAP will be used :

1.  to define features (specific models) for the managed data types
    (vertical profiles, under track observations, ...)
2.  to access metadata and populate catalogues
3.  to access and visualize data.

In order to assess interoperability with equivalent networks, Seadatanet
is very concerned in all OPeNDAP tentatives on marine in-situ
observation conventions studies.

The Mersea project (http://www.mersea.eu.org) aims at setting up a
virtual distributed ocean forecasting centre federating results from the
main European operational observation and forecasting centres. In this
distributed system, OPeNDAP (usually TDS servers) is used by every data
providers for data dissemination. It is useful as a low level service
used by more advanced services such as visualization. It is also planned
to use OPeNDAP servers in order to build a distributed download service.
This latest function has to implement access restriction and single sign
on over the distributed servers. OPeNDAP is usually used as a B2B
protocol (business to business) more than a B2C (business to client). To
improve end-users access to the OPeNDAP data flow some initiatives are
starting in the Mersea framework. A simple OPeNDAP client with a simple
GUI assistant to build the request and save the data as netCDF files is
now being developed. This latest software will enable the Mersea
download service authentification.

### The ROSES ACCESS OPeNDAP/OGC Gateway Project

Wenli Yang

This presentation will introduce the current status and future work of
the ROSES ACCESS OPeNDAP/OGC gateway project. The gateway project
addresses the interoperability of two data system infrastructures that
are widely used by different segments of the Earth science research and
applications community. The first are the systems and components that
have been developed within the Earth science community to use a family
of geoscience protocols, including OPeNDAP, netCDF/http, and the
thematic Real-time Environmental Distributed Data Services (THREDDS)
data catalog in accessing Earth science data collections. The second use
the set of interface specifications developed by the Open Geospatial
Consortium (OGC) to access geospatial data collections. The approach of
this project is to develop a gateway that allows a user of a client
component based on one of the infrastructures to have direct access to
the collections of a data provider employing a server based on the !
other.

### Server-side OPeNDAP Analysis - A General Approach Utilizing Legacy Applications through TDS

Roland Schweitzer, Ansley Manke and Steve Hankin

Within the OPeNDAP community the GrADS Data Server (GDS) was a pioneer
\[Wielgosz, 2003\] in introducing server-side analysis capabilities. GDS
server-side calculations have proven to be enormously popular because
they permit data-intensive calculations (e.g. climatological averaging)
to be performed on fast data resources local to the server greatly
reducing the volume of traffic across the Internet. Within the Live
Access Server (LAS) project we adopted the GDS code framework and URL
syntax in the development of the Ferret Data Server (FDS) \[Rogers,
2004\]. Through this server LAS was able to guarantee a consistent
geo-referenced and COARDS-standardized OPeNDAP access to the gridded
data served by LAS.

In recent work we have ported the FDS functionality to the flexible
framework that is available through the Unidata THREDDS Data Server. (We
refer to the new server as F-TDS.) We developed a Ferret I/O Service
Provider (IOSP) for the Java netCDF library. Ferret is a legacy,
command-line analysis and graphics package that reads netCDF (COARDS and
CF-1.0), ASCII and various binary file formats. Through Ferret
directives (âcommandsâ) one can define new virtual variables which
represent the result of some analysis operation applied to one or more
of the data variables. By registering the Ferret IOSP with the THREDDS
Data Server (TDS) a Ferret script which reads netCDF data and defines
virtual variables then becomes an OPeNDAP data set. All of the variables
(the real and virtual variables) defined by the script are visible to
OPeNDAP clients â seamlessly through the netCDF API.

Much of the power of the netCDF API is that it allows applications to
'see' the full coordinate domain of a dataset, but to delay the reading
of the data until the client specifies the precise subset of interest. A
special character of Ferret is that it allows this approach (so-called
'delayed mode'?) to be applied to virtual variables. It performs the
analysis calculations on-demand, only on the sub-set of the data
requested.

The Ferret IOSP and THREDDS Data Server combination can also be used to
enable on-the-fly server-side analysis. Data access information for
multiple data sources and the Ferret commands to define virtual variable
can be embedded into the OPeNDAP URL for this server. This information
is parsed on the server; a new script is dropped into the scan area
being monitored by TDS and the new data set with its virtual variables
is immediately available via the TDS.

The techniques that we have employed are readily applicable for
integrating broad classes of legacy applications (Ferret, GrADS, NCL,
CDAT, Matlab, etc.) as OPeNDAP server-side computation engines. Other
efforts such as SWAMP \[Wang, 2007\] demonstrate the community's
interest in OPeNDAP server-side capabilities. We think it would be a
good idea to develop a common syntax for communicating the underlying
mathematics of the server-side functions. Having a common syntax would
simplify the work for client writers and users who want to take
advantage of server-side analysis available from GDS, the Ferret
IOSP/TDS and other frameworks and servers. We would appreciate some
suggestions, comments and volunteers toward nailing down a server-side
analysis framework.

Joe Wielgosz, Brian Doty, The Grads-Dods Server: An Open-Source Tool For
Distributed Data Access And Anaysis, 19th Conference on IIPS

Richard Rogers, Steve Hankin and Ansley Manke, The Ferret DODS Server,
20th Conference on IIPS

Daniel L. Wang, Univ. of California, Irvine, CA; and C. S. Zender and S.
F. Jenks DAP-enabled Server-side Data Reduction and Analysis, 23rd
Conference on IIPS

### Server-side Data Reduction and Analysis with Script Workflow Analysis for MultiProcessing (SWAMP)

Daniel L. Wang

Despite the inexorable advance of faster, better, and cheaper computing
hardware, terascale data reduction and analysis remain elusive for most.
Massive amounts of netCDF data remain underutilized due to scientists'
limited bandwidth and computational capacity. Our strategy relocates
computation closer to data sources to minimize bandwidth usage and
increase efficiency. We apply our server-side analysis framework, the
Script Workflow Analysis for MultiProcessing (SWAMP) system to specify
and perform computational workflows. Instead of running locally,
scientists' netCDF Operator (NCO) data analysis scripts are processed
and sent via Data Access Protocol (DAP) to a modified OPeNDAP server,
where they are parsed, optimized, scheduled, and executed. Instead of
receiving raw input data to be computed locally, scientists distribute
computation and merely receive the reduced output data, while leveraging
their legacy script-based analysis methods. Our benchmarks quantify the
reduced bandwidth and improved execution time. Local computation and
bandwidth requirements are drastically reduced, freeing the scientist to
perform exploratory analysis and discovery in wider scopes and finer
resolutions, and reducing time to discovery.

### Server side Functions for Geo-spatial Selection

James Gallagher

### Dap4cor -- A Dapper-like server for CORIOLIS in-situ data centre

Thomas LOUBRIEU

CORIOLIS is an operational global ocean in-situ data centre. As such,
CORIOLIS collects, does quality assessment and disseminates datasets.
The vertical profiles datasets are fully managed in an Oracle database.
In order to answer operational data centres (mainly ocean forecasting
centres) needs, CORIOLIS extracts on a regular basis many netCDF files
from the database. The datasets are usually in a standard format (ARGO)
but, depending on the users needs, the contents and update frequencies
differ. The netCDF files are made available through an ftp server.

In order to avoid maintaining so many extraction scripts and
replications of datasets in netCDF files and to improve the data center
interoperability, we have decided to set up an OPeNDAP interface on the
CORIOLIS database. For internal usage it centralizes the extraction
facility, for external users it gives a standard interface for
interactive or batch extraction.

After studying the DAPPER solution from PMEL, it has been decided to
keep the DAP profile data model but to develop a specific implementation
based on our Oracle database.

The server called DAP4COR is a web application running under tomcat. It
is based on the java DAP 1.1.7 API. The most of work has been spent by,
on one hand translating OPeNDAP requests criteria into SQL requests, on
the other hand setting up a profile interface which reads the database
tables and serializes into a DAP response.

For accessing the server, a pool of MATLAB scripts and a Dapper to
netCDF conversion tool have been developed. The further developments
will aim at improving the server delays of response and integrating, in
addition to vertical profiles, moorings and trajectories data type
(following the dapper observation data convention).

Server : <http://www.ifremer.fr/dap4cor> Documentation and tools :
<ftp://ftp.ifremer.fr/ifremer/coriolis/tools/dap4cor>

### Managing large in-situ datasets with Dapper

Joe Sirott Dapper is an OPeNDAP enabled Web server that provides access
to millions of atmospheric and oceanographic observations. This talk
discusses some of the real-world problems that occur when using the
OPeNDAP protocol to serve large in-situ datasets and methods for
overcoming some of these difficulties.