# Using OPeNDAP

A user uses OPeNDAP with an OPeNDAP client program. This client program
may have been acquired by the user (for example, the OPeNDAP Matlab and
IDL graphic user interfaces, or Ferret, a freeware data analysis package
each use OPeNDAP for data access), or may be a program converted to use
the OPeNDAP library for data access (see ([<cite>
opd-client</cite>](http://www)).

In either case, there are a set of issues that must be addressed in
order to use a program to access data through OPeNDAP. The issues can be
classed into two groups. One set of issues involves configuring the
system to provide OPeNDAP with the helper applications and environment
variables it requires. The other set concerns the manner in which a user
communicates with an OPeNDAP server. We cover this first

## How OPeNDAP Finds Data

Once linked to the OPeNDAP libraries, an OPeNDAP client created from an
existing program will work exactly as before when run using local files.
However, a user can also specify an OPeNDAP Uniform Resource Locator
(URL) to indicate some data file on a remote host machine. When the
program receives this URL, the OPeNDAP libraries will recognize it as
remote data, and issue a network request for the data. If a user has
also installed an OPeNDAP server on the local machine, then local data
may be accessed either through their local filenames or their OPeNDAP
URL.

A URL is simply a unique name for some Internet resource. The
![Image:opd-client,fig,url-parts](opd-client,fig,url-parts "Image:opd-client,fig,url-parts")
shows the parts of a typical OPeNDAP URL.

\begin{figure}\[h\] \texorhtml {\small
\${}\overbrace{\>dncview}^{Program} \overbrace"http}^{Protocol"://
\overbrace"dods.gso.uri.edu}^{Machine Name"/
\overbrace"cgi-bin/nph-nc}^{Server"/ \overbrace"data}^{Directory"/
\overbrace"fnoc1.nc}^{Filename"/ \overbrace".das}^{URL Suffix}\$"
{\begin{vcode}{cb} \>dncview
<http://dods.gso.uri.edu/cgi-bin/nph-nc/data/fnoc1.nc.das>

^ ^ ^ ^ ^ ^ ^

\| \| \| \| \| \| \| Program \| \| \| \| \| \| Protocol-- \| \| \| \| \|
Machine Name----- \| \| \| \| Server------------------------------------
\| \| \| Directory---------------------------------------- \| \|
Filename---------------------------------------------- \| URL
Suffix----------------------------------------------------- \end{vcode}}
\caption{Parts of an OPeNDAP URL (without a constraint expression)}

\end{figure}

The parts of the URL are:

> protocol :
>
> The protocol of an Internet request may be thought of as the kind of
> conversation the client expects to have with the target machine. For
> example, a web browser like Netscape Navigator wants to find a server
> that can return hypertext documents, while an ftp client wants to find
> a server that can understand file transfer requests. A web browser
> equipped to display hypertext documents will specify
> <font color='green'>http</font> as the protocol for its conversation,
> and hope that the target machine has an
> <font color='green'>httpd</font> daemon listening.
>
> host : The host name in a URL is simply the
>
> Internet address of the host machine running whatever server can reply
> to the specified protocol.
>
> server : A special feature of the <font color='green'>httpd</font> server process is
>
> that it may be configured to execute Common Gateway Interface (CGI)
> programs upon receipt of a properly specified URL. This is used, for
> example, by Internet search engines that ask a user to fill out a
> form. The CGI specification will be specific to the server in
> question, and the part of the URL that follows the CGI name is passed
> to the CGI upon invocation. This data may include a file name, but it
> may as easily be some arbitrary string of instructions. The OPeNDAP
> server is simply a set of CGI scripts executed on demand by the
> <font color='green'>httpd</font> server. Here, the OPeNDAP server is
> represented by a CGI script called <font color='green'>nph-nc</font>.
>
> filename : If a CGI is not
>
> specified, the part of the URL after the host name is simply the name
> of a file that is to be returned to the inquiring browser. If a CGI is
> specified, the file is given to the program as its argument.
>
> URL suffix : If you are issuing an OPeNDAP request
>
> from a non-OPeNDAP client, such as a web browser, you can specify the
> type of request by appending a suffix to the URL. Different suffixes
> demand different services from the server. The different services are
> listed in ([<cite> opd-client,services</cite>](http://www)). If you
> are using OPeNDAP from an OPeNDAP client, or a client program adapted
> to use the OPeNDAP DAP library, you do not need to use a URL suffix.
> For example, to use OPeNDAP from Matlab, with the Matlab GUI or
> command-line clients, you do not need to use a suffix. To use OPeNDAP
> from a simple web browser like Netscape Navigator, you will need to
> use a suffix.

The URL in
![Image:opd-client,fig,url-parts](opd-client,fig,url-parts "Image:opd-client,fig,url-parts")
shows a client request to the <font color='green'>httpd</font> server on
the machine <font color='green'>dods.gso.uri.edu</font>, for a netCDF
dataset (specified by the <font color='green'>nph-nc} in the
\lit{cgi-bin</font> directory) contained in a file called
<font color='green'>fnoc1.nc}. Upon receiving this URL, the
\lit{httpd</font> server executes the specified OPeNDAP server module
(<font color='green'>nph-nc</font>), which retrieves the file is in a
directory called <font color='green'>data</font> relative to wherever
the <font color='green'>httpd</font> server looks for its
data\footnote{The only

part of the URL whose spelling is not at the discretion of the
administrator of the host machine is the
<font color='green'>http</font>, and the <font color='green'>nph-</font>
at the beginning of the CGI script name. Even the
<font color='green'>nc</font>, indicating netCDF, can be changed,
although for clarity's sake, we hope people won't do so. Incidentally,
the <font color='green'>nph-</font> is a relic, dating from the early
days of the World Wide Web and the first hypertext protocol standards.
It stands for "Non-Parsing Header" (See the CGI 1.1 Standard for more
information.), and is the only way to pass data through many httpd
servers unparsed.}.

OPeNDAP URLs can get somewhat more complicated than this simple
description. In particular, they can contain "constraint expressions"
that limit a request to data satisfying a set of conditions, and they
can contain requests to specific OPeNDAP services, besides the data
delivery service suggested here. Constraint expressions are described in
more detail in ([<cite> opd-client,constraint</cite>](http://www)),
while the array of services provided by OPeNDAP servers are described in
([<cite> opd-client,services</cite>](http://www)).

### Security

Some OPeNDAP data providers will choose to control access to some or all
of their data. When you request data from one of these servers, the
OPeNDAP client will prompt you for a username and password. If you want
to avoid the prompt, you can make the OPeNDAP URL even more baroque by
embedding a username and password in it, like this:

\begin{vcode}{sib} <http://user:password@www.dods.org/nph-dods/etc>...
\end{vcode}

## The OPeNDAP Services

Up to now, we have treated the OPeNDAP server as if it has only one
service: providing data to clients who ask for it. It is true that this
is the most important service a server provides. However, it is also
true that the server provides several other services besides that. In
fact, fulfilling a request for data actually requires three separate
requests from the client, using three different services of the OPeNDAP
server.

The services requested from an OPeNDAP server are specified in a suffix
appended to the URL described in
![Image:opd-client,fig,url-parts](opd-client,fig,url-parts "Image:opd-client,fig,url-parts").
Depending on the suffix supplied, the server will provide one of these
services:

> Data Attribute : This service returns the entire data
>
> attribute structure for the given dataset. This is a text file
>
> describing the attributes of each data quantity in that dataset.
>
> (See ([<cite> data,das</cite>](http://www)) for more information about
> data
>
> attributes.) This service is activated when the
>
> server receives a URL ending with <font color='green'>.das</font>.
>
> Data Descriptor : This service returns the entire data descriptor
>
> structure for the given dataset. This is a text file describing the
> structure of the variables in the dataset. (See ([<cite>
> data,dds</cite>](http://www)) for more information about data
> descriptors.) This service is activated when the server receives a URL
> ending with <font color='green'>.dds</font>.
>
> OPeNDAP Data : This service returns the actual data requested by
>
> a given URL. This is not a text file, but is encoded as a Multipurpose
> Internet Mail Extensions (MIME) document. This service is activated
> when the server receives a URL ending with
> <font color='green'>.dods</font>
>
> ASCII Data : This service returns an ASCII representation of
>
> the requested data. This can make the data available to a wide variety
> of browser programs. This service is activated when the server
> receives a URL ending with <font color='green'>.asc} or
> \lit{.ascii</font>.
>
> \ifh : When the server receives a URL ending in
>
> <font color='green'>.html</font>, it produces an HTML form containing
> information from the dataset that you can use to construct a sensible
> URL with which to request OPeNDAP data. The \ifh is also triggered
> when the OPeNDAP server receives a URL that references a directory
> instead of a file.
>
> Information : This service returns information about
>
> the server and dataset, in human-readable HTML form. The returned
> document may include information about both the data server itself
> (e.g. server functions implemented), and the dataset referenced in the
> URL. The server administrator determines what information is returned
> in response to such a request. This service is activated when the
> server receives a URL ending with <font color='green'>.info</font>.
> See ([<cite> sec,document-data</cite>](http://www)) for more
> information about how to configure the information service.
>
> Version : This service returns the version information for the
>
> OPeNDAP server software running on the server. This service is
> triggered by a URL ending with <font color='green'>.ver</font>.
>
> Help : This service returns some help text in response to an
>
> improperly specified URL. This service is triggered by a URL ending in
> any suffix that is not recognized by the OPeNDAP server.

> A request for data from an OPeNDAP client will generally make three
> different service requests, for data attributes, data descriptors, and
> for data. The prepackaged OPeNDAP clients do this for you, so you may
> not be aware that three requests are made for each URL. That is, an
> OPeNDAP client may accept an OPeNDAP URL specifying some data, such as
> the one shown in
> ![Image:opd-client,fig,url-parts](opd-client,fig,url-parts "Image:opd-client,fig,url-parts").
> In this case, the OPeNDAP client library (such as nc-dods) will accept
> the input URL, and append the different suffixes to that URL, making
> three distinct requests to the OPeNDAP server.

### \ifh

Each OPeNDAP server implements a service called the \ifh . This is a way
to use a standard Web client, such as Netscape, to get information about
the data served by a specific server.\footnote{The \ifh is only

available for servers later than version 3.1.} The \ifh has two modes of
operation: the directory level and the file level.

If an OPeNDAP URL references a directory instead of a file on the server
machine, the server produces a listing similar to that shown in
![Image:opd-client,fig,ifh-dir](opd-client,fig,ifh-dir "Image:opd-client,fig,ifh-dir").

\figureplace{\ifh - Directory Level}{htbp}
{opd-client,fig,ifh-dir}{ifh-dir.ps}{ifh-dir.gif}{}

Clicking on a dataset shown in the directory-level listing will produce
an HTML form similar to the one in
![Image:opd-client,fig,ifh](opd-client,fig,ifh "Image:opd-client,fig,ifh").
The top line in the window ("Data URL") shows a URL that makes a request
for an OPeNDAP dataset. The windows below it show the variables that
make up the dataset. You can edit the form to select the data you'd like
to see from this dataset, and the \ifh will edit the Data URL so that it
only requests the data you are interested in. When done, you can push
the "ASCII" button, to see an ASCII representation of the data you've
requested. Netscape cannot handle binary data, so if you want to use the
binary data, you should copy the URL in the Data URL window to the
OPeNDAP client you'd like to use.

\figureplace{\ifh}{htbp} {opd-client,fig,ifh}{ifh.ps}{ifh.gif}{}

## Using an OPeNDAP Program

There are some configuration issues a user must consider in order to use
an OPeNDAP client application program. There is a short list of software
that is required for some of the advanced features of OPeNDAP, and some
environment variables that control the execution of the OPeNDAP
software. For a piece of software that has been converted to use
OPeNDAP, after these conditions are satisfied, the program will run in
the same manner it ran before. Aside from network delays, the user
should not be able to tell that they are accessing data from the
Internet.

Finally, though it may seem unnecessary to mention, in order for an
OPeNDAP client application to communicate with an OPeNDAP server, the
computer running the OPeNDAP client must be connected to the Internet.

### Requirements

In order to use of some of the features of the OPeNDAP core software, a
user's computer must have some additional software installed, and
available on the user's <font color='green'>PATH</font>, in
<font color='green'>\$DODS_ROOT/bin} or \lit{\$DODS_ROOT/etc</font>.

\indc{system

configuration}

- The <font color='green'>wish} {Tcl}}/{\ind{Tk</font> interpreter (or
  whatever

program is indicated by the <font color='green'>DODS_GUI</font>
environment variable) is used by the "GUI manager" to provide a progress
indicator that displays the status of a pending data request as it is
being processed. It is also used by the error reporting system to
display error message received from the server. \tbd{and by the data
locator, to display information and query the user}

- The <font color='green'>gzip}</font> program, the \ind{GNU compression

software, is used to decompress data messages received from an OPeNDAP
server. If this program is not installed, the OPeNDAP core software
tells the server not to send compressed messages, so data may still be
received. However, having the compression software installed and
available will increase the data transfer rate.

The required software, like OPeNDAP itself, is free software. Refer to
\appref{install} for information about acquiring that software.

### Environment Variables

After successfully relinking an application program with the OPeNDAP
libraries, there is a short list of environment variables that may be
defined. Only <font color='green'>DODS_ROOT</font> is required. The
other three variables are only used to override default values
controlling the GUI manager process. Most users may safely ignore them.

> <font color='green'>DODS_ROOT</font> : indicates the root directory of the OPeNDAP
>
> software. The OPeNDAP core software must be able to locate utilities
> that are located in this directory tree. \indc{environment
> variables!DODS_ROOT}
>
> <font color='green'>DODS_GUI</font> : can contain the name of the program used by the
>
> \new{GUI manager}. A user might wish to change this variable to point
> to a "safe" Tcl/Tk interpreter; whatever program is used here must be
> able to process Tcl and Tk commands. The default value is the
> <font color='green'>wish</font> program. \indc{environment
> variables!DODS_GUI}
>
> <font color='green'>DODS_GUI_INIT</font> : indicates the name of any initialization
>
> command required by the "GUI manager". The default initialization
> string executes the Tcl program in
> <font color='green'>\$DODS_ROOT/etc/dods_gui.tc1</font>.
> \indc{environment variables!DODS_GUI_INIT}
>
> <font color='green'>DODS_USE_GUI</font> : may be used to turn off the GUI manager. Set
>
> the value of this variable to <font color='green'>no</font>, and the
> progress indicator and the error message windows will not be
> displayed.

> The user has substantial control over the GUI manager. You can change
> the program that listens for GUI commands from
> <font color='green'>wish</font> to anything else, and you can actually
> change the action of the GUI commands by editing the Tcl code in the
> files <font color='green'>dods_gui.tcl</font>,
> <font color='green'>error.tcl}, and \lit{progress.tcl</font>. (These
> are in the <font color='green'>\$DODS_ROOT/etc</font> directory.)
> However, editing these files and variables will not change the form of
> the messages from the OPeNDAP server, and from the core software that
> are meant to invoke these programs. In other words, the user may mess
> with these, but must be careful to leave the GUI manager in a form
> that will be able to process the messages it receives.

### The Error System

The GUI manager is used to display error messages to the user. The
messages themselves will vary with the server implementation. Refer to
the documentation of the particular server, or consult the server's
<font color='green'>info</font> Service (See ([<cite>
opd-server,service</cite>](http://www)).), for a list of the error
messages that might be issued by a particular server. \tbd{As error
codes are finalized, they should be included in an Appendix of this
document, and a pointer to them included here.}

### Temporary Files

Using an OPeNDAP client application will create a number of temporary
files. They are created with the <font color='green'>tmpnam()</font>
function, so their names will correspond to the rules for that function
on your system (See the manual page for <font color='green'>tmpnam(3)},
or type \lit{man tmpnam</font> for more information.) During normal
operation, OPeNDAP will delete the temporary files it creates as it
goes. However, if execution of the OPeNDAP client is somehow
interrupted, these files may remain, and will have to be deleted by
hand.

This template will add tagged articles to
[:UserGuide1](:UserGuide1 "wikilink").