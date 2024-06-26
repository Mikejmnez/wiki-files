|                                                                  |                                              |                                                                                        |                                       |
|------------------------------------------------------------------|----------------------------------------------|----------------------------------------------------------------------------------------|:-------------------------------------:|
| \[writing_client_1.html ![1 Preface](previous.gif "1 Preface")\] | \[writing_client.html ![Top](up.gif "Top")\] | \[writing_client_3.html ![3 The DAP Architecture](next.gif "3 The DAP Architecture")\] | **2 Writing your own OPeNDAP client** |

# 2 Writing your own OPeNDAP client

## 2.1 Choose a language

The OPeNDAP project provides both
[C++](http://www.opendap.org/support/docs.html/api/pguide-html/) and
[Java](http://www.opendap.org/support/docs.html/home/swJava1.1/)
implementations of the DAP. Each library includes classes that implement
the various objects which comprise the DAP software for building
clients. Each also includes some extra software which simplifies
building clients by managing virtual connections, handling data caching,
et cetera. To choose one of the toolkits, several factors should be
weighed. With which of the two programming languages are you most
comfortable? What type of computer will the client run on? For client
development, both Java and C++ are supported on win32 and Unix
architectures. Java is more likely to be supported on other
architectures, such as Mac.

The DAP library is middle-ware. You can use it to build a completely new
client or to add network data access to an existing program (making it a
client). If you're interested in writing a client from scratch, simply
choose the toolkit/language you feel most comfortable. If you're going
to take an existing program and transform it into a client there are
several additional factors beyond programming language you should
consider.

If the program you want to DAP-enable can read netCDF files, by far the
easiest way to achieve your goal is to use our DAP-enable netCDF *client
library* (CL). This piece of software works like the standard netCDF CL
but has been modified to recognize DAP URLs when they are presented in
place of local file names. The OPeNDAP netCDF CL has exactly the same
functional interface as the standard netCDF library available from
Unidata. That makes it a very powerful tool because it is possible to
tap into a large number of existing programs and build OPeNDAP clients.
Ferret, GRrADS and IDV are complex programs which have been
OPeNDAP-enabled using the netCDF CL.

Using the netCDF CL is not without its caveats. First netCDF does not do
a good job of representing the entire OPeNDAP data model (That's not a
dig at netCDF, it's a reflection of different design goals for two
pieces of software). It's very hard to access point data using the
netCDF CL, although we're working on this problem. Second, the C++ DAP
library is used to build the C version of the netCDF
CL<sup>\[writing_client_12.html#id1 1\]</sup> which means that the
application programs *must* be linked use a C++ compiler. Finally, it
can be tricky to build a shared object (aka DLL) using the C++ DAP
library.<sup>\[writing_client_12.html#id2 2\]</sup> If you're going to
do that, please contact us through user support or the technical
discussion list.

However, while the previous points are true, the principle distinction
between using the netCDF CL and one of the DAP class libraries is that
with the netcDF CL you often do not have to write any new software at
all! If you choose to use one of the DAP libraries, you're going to have
to write some code.

If you plan to OPeNDAP-enable an existing application using the Java
toolkit, the application typically must provide a Java Virtual Machine
(VM). You may be able to side-step this if you feel you can rely on the
host OS to provide a JVM and/or your target program already has a JVM
embedded in it. Still you must be sure there is a way to communicate
data values between the DAP library and the application.

To use the C++ toolkit you should insure that your client application
can be built with, or link with libraries constructed using
C++compilers.<sup>\[writing_client_12.html#id3 3\]</sup> Check the
current list of supported architectures on the web-site, or contact the
DODS technical support (<support@unidata.ucar.edu>), or write to the
[OPeNDAP Mailing Lists](http://www.opendap.org/mailLists/) mailing list
for information and/or help.

This tutorial will focus on using the C++ toolkit. In Section
\[writing_client_10.html A\] the main programmatic differences between
the two class libraries is listed. Both toolkits are essentially the
same, with only minor differences between them.

## 2.2 Client Architecture

In essence, an OPeNDAP-enabled client uses overloaded URLs to form the
requests to a remote data server. Through the URL, the client connects
to the remote server and issues one of several requests. In response to
each request, the server will return a well-defined response that the
client can use to intern the structure and content of the remote data
into local data structures, as well as retrieve any attributes
associated with the remote data.

The C++ and Java toolkits share the same characteristics, though the
names of the objects and their methods may be slightly different. If you
understand how the clients are built, it will be easy to see how your
own client application can be OPeNDAP-enabled with minimal effort. The
[<cite>The DODS Toolkit Programmer's
Guide</cite>](http://www.opendap.org/support/docs.html/api/pguide-html/)
provides a complete description of the C++ Toolkit and the
<http://www.opendap.org/support/docs.html/home/swJava1.1/> provides a
complete description of the Java toolkit.

------------------------------------------------------------------------

James Gallagher \<jgallagher@gso.uri.edu\>, 2004/04/24, Revision: 1.10



|                                                                  |                                              |                                                                                        |                                       |
|------------------------------------------------------------------|----------------------------------------------|----------------------------------------------------------------------------------------|:-------------------------------------:|
| \[writing_client_1.html ![1 Preface](previous.gif "1 Preface")\] | \[writing_client.html ![Top](up.gif "Top")\] | \[writing_client_3.html ![3 The DAP Architecture](next.gif "3 The DAP Architecture")\] | **2 Writing your own OPeNDAP client** |