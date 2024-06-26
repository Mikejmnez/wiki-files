# Using OPeNDAP

A user uses OPeNDAP with an OPeNDAP client program. This client program
may have been acquired by the user (for example, the OPeNDAP Matlab and
IDL graphic user interfaces, or Ferret, a freeware data analysis package
each use OPeNDAP for data access), or may be a program converted to use
the OPeNDAP library for data access (see ([Chapter
3](Wiki_Testing/OPeNDAPUserGuide3 "wikilink")).

In either case, there are a set of issues that must be addressed in
order to use a program to access data through OPeNDAP. The issues can be
classed into two groups. One set of issues involves configuring the
system to provide OPeNDAP with the helper applications and environment
variables it requires. The other set concerns the manner in which a user
communicates with an OPeNDAP server. We cover this first.

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

A URL is simply a unique name for some Internet resource. The figure
shows the parts of a typical OPeNDAP URL.

    >dncview http://dods.gso.uri.edu/cgi-bin/nph-nc/data/fnoc1.nc.das

    ^     ^      ^                        ^      ^    ^        ^

    |     |      |                        |      |    |        |
    Program  |      |                        |      |    |        |
    Protocol--      |                        |      |    |        |
    Machine Name-----                        |      |    |        |
    Server------------------------------------      |    |        |
    Directory----------------------------------------    |        |
    Filename----------------------------------------------        |
    URL Suffix-----------------------------------------------------

    Parts of an OPeNDAP URL (without a constraint expression

The parts of the URL are:

> protocol :The protocol of an Internet request may be thought of as the kind
>
> of conversation the client expects to have with the target machine.
> For example, a web browser like Netscape Navigator wants to find a
> server that can return hypertext documents, while an ftp client wants
> to find a server that can understand file transfer requests. A web
> browser equipped to display hypertext documents will specify
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
> listed in ([Section2.2](Wiki_Testing/OPeNDAPUserGuide2 "wikilink")).
> If you are using OPeNDAP from an OPeNDAP client, or a client program
> adapted to use the OPeNDAP DAP library, you do not need to use a URL
> suffix. For example, to use OPeNDAP from Matlab, with the Matlab GUI
> or command-line clients, you do not need to use a suffix. To use
> OPeNDAP from a simple web browser like Netscape Navigator, you will
> need to use a suffix.

The URL in figure shows a client request to the
<font color='green'>httpd</font> server on the machine
<font color='green'>dods.gso.uri.edu</font>, for a netCDF dataset
(specified by the <font color='green'>nph-nc</font> in the
<font color='green'>cgi-bin</font> directory) contained in a file called
<font color='green'>fnoc1.nc</font>. Upon receiving this URL, the
<font color='green'>httpd</font> server executes the specified OPeNDAP
server module (<font color='green'>nph-nc</font>), which retrieves the
file is in a directory called <font color='green'>data</font> relative
to wherever the <font color='green'>httpd</font> server looks for its
data([6](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink")).

OPeNDAP URLs can get somewhat more complicated than this simple
description. In particular, they can contain "constraint expressions"
that limit a request to data satisfying a set of conditions, and they
can contain requests to specific OPeNDAP services, besides the data
delivery service suggested here. Constraint expressions are described in
more detail in
([Section4.1](Wiki_Testing/OPeNDAPUserGuide4 "wikilink")), while the
array of services provided by OPeNDAP servers are described in
([Section2.2](Wiki_Testing/OPeNDAPUserGuide2 "wikilink")).

### Security

Some OPeNDAP data providers will choose to control access to some or all
of their data. When you request data from one of these servers, the
OPeNDAP client will prompt you for a username and password. If you want
to avoid the prompt, you can make the OPeNDAP URL even more baroque by
embedding a username and password in it, like this:

    http://user:password@www.dods.org/nph-dods/etc...

## The OPeNDAP Services

Up to now, we have treated the OPeNDAP server as if it has only one
service: providing data to clients who ask for it. It is true that this
is the most important service a server provides. However, it is also
true that the server provides several other services besides that. In
fact, fulfilling a request for data actually requires three separate
requests from the client, using three different services of the OPeNDAP
server.

The services requested from an OPeNDAP server are specified in a suffix
appended to the URL described in figure 2.1. Depending on the suffix
supplied, the server will provide one of these services:

> Data Attribute : This service returns the entire data
>
> attribute structure for the given dataset. This is a text file
> describing the attributes of each data quantity in that dataset. (See
> ([Section6.4.2](Wiki_Testing/OPeNDAPUserGuide6 "wikilink")) for more
> information about data attributes.) This service is activated when the
> server receives a URL ending with <font color='green'>.das</font>.
>
> Data Descriptor : This service returns the entire data descriptor
>
> structure for the given dataset. This is a text file describing the
> structure of the variables in the dataset. (See
> ([Section6.4.1](Wiki_Testing/OPeNDAPUserGuide6 "wikilink")) for more
> information about data descriptors.) This service is activated when
> the server receives a URL ending with <font color='green'>.dds</font>.
>
> OPeNDAP Data : This service returns the actual data requested by
>
> a given URL. This is not a text file, but is encoded as a Multipurpose
> Internet Mail Extensions (MIME) document. This service is activated
> when the server receives a URL ending with
> <font color='green'>.dods</font>
>
> ASCII Data : This service returns an ASCII representation of the requested data. This can make the data available to a wide
>
> variety of browser programs. This service is activated when the server
> receives a URL ending with <font color='green'>.asc</font> or
> <font color='green'>..ascii</font>.
>
> WWW Interface : When the server receives a URL ending in <font color='green'>.html</font>, it produces an HTML form containing information from
>
> the dataset that you can use to construct a sensible URL with which to
> request OPeNDAP data. The \ifh is also triggered when the OPeNDAP
> server receives a URL that references a directory instead of a file.
>
> Information : This service returns information about
>
> the server and dataset, in human-readable HTML form. The returned
> document may include information about both the data server itself
> (e.g. server functions implemented), and the dataset referenced in the
> URL. The server administrator determines what information is returned
> in response to such a request. This service is activated when the
> server receives a URL ending with <font color='green'>.info</font>.
> See ([Section5.2.3](Wiki_Testing/OPeNDAPUserGuide5 "wikilink")) for
> more information about how to configure the information service.
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

> NOTE:A request for data from an OPeNDAP client will generally make
> three different service requests, for data attributes, data
> descriptors, and for data. The prepackaged OPeNDAP clients do this for
> you, so you may not be aware that three requests are made for each
> URL. That is, an OPeNDAP client may accept an OPeNDAP URL specifying
> some data, such as the one shown in
> ![Image:opd-client,fig,url-parts](opd-client,fig,url-parts "Image:opd-client,fig,url-parts").
> In this case, the OPeNDAP client library (such as nc-dods) will accept
> the input URL, and append the different suffixes to that URL, making
> three distinct requests to the OPeNDAP server.

### WWW Interface

Each OPeNDAP server implements a service called the WWW Interface . This
is a way to use a standard Web client, such as Netscape, to get
information about the data served by a specific server.
([7](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink")) The WWW
Interface has two modes of operation: the directory level and the file
level.

If an OPeNDAP URL references a directory instead of a file on the server
machine, the server produces a listing similar to that shown in [figure
2.2.1](:Image:ifh-dir.gif "wikilink")

<center>

<figure>
<img src="ifh-dir.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

WWW Interface - Directory Level

</center>

Clicking on a dataset shown in the directory-level listing will produce
an HTML form similar to the one in [figure
2.2.1](:Image:ifh-dir.gif "wikilink"). The top line in the window ("Data
URL") shows a URL that makes a request for an OPeNDAP dataset. The
windows below it show the variables that make up the dataset. You can
edit the form to select the data you'd like to see from this dataset,
and the \ifh will edit the Data URL so that it only requests the data
you are interested in. When done, you can push the "ASCII" button, to
see an ASCII representation of the data you've requested. Netscape
cannot handle binary data, so if you want to use the binary data, you
should copy the URL in the Data URL window to the OPeNDAP client you'd
like to use.

<center>

<figure>
<img src="ifh.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

WWW Interface

</center>