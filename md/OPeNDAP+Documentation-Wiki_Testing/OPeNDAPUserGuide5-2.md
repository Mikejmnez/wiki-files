## Installing an OPeNDAP Server

Most of the task of installing an OPeNDAP server consists of getting the
required Web server installed and running. The intricacies of this task,
and the variety of available Web servers make this task beyond the scope
of this guide. Proceed with the following steps only after the Web
server itself is operational.

Installing the OPeNDAP CGI programs and the data to be served is a

`relatively simple operation. After`

installing the OPeNDAP source tree and building the software, (See
[Appendix](Wiki_Testing/OPeNDAPUserGuideAppendix "wikilink")), the user
need only copy the CGI program from the <font color='green'>etc</font>
directory in the OPeNDAP source tree
(<font color='green'>\$(DODS_ROOT)/etc</font>) to one of the directories
where the Web server expects to find its CGI programs. The exact name of
this directory is an implementation detail of the Web server itself.

The service programs used by the CGI are generally kept in the same
directory as the CGI itself, although this can be changed by modifying
the OPeNDAP CGI dispatch script.

> The server programs come with release notes and installation notes, in
> files <font color='green'>README</font> and
> <font color='green'>INSTALL</font>, among others. These will be found
> in the distribution directories for the particular server. For
> example, the documentation for the JGOFS server will be found in
> <font color='green'>\$DODS_ROOT/src/http/jg-dods</font>. See
> \[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
> OPeNDAP Programmer's Guide</cite>\] for additional information about
> server documentation.

After installing the CGI program and the services, the data to be
provided must be put in some location where it may be served to clients.
Again, the location of the data depends on the configuration of the Web
server and the API used by the CGI services. Most often, data that is
served by a Web server is kept in the <font color='green'>htdocs</font>
directory, the exact pathname of which is specified in the
<font color='green'>httpd</font>.configuration file. A server may also
be enabled to search a user's home directory tree or may follow links
from the <font color='green'>htdocs</font> directory (if the server is
enabled to follow symbolic links). There may be yet other options
provided by the specific server used in a particular installation, so
there is really no way to avoid consulting the configuration
instructions of the Web server.

As noted, the location of the data depends not only on the configuration
of the Web server, but also on the API used to access the data
requested. For example, the netCDF server simply stores data in a path
relative to the working directory of the CGI program,
<font color='green'>htdocs</font>, while the JGOFS server uses its data
dictionary to specify the location of its data. Refer to the specific
installation notes for each API for more information about the location
of the data.

### Configuring the Server

The issues of server configuration depend to a large extent on the
particular server in question. The OPeNDAP server for JGOFS data is
configured differently than the OPeNDAP server for netCDF data. Each
server comes with its own installation and configuration instructions.
These can be found in a file called <font color='green'>INSTALL</font>
in the distribution directory for the server. The server distribution
directories are in <font color='green'>\$DODS_ROOT/src</font>. Here is a
checklist of items that need to be attended in order to install any
OPeNDAP server:

- Is the <font color='green'>httpd</font> server configured to execute
  CGI programs?
- Are the main CGI and subsidiary CGI programs installed in the

server's CGI directory? For the netCDF API, these will be called
<font color='green'>nph-nc</font>, and
<font color='green'>nc_das</font>, <font color='green'>nc_dds</font>,
and so on. The server CGI's for other API's will have comparable names.

- Is the <font color='green'>gzip</font> program installed in the
  <font color='green'>PATH</font> of the

<font color='green'>httpd</font> server? This is used to compress data
messages returned to the client.

### Constructing the URL

After a dataset has been installed, and the server programs installed,
you need to know what its address is. ([Section
2.1](Wiki_Testing/OPeNDAPUserGuide2 "wikilink")) contains an explanation
of the various parts of the OPeNDAP URL, including a diagram in figure
2.1. Refer to this section, with a copy of the Web server configuration
data readily available. Using the configuration data, you should be able
to determine the appropriate URL for the data you are serving.

Remember that the web server will have its own definition of the root
directory for data, and another definition for CGI programs, depending
on the configuration.

### Documenting Your Data

OPeNDAP contains provisions for supplying documentation to users about a
server, and also about the data that server provides. When a server
receives an information request (through the
<font color='green'>info</font> service that invokes the
<font color='green'>usage</font> program), it returns to the client an
HTML document created from the DAS and DDS of the referenced data. It
may also return information about the server, and more detail about the
dataset.

If you would like to provide more information about a dataset than is
contained in the DAS and DDS, simply create an HTML document (without
the <font color='green'>

<html>

</font> and <font color='green'>

<body>

</font> tags, which are supplied by the <font color='green'>info</font>
service), and store it in the same directory as the dataset, with a name
corresponding to the dataset filename. For example, the datasets
<font color='green'>fnoc1.nc</font>,
<font color='green'>fnoc2.nc</font>, and
<font color='green'>fnoc3.nc</font> might be documented with a file
called <font color='green'>fnoc.html</font>.

You may prefer to override this method of creating documentation and
simply provide a single, complete HTML document that contains general
information for the server or for a group of datasets. For example, to
force the info server to return a particular HTML document for all its
datasets, you would create a complete HTML document and give it the name
dataset <font color='green'>.ovr</font>, where dataset is the dataset
name.

More information about providing user information, including sample HTML
files, and a complete description of the search procedure for finding
the dataset documentation, is to be found in
\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
OPeNDAP Programmer's Guide</cite>\].

### Testing the Installation

It is possible to test the OPeNDAP server to see whether an installation
has been properly done. The easiest way to test the installation is with
a simple Web client like Netscape or Mosaic. (A simple Web client called
<font color='green'>geturl</font> is provided in the OPeNDAP core
software which can retrieve text from Web servers. Look for it in the
<font color='green'>\$(DODS_ROOT)/etc</font> directory.)

The simplest test is simply to ask for the version of the server, or the
help message. The OPeNDAP server uses helper programs to return the DAS,
DDS, and data. If you want to test the server itself, and not the
configuration of the helper programs, the version, help, or info
services will suffice. Issuing a URL with
<font color='green'>.ver</font> on the end will return the version
information for this server, appending <font color='green'>.info</font>
will return the info message, and issuing a URL with a nonsense suffix
or <font color='green'>.help</font> will return a help message:

    > geturl http://dods.gso.uri.edu/cgi-bin/nph-nc/data/test.nc.ver
    > geturl http://dods.gso.uri.edu/cgi-bin/nph-nc/data/test.nc.info
    > geturl http://dods.gso.uri.edu/cgi-bin/nph-nc/data/test.nc.help

To return the data attribute structure of a dataset, use a URL such as
the following: ([15](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink"))

    > geturl http://dods.gso.uri.edu/cgi-bin/nph-nc/data/test.nc.das

Refer to ([Section 6.4.2](Wiki_Testing/OPeNDAPUserGuide6 "wikilink"))
for a description of a data attribute structure. You can compare the
description against what is returned by the above URL to test the
operation of the OPeNDAP server.

You can use your web client to test the OPeNDAP server by using it to
submit URLs that address specific services of the client. See ([Section
2.2](Wiki_Testing/OPeNDAPUserGuide2 "wikilink")) for information about
how to request individual services. If any of the services fail, you can
check the list of helper programs in ([Section
5.1.1](Wiki_Testing/OPeNDAPUserGuide5 "wikilink")) to find out which is
missing. From the web browser, you can access all the OPeNDAP services,
except the (binary) data service. However, if all the others work, you
can be relatively assured that one will, too.

Using the <font color='green'>.html</font> suffix produces the \ifh,
providing a forms-based interface with which a user can query the
dataset using a simple web browser. There's more about the \ifh in
([Chapter 3](Wiki_Testing/OPeNDAPUserGuide3 "wikilink")).

## Displaying Information to the OPeNDAP User

OPeNDAP contains a system that allows an OPeNDAP server a degree of
control over the user's graphic user interface (GUI). This system runs
the system progress indicator, that displays to the user the status of a
pending data request. However, a server may also use the GUI interface
to display messages to the user, such as error messages, and even to
query the user for information.

### GUI Architecture

Since OPeNDAP is built inside a data access API, and since the
application program that has become the OPeNDAP client was presumably
not built with network I/O in mind, an OPeNDAP client will typically not
do any processing at all while it awaits a return message from a data
request. Any communication that must happen between the OPeNDAP software
and the user must occur without the involvement of the application
program that has invoked the OPeNDAP software. To avoid this limitation,
OPeNDAP starts up a GUI manager sub-process. This sub-process can
receive data from the OPeNDAP core software, and can operate the user's
graphical user interface.

The operation of the GUI manager is illustrated in [figure
5.3.1](:Image:wish.gif "wikilink"). As seen in the figure, the client
application can usually control the user's screen, but during a data
request, this communication is suspended. Until the request returns
control to the client application, messages returned from the OPeNDAP
server can be displayed to the user by passing them to the GUI manager
sub-process, who can display them in a window to the user.

<center>

<figure>
<img src="wish.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Architecture of an OPeNDAP Client GUI

</center>

The GUI manager in \OPDversion uses a Tcl/Tk interpreter (the
<font color='green'>wish</font> program is the default) to interpret
messages from the server. These messages usually contain Tcl programs to
display information to the user. However, the
<font color='green'>wish</font> interpreter can also be sent programs to
query the user for more information, or draw little rabbits on the
screen or any other graphic function the server needs to have displayed
to the user. See Tcl and the Tk Toolkit~\citel{osterhout:tcl} for more
information about Tcl.

By default, the GUI manager initializes by running the Tcl programs in
the files <font color='green'>dods_gui.tcl</font>,
<font color='green'>error.tcl</font> and
<font color='green'>progress.tcl</font>. (These are stored in
<font color='green'>\$DODS_ROOT/etc</font>.) Server commands to the GUI
manager can use the functions defined in these files. Note also that the
user may be using a "safe" Tcl interpreter, with a restricted subset of
the usual array of Tcl commands available to it. The user can control
these features of the operation of the GUI by changing several
environment variables. These are described in ([<cite>
opd-client,environment</cite>](http://www)).

A server will use the features of the GUI manager to display error
messages to the user. A server may also use the GUI to query a user to
correct whatever condition caused the error. For example, if a user has
misspelled some part of a constraint expression in a URL submitted to a
server, the server can send the constraint expression back to the user
in an edit window, with instructions to fix it. The user can edit the
expression, and send it back, allowing the server to proceed without
submitting a new request. Consult the client and server toolkit manual
for more information about the \class{Error} object on this subject.

## Building OPeNDAP Data Servers

Though servers are included in the OPeNDAP core software, some users may
wish to write their own OPeNDAP data servers. The architecture of the
<font color='green'>httpd</font> server and the OPeNDAP core software
make this a relatively simple task.

A user may wish to write his or her own OPeNDAP server for any or all of
the following reasons:

- The data to be served may be stored in a format not compatible

with one of the existing OPeNDAP servers.

- The data may be arranged in a fashion that allows a user to

optimize the access of those data by rewriting the service programs.

- The user may wish to provide ancillary data to OPeNDAP clients not

anticipated by the writers of the servers available.

The design of the OPeNDAP library make the task a relatively simple one
for a programmer already familiar with the data access API to be used.
Also, though the servers provided with the OPeNDAP core software are
written in C++, they may be written in any language from which the
OPeNDAP libraries may be called.

Once it is invoked, a CGI program scoops up whatever input is going to
the standard input stream of the Web server
(<font color='green'>httpd</font>) that invoked it. Further, the
standard output of the CGI is piped directly to the WWW library, which
sends it directly back to the requesting client. This means that the CGI
program itself need only read its input from standard input and write
its output to standard output.

Most of the task of writing a server, then, consists of reading the data
with the data access API and loading it into the OPeNDAP classes. Method
functions defined for each class make it simple to output the data so
that it may be sent back to the requesting client. Refer to
\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
OPeNDAP Programmer's Guide</cite>\] for specific information about the
classes and the facilities of the OPeNDAP core software, and
instructions about how to write a new server.

## Installing an OPeNDAP Server

Most of the task of installing an OPeNDAP server consists of getting the
required Web server installed and running. The intricacies of this task,
and the variety of available Web servers make this task beyond the scope
of this guide. Proceed with the following steps only after the Web
server itself is operational.

Installing the OPeNDAP CGI programs and the data to be served is a

`relatively simple operation. After`

installing the OPeNDAP source tree and building the software, (See
[Appendix](Wiki_Testing/OPeNDAPUserGuideAppendix "wikilink")), the user
need only copy the CGI program from the <font color='green'>etc</font>
directory in the OPeNDAP source tree
(<font color='green'>\$(DODS_ROOT)/etc</font>) to one of the directories
where the Web server expects to find its CGI programs. The exact name of
this directory is an implementation detail of the Web server itself.

The service programs used by the CGI are generally kept in the same
directory as the CGI itself, although this can be changed by modifying
the OPeNDAP CGI dispatch script.

> The server programs come with release notes and installation notes, in
> files <font color='green'>README</font> and
> <font color='green'>INSTALL</font>, among others. These will be found
> in the distribution directories for the particular server. For
> example, the documentation for the JGOFS server will be found in
> <font color='green'>\$DODS_ROOT/src/http/jg-dods</font>. See
> \[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
> OPeNDAP Programmer's Guide</cite>\] for additional information about
> server documentation.

After installing the CGI program and the services, the data to be
provided must be put in some location where it may be served to clients.
Again, the location of the data depends on the configuration of the Web
server and the API used by the CGI services. Most often, data that is
served by a Web server is kept in the <font color='green'>htdocs</font>
directory, the exact pathname of which is specified in the
<font color='green'>httpd</font>.configuration file. A server may also
be enabled to search a user's home directory tree or may follow links
from the <font color='green'>htdocs</font> directory (if the server is
enabled to follow symbolic links). There may be yet other options
provided by the specific server used in a particular installation, so
there is really no way to avoid consulting the configuration
instructions of the Web server.

As noted, the location of the data depends not only on the configuration
of the Web server, but also on the API used to access the data
requested. For example, the netCDF server simply stores data in a path
relative to the working directory of the CGI program,
<font color='green'>htdocs</font>, while the JGOFS server uses its data
dictionary to specify the location of its data. Refer to the specific
installation notes for each API for more information about the location
of the data.

### Configuring the Server

The issues of server configuration depend to a large extent on the
particular server in question. The OPeNDAP server for JGOFS data is
configured differently than the OPeNDAP server for netCDF data. Each
server comes with its own installation and configuration instructions.
These can be found in a file called <font color='green'>INSTALL</font>
in the distribution directory for the server. The server distribution
directories are in <font color='green'>\$DODS_ROOT/src</font>. Here is a
checklist of items that need to be attended in order to install any
OPeNDAP server:

- Is the <font color='green'>httpd</font> server configured to execute
  CGI programs?
- Are the main CGI and subsidiary CGI programs installed in the

server's CGI directory? For the netCDF API, these will be called
<font color='green'>nph-nc</font>, and
<font color='green'>nc_das</font>, <font color='green'>nc_dds</font>,
and so on. The server CGI's for other API's will have comparable names.

- Is the <font color='green'>gzip</font> program installed in the
  <font color='green'>PATH</font> of the

<font color='green'>httpd</font> server? This is used to compress data
messages returned to the client.

### Constructing the URL

After a dataset has been installed, and the server programs installed,
you need to know what its address is. ([Section
2.1](Wiki_Testing/OPeNDAPUserGuide2 "wikilink")) contains an explanation
of the various parts of the OPeNDAP URL, including a diagram in figure
2.1. Refer to this section, with a copy of the Web server configuration
data readily available. Using the configuration data, you should be able
to determine the appropriate URL for the data you are serving.

Remember that the web server will have its own definition of the root
directory for data, and another definition for CGI programs, depending
on the configuration.

### Documenting Your Data

OPeNDAP contains provisions for supplying documentation to users about a
server, and also about the data that server provides. When a server
receives an information request (through the
<font color='green'>info</font> service that invokes the
<font color='green'>usage</font> program), it returns to the client an
HTML document created from the DAS and DDS of the referenced data. It
may also return information about the server, and more detail about the
dataset.

If you would like to provide more information about a dataset than is
contained in the DAS and DDS, simply create an HTML document (without
the <font color='green'>

<html>

</font> and <font color='green'>

<body>

</font> tags, which are supplied by the <font color='green'>info</font>
service), and store it in the same directory as the dataset, with a name
corresponding to the dataset filename. For example, the datasets
<font color='green'>fnoc1.nc</font>,
<font color='green'>fnoc2.nc</font>, and
<font color='green'>fnoc3.nc</font> might be documented with a file
called <font color='green'>fnoc.html</font>.

You may prefer to override this method of creating documentation and
simply provide a single, complete HTML document that contains general
information for the server or for a group of datasets. For example, to
force the info server to return a particular HTML document for all its
datasets, you would create a complete HTML document and give it the name
dataset <font color='green'>.ovr</font>, where dataset is the dataset
name.

More information about providing user information, including sample HTML
files, and a complete description of the search procedure for finding
the dataset documentation, is to be found in
\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
OPeNDAP Programmer's Guide</cite>\].

### Testing the Installation

It is possible to test the OPeNDAP server to see whether an installation
has been properly done. The easiest way to test the installation is with
a simple Web client like Netscape or Mosaic. (A simple Web client called
<font color='green'>geturl</font> is provided in the OPeNDAP core
software which can retrieve text from Web servers. Look for it in the
<font color='green'>\$(DODS_ROOT)/etc</font> directory.)

The simplest test is simply to ask for the version of the server, or the
help message. The OPeNDAP server uses helper programs to return the DAS,
DDS, and data. If you want to test the server itself, and not the
configuration of the helper programs, the version, help, or info
services will suffice. Issuing a URL with
<font color='green'>.ver</font> on the end will return the version
information for this server, appending <font color='green'>.info</font>
will return the info message, and issuing a URL with a nonsense suffix
or <font color='green'>.help</font> will return a help message:

    > geturl http://dods.gso.uri.edu/cgi-bin/nph-nc/data/test.nc.ver
    > geturl http://dods.gso.uri.edu/cgi-bin/nph-nc/data/test.nc.info
    > geturl http://dods.gso.uri.edu/cgi-bin/nph-nc/data/test.nc.help

To return the data attribute structure of a dataset, use a URL such as
the following: ([15](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink"))

    > geturl http://dods.gso.uri.edu/cgi-bin/nph-nc/data/test.nc.das

Refer to ([Section 6.4.2](Wiki_Testing/OPeNDAPUserGuide6 "wikilink"))
for a description of a data attribute structure. You can compare the
description against what is returned by the above URL to test the
operation of the OPeNDAP server.

You can use your web client to test the OPeNDAP server by using it to
submit URLs that address specific services of the client. See ([Section
2.2](Wiki_Testing/OPeNDAPUserGuide2 "wikilink")) for information about
how to request individual services. If any of the services fail, you can
check the list of helper programs in ([Section
5.1.1](Wiki_Testing/OPeNDAPUserGuide5 "wikilink")) to find out which is
missing. From the web browser, you can access all the OPeNDAP services,
except the (binary) data service. However, if all the others work, you
can be relatively assured that one will, too.

Using the <font color='green'>.html</font> suffix produces the \ifh,
providing a forms-based interface with which a user can query the
dataset using a simple web browser. There's more about the \ifh in
([Chapter 3](Wiki_Testing/OPeNDAPUserGuide3 "wikilink")).

## Displaying Information to the OPeNDAP User

OPeNDAP contains a system that allows an OPeNDAP server a degree of
control over the user's graphic user interface (GUI). This system runs
the system progress indicator, that displays to the user the status of a
pending data request. However, a server may also use the GUI interface
to display messages to the user, such as error messages, and even to
query the user for information.

### GUI Architecture

Since OPeNDAP is built inside a data access API, and since the
application program that has become the OPeNDAP client was presumably
not built with network I/O in mind, an OPeNDAP client will typically not
do any processing at all while it awaits a return message from a data
request. Any communication that must happen between the OPeNDAP software
and the user must occur without the involvement of the application
program that has invoked the OPeNDAP software. To avoid this limitation,
OPeNDAP starts up a GUI manager sub-process. This sub-process can
receive data from the OPeNDAP core software, and can operate the user's
graphical user interface.

The operation of the GUI manager is illustrated in [figure
5.3.1](:Image:wish.gif "wikilink"). As seen in the figure, the client
application can usually control the user's screen, but during a data
request, this communication is suspended. Until the request returns
control to the client application, messages returned from the OPeNDAP
server can be displayed to the user by passing them to the GUI manager
sub-process, who can display them in a window to the user.

<center>

<figure>
<img src="wish.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Architecture of an OPeNDAP Client GUI

</center>

The GUI manager in \OPDversion uses a Tcl/Tk interpreter (the
<font color='green'>wish</font> program is the default) to interpret
messages from the server. These messages usually contain Tcl programs to
display information to the user. However, the
<font color='green'>wish</font> interpreter can also be sent programs to
query the user for more information, or draw little rabbits on the
screen or any other graphic function the server needs to have displayed
to the user. See Tcl and the Tk Toolkit~\citel{osterhout:tcl} for more
information about Tcl.

By default, the GUI manager initializes by running the Tcl programs in
the files <font color='green'>dods_gui.tcl</font>,
<font color='green'>error.tcl</font> and
<font color='green'>progress.tcl</font>. (These are stored in
<font color='green'>\$DODS_ROOT/etc</font>.) Server commands to the GUI
manager can use the functions defined in these files. Note also that the
user may be using a "safe" Tcl interpreter, with a restricted subset of
the usual array of Tcl commands available to it. The user can control
these features of the operation of the GUI by changing several
environment variables. These are described in ([<cite>
opd-client,environment</cite>](http://www)).

A server will use the features of the GUI manager to display error
messages to the user. A server may also use the GUI to query a user to
correct whatever condition caused the error. For example, if a user has
misspelled some part of a constraint expression in a URL submitted to a
server, the server can send the constraint expression back to the user
in an edit window, with instructions to fix it. The user can edit the
expression, and send it back, allowing the server to proceed without
submitting a new request. Consult the client and server toolkit manual
for more information about the \class{Error} object on this subject.

## Building OPeNDAP Data Servers

Though servers are included in the OPeNDAP core software, some users may
wish to write their own OPeNDAP data servers. The architecture of the
<font color='green'>httpd</font> server and the OPeNDAP core software
make this a relatively simple task.

A user may wish to write his or her own OPeNDAP server for any or all of
the following reasons:

- The data to be served may be stored in a format not compatible

with one of the existing OPeNDAP servers.

- The data may be arranged in a fashion that allows a user to

optimize the access of those data by rewriting the service programs.

- The user may wish to provide ancillary data to OPeNDAP clients not

anticipated by the writers of the servers available.

The design of the OPeNDAP library make the task a relatively simple one
for a programmer already familiar with the data access API to be used.
Also, though the servers provided with the OPeNDAP core software are
written in C++, they may be written in any language from which the
OPeNDAP libraries may be called.

Once it is invoked, a CGI program scoops up whatever input is going to
the standard input stream of the Web server
(<font color='green'>httpd</font>) that invoked it. Further, the
standard output of the CGI is piped directly to the WWW library, which
sends it directly back to the requesting client. This means that the CGI
program itself need only read its input from standard input and write
its output to standard output.

Most of the task of writing a server, then, consists of reading the data
with the data access API and loading it into the OPeNDAP classes. Method
functions defined for each class make it simple to output the data so
that it may be sent back to the requesting client. Refer to
\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
OPeNDAP Programmer's Guide</cite>\] for specific information about the
classes and the facilities of the OPeNDAP core software, and
instructions about how to write a new server.