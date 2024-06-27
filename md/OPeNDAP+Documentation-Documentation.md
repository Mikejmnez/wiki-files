__NOTOC__ This site contains the (new) repository for OPeNDAP
software documentation.

# User Guides

### [Getting Started with OPeNDAP Software](https://opendap.github.io/documentation/QuickStart.html)

An overview of our software

### [The User's Guide](https://opendap.github.io/documentation/UserGuideComprehensive.html)

A comprehensive guide to sharing data with our software

### [Server Side Processing Functions](Server_Side_Processing_Functions "wikilink")

A listing of functions that Hyrax provides along with their
documentation.

### [How-To Guides](How-To_Guides "wikilink")

This page holds a collection of Jupyter notebooks, Videos, et cetera.
These have been developed by us and by other groups.

# Software

### [Hyrax - Installation and Customization (BES, OLFS, format & response handlers)](https://opendap.github.io/hyrax_guide/Master_Hyrax_Guide.html)

The OPeNDAP Data Server, Hyrax, is a data server developed by OPeNDAP
that supports DAP2, DAP4 and other Web APIs for data access. These pages
contain documentation that covers server installation and customization.
The old [MediaWiki documentation pages](Hyrax "wikilink") are still
available, but are not being maintained.

### [libdap++: C++ DAP Implementation Documentation](libdap "wikilink")

Libdap is the C++ implementation of the OPeNDAP Data Access Protocol.
This pages contains links to the libdap++ reference guide and a usage
guide that explains some of the ins and outs of this class library.

### [Java OPeNDAP API Documentation](http://www.opendap.org/api/javaDocs/index.html)

The Java OPeNDAP API; this is used by TDS, Netcdf-Java and lots of other
software.

### [BES Implementation Documentation](https://opendap.github.io/bes/html/)

The BES (OPeNDAP Back-End Server) is a part of the OPeNDAP 4 Server,
known as Hyrax. These pages contain the BES reference guide.

### [Aggregation Service](Aggregation_enhancements "wikilink")

The Aggregation Service provides a way for users to apply constraints to
a number of files and returns the result in a single archive file.

# White Papers

### [Making the transition to DAP4](Making_the_transition_to_DAP4 "wikilink")

8/27/14

This is a short How-To that covers modifying existing modules (aka
'handlers') for the BES so that they will return DAP4 Data and Metadata
responses. In this how-to, we cover modifications for modules that read
data as well as ones that transform the DAP responses into other things,
such as taking a binary data object and transforming it into a netCDF
file or a CSV ASCII response.

### [Configuration of BES Modules](Configuration_of_BES_Modules "wikilink")

11/20/14

This is a short How-To for BES modules so that they can be built using
autotools as both standalone code and as part of a unified bes build.

### [A One-day Course on Hyrax Development](A_One-day_Course_on_Hyrax_Development "wikilink")

2/11/2013

This one day course on Hyrax development focuses on building and
debugging the Back End Server (BES) component of Hyrax, with a
particular emphasis on writing server side functions. Included is a SUSE
linux virtual machine that contains a complete copy of Hyrax, ready to
run, along with some sample data and Eclipse. The course is a mixture of
PowerPoint slide sets, hands-on work and MediaWiki pages.

### [Writing a Client](Writing_a_Client "wikilink")

A short tutorial on how to write a client. Still under development.

### [Writing a Module for Hyrax](Writing_a_Module_for_Hyrax "wikilink")

A short tutorial on how to write a module for Hyrax. This is one of many
such 'short tutorials on ...'. Another, much older, one can be found at
[Writing A Server](Wiki_Testing/WritingAServer "wikilink"). It covers
the older server design, but many of the concepts are still valid.

### [Using Virtual Machines to Serve Data](Using_Virtual_Machines_to_Serve_Data "wikilink")

This short guide discusses using a virtual machine and a
[hypervisor](http://en.wikipedia.org/wiki/Hypervisor)
([VMware](http://www.vmware.com/) Server) to serve data. In the guide we
cover both serving data with Hyrax running within a VM and also using
Hyrax in a workshop where the hypervisor is VMware Workstation. You
cannot actually serve data to remote users with Workstation, but it's a
great environment in which to learn about the server's different
capabilities. In both cases the advantages of using a VM are that the
server runs in Linux on the virtual machine while you run the hypervisor
under any of its supported operating systems. An additional advantage is
that the hypervisor can be used very effectively in the context of an
overall security plan.

### [Server Dispatch Operations](ServerDispatchOperations "wikilink")

### Building OPeNDAP Software

We have several pages that describe how to set up an OS/X or CentOS
machine to build our code, particularly Hyrax, which can be tricky to
build. See the [Developer Info](Developer_Info "wikilink") page for more
information.

# Housekeeping

#### [Wiki Testing](Wiki_Testing "wikilink")

#### [Retired Pages](Retired "wikilink")