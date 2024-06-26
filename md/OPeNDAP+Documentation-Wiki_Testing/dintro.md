[return to FreeForm](FreeForm "wikilink")

# Introduction

The OPeNDAP FreeForm ND Data Handler is an OPeNDAP data handler OPeNDAP
FreeForm ND software to serve data from files in almost any format. The
FreeForm ND Data Access System is a flexible system for specifying data
formats to facilitate data access, management, and use. Since DAP2
allows data to be translated over the internet and read by a client
regardless of the storage format of the data, the combination allows
several format restrictions to be overcome.

The large variety of data formats is a primary obstacle in the way of
creating flexible data management and analysis software. FreeForm ND was
conceived, developed, and implemented at the National Geophysical Data
Center (NGDC) to alleviate the problems that occur when you need to use
data sets with varying native formats or to write format-independent
applications.

DAP2 was originally conceived as a way to move large amounts of
scientific data over the internet. As a consequence of establishing a
flexible data transmission format, DAP2 also allows substantial
independence from the storage format of the original data. Up to now,
however, DAP2 servers have been limited to data in a few widely used
formats. Using the OPeNDAP FreeForm ND Data Handler , many more datasets
can be made available through DAP2.

## The FreeForm ND Solution

OPeNDAP FreeForm ND uses a *format descriptor* file to describe the
format of one or more data files. This descriptor file is a simple text
file that can be created with a text editor, describing the structure of
your data files.

A traditional DAP2 server, illustrated
[below](:Image:regular.jpg "wikilink") receives a request for data from
a DAP2 client who may be at some remote
computer[(2)](Wiki_Testing/footnotes "wikilink"). The data served by
this server must be stored in one of the data formats supported by the
OPeNDAP server (such as netCDF, HDF, or JGOFS), and the server uses
specialized software to read this data from disk.

When it receives a request, the server reads the requested data from its
archive, reformats the data into the DAP2 transmission format and sends
the data back to the client.

<center>

<figure>
<img src="regular.jpg" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

A Traditional DAP2 Server

</center>

The OPeNDAP FreeForm ND Data Handler works in a similar fashion to a
traditional DAP2 server, but before the server reads the data from the
archive, it first reads the data format descriptor to determine how it
should read the data. Only after it has absorbed the details of the data
storage format does it attempt to read the data, pack it into the
transmission format and send it on its way back to the client.

<center>

<figure>
<img src="ff1.jpg" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The OPeNDAP FreeForm ND Data Handler

</center>

## The FreeForm ND System

The OPeNDAP FreeForm ND Data Handler comprises a format description
mechanism, a set of programs for manipulating data, and the server
itself. The software was built using the FreeForm ND library and data
objects. These are documented in The FreeForm ND User's Guide.

The OPeNDAP FreeForm ND Data Handler includes the following programs:

<font color='green'>dap_ff_handler</font> : The OPeNDAP FreeForm ND Data Handler *data handler*. This program is run by the OPeNDAP server to handle the parts of any requests that require knowledge specifically about FreeForm. This program is run by the main server 'dispatch' software. That software is described in the Server Installation Guide, available on the \[<http://www.opendap.org/><cite>OPeNDAP Home Page</cite>\] .

The OPeNDAP FreeForm ND Data Handler distribution also includes the
following OPeNDAP FreeForm ND utilities. These are quite useful to write
and debug format description files.

<font color='green'>newform</font> : This program reformats data according to the input *and output* specifications in a format description file.

<!-- -->

<font color='green'>chkform</font> : After writing a format description file, you can use this program to cross-check the description against a data file.

<!-- -->

<font color='green'>readfile</font> : This program is useful to decode the format used by a binary file. It allows you to try different formats on pieces of a binary file, and see what works.

## Installing the OPeNDAP FreeForm ND Data Handler

If you don't have the OPeNDAP FreeForm ND Data Handler , and want it,
follow these directions. If you have a copy of the OPeNDAP FreeForm ND
Data Handler , and want to know how to use it, see ([<cite>
ff,dquick</cite>](http://www)) for quick instructions and examples of
its use, and [Chapter 6](Wiki_Testing/ff-server "wikilink") for further
information.

You can get the OPeNDAP FreeForm ND Data Handler from the
\[<http://www.opendap.org/><cite>OPeNDAP</cite>\] . Follow the links to
"Download Software" and then to "Current Release." If your computer is
one of the platforms for which we provide a binary release, get that,
otherwise get the source code.

To get a binary release, go to that page, click on the computer you use,
and click on the "FreeForm" button in the "Servers" box. Click the
Download button, and follow the directions. The server will make a
custom binary file for you, which you then download.

Install the resulting shared library in a place where
[Hyrax](Hyrax "wikilink") can find it, and then consult the Hyrax
configuration instructions for the remaining configuration steps.

#### Compiling the OPeNDAP FreeForm ND Data Handler

If the computer and operating system combination you use is not one of
the ones we own, you will have to compile the OPeNDAP FreeForm ND Data
Handler from its source. Go to the OPeNDAP home page (www.opendap.org)
and follow the menu item to the downloads page. From there you will need
the libdap, dap-server and FreeForm handler software source
distributions. Get each of these and perform the following steps:

1.  Expand the distribution (e.g., tar -xzf libdap-3.5.3.tar.gz)
2.  Change to the newly created directory (<font color='green'>cd
    libdap-3.5.3</font>)
3.  Run the configure script (<font color='green'>./configure</font>)
4.  Run make (<font color='green'>make</font>)
5.  Install the software (<font color='green'>make install</font> or
    sudo make install)

Each source distribution contains more detailed build instructions; see
the <font color='green'>README</font>,
<font color='green'>INSTALL</font> and <font color='green'>NEWS</font>
files for the most up-to-date information.