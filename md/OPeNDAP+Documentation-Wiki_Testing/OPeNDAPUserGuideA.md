# Installing the OPeNDAP Software

The current version of the Distributed Oceanographic Data System core
software is 3.2. Note that this number applies only to the core
software. The individual servers, clients, and client libraries have
their own version numbers.

Full information about the latest versions of the OPeNDAP software is
available from the OPeNDAP web site: [OPeNDAP Home
Page](http://www.opendap.org).

This version of OPeNDAP uses CGI standard 1.1.1.

## Acquiring the OPeNDAP Software

#### The OPeNDAP Web Site

We recommend that you use the [OPeNDAP Home
Page](http://www.opendap.org) to obtain the most current version of the
software. The "Software" page includes a form for selecting OPeNDAP
components. Completing the form automatically creates a custom
compressed archive of all the software components you selected, which
you can then download to your own machine.

Whenever possible, you should use the provided binary software rather
than trying to compile and link OPeNDAP yourself. Compiling will work,
but the OPeNDAP software is large, and it takes a long time to compile.

Don't forget to select the core software as well as the specific data
access API or server you need. For an OPeNDAP client, you will also need
<font color='green'>Tcl/Tk</font> and <font color='green'>gzip</font>.

#### By Anonymous FTP

The OPeNDAP software may be down-loaded by anonymous
<font color='green'>ftp</font> from the [<cite>OPeNDAP ftp
site</cite>](http://opendap.org/pub/dods).

Don't forget to down-load the core software
<font color='green'>tar</font> file as well as the
<font color='green'>tar</font> file corresponding to whatever data
access API you need.

The OPeNDAP project provides a small number of archive files containing
linked libraries and executables for some computing platforms. You
should use these files whenever possible, to avoid the hassle of
compiling the software yourself. The OPeNDAP software is a substantial
chunk of code, and it takes a long time to compile and link.

## Installing the Software

To install the OPeNDAP software, choose a directory to be the OPeNDAP
root directory. This directory must be identified with the
<font color='green'>DODS_ROOT</font> environment variable for the
OPeNDAP software to run.

First, set the working directory to the
<font color='green'>DODS_ROOT</font> directory.For example,

    export DODS_ROOT=/usr/local/DODS
    cd $DODS_ROOT

Next, expand the archives with <font color='green'>gzip</font> and
unpack the expanded files with <font color='green'>tar</font>:

    gzip -d DODS-88772.tar.gz
    tar -xvf DODS-88772.tar

If you got the files via anonymous ftp, you would have to repeat the
process for each down-loaded archive file, specifying the name of each
component file. For example:

    gzip -d DODS-core-2.19.tar.gz
    tar -xvf DODS-core-2.19.tar

#### Building the OPeNDAP Core

Unpacking the core software archive will create a configure script in
the <font color='green'>DODS_ROOT</font> directory. The core software
may then be built with:

    cd $DODS_ROOT
    ./configure
    make

If you downloaded binary files, you can skip the "building" steps.

#### Building an OPeNDAP Client or Server

Unpacking the client library archives in the
<font color='green'>DODS_ROOT</font> directory will produce directories
such as <font color='green'>jg-dods</font> for the JGOFS software and
<font color='green'>nc-dods</font> for the netCDF software. These will
appear in the <font color='green'>src</font> subdirectory under
<font color='green'>DODS_ROOT</font>. Each of these directories will
also be equipped with a configure script to create a makefile, so the
build procedure for them is the same as for the core software, for
example:

    cd $DODS_ROOT/src/nc-dods
    ./configure
    make

#### Getting the Software Used by DODS

Several pieces of software are required to run OPeNDAP. These are all
free software, and are descibed in
[Appendix](Wiki_Testing/OPeNDAPUserGuideA "wikilink").

### Installing the OPeNDAP Libraries

In order to link and run client programs, the only OPeNDAP software that
must be installed are the OPeNDAP libraries. The following libraries,
described in ([<cite> opd-client,link</cite>](http://www)) must be
installed in the <font color='green'>/usr/lib</font> directory, or
somewhere else where the linker will find them. Simply copy them into
that directory from the OPeNDAP distribution.

- <font color='green'>libdap++.a</font>
- The OPeNDAP version of the API your software uses.

## The OPeNDAP Client Initialization File (<font color='green'>.dodsrc</font>)

The following section refers only to OPeNDAP clients from release 3.2
and after.

When an OPeNDAP client starts up, it checks to see whether the user has
an initialization file available to control the setting of a number of
parameters having to do with caching, proxy servers, and other http
issues. If this file is not found, one is created in the user's home
directory, using default
values.[(24)](Wiki_Testing/OPeNDAPUserGuideFootnotes "wikilink")

The client initialization file is usually called
<font color='green'>.dodsrc</font>, and is usually located in the user's
home directory. You can change this by creating an environment variable
called <font color='green'>DODS_CONF</font> and setting it to the full
pathname of the configuration file.

Here is a sample configuration file:

    # OPeNDAP client configuration file. See the OPeNDAP
    # users guide for information.
    USE_CACHE=0
    # Cache and object size are given in megabytes (20 ==> 20Mb).
    MAX_CACHE_SIZE=20
    MAX_CACHED_OBJ=5
    IGNORE_EXPIRES=0
    CACHE_ROOT=/home/jimg/.dods_cache/
    DEFAULT_EXPIRES=86400
    ALWAYS_VALIDATE=0

    # Request servers compress responses if possible?
    # 1 (yes) or 0 (false).
    DEFLATE=0

    # Should SSL certificates and hosts be validated? SSL
    # will only work with signed certificates.
    VALIDATE_SSL=1

    # Proxy configuration (optional parts in []s):
    # You may also use the 'http_proxy' environment variable.
    # A value in this file will override that env variable.
    # PROXY_SERVER=<http://[username:password@]host[:port]>
    # NO_PROXY_FOR=<host|domain>
    PROXY_SERVER=http://username:password@squid.proxy.org:3128/
    NO_PROXY_FOR=localhost

    # AIS_DATABASE=<file or url>

#### Comments

Starting a line with a <font color='green'>\#</font> makes that line a
comment.

#### Caching

The first seven parameters control caching. The OPeNDAP client can store
data you've requested on your local computer. If you repeat a request,
the data can be retrieved from this local cache, saving the expense of a
network connection. Most web browsers operate the same way. You can
control the caching behavior with the following configuration file
parameters.

> <font color='green'>USE_CACHE</font> : 1 turns on caching and 0 turns it off.
> <font color='green'>MAX_CACHE_SIZE</font> : The value of <font color='green'>MAX_CACHE_SIZE</font> sets the maximum size of the cache in megabytes. Once the cache reaches this size, caching more objects will cause cache garbage collection. OPeNDAP will first purge the cache of any stale entries and then remove remaining entries starting with those that have the lowest hit count. Garbage collection stops when 90% purged.
> <font color='green'>MAX_CACHED_OBJ</font> : This parameter sets the maximum size of any individual object in the cache in megabytes. Objects received from a server larger than this value will not be cached even if there's room for them without purging other objects.

Many web documents, and OPeNDAP data, are delivered with an expiration
date in their header information. Generally, this is done for
time-sensitive information that may not be valid after the expiration
date. You can control the behavior of the OPeNDAP client with respect to
expiration dates with two configuration file parameters.

> <font color='green'>IGNORE_EXPIRES</font> : If the value of this parameter is 1 (one), then expiration dates will be ignored, and the caching behavior will be ruled by the <font color='green'>DEFAULT_EXPIRES</font> parameter.
> <font color='green'>CACHE_ROOT</font> : This parameter contains the pathname to the cache's top directory. If two or more users want to share a cache, then they must both have read and write permissions to the cache root. NB: Do not share caches unless you're using libdap version 3.8.1 or newer.
> <font color='green'>DEFAULT_EXPIRES</font> : Any data received without an expiration time will expire in the number of seconds given by this parameter. If <font color='green'>IGNORE_EXPIRES</font> is zero, this will apply to all data, whether or not it comes with an expiration date. The value is given in seconds. The configuration in the example is set for 24 hours.
> <font color='green'>ALWAYS_VALIDATE</font> : Entries in a cache might be stale even though the default expiration time has not elapsed and some servers might not provide expiration dates for all of their responses. Setting this to '1' forces all cache entries to be validated before the client will use them. This makes for slower response times than just assuming the cached data is valid, but it is faster than fetching new data for every request.

#### Proxy Servers

An OPeNDAP client can negotiate proxy servers, with help from directions
derived from its configuration file. There are three parameters that
control proxy behavior. There can be more than one of each of these
declarations.

> <font color='green'>PROXY_SERVER</font> : This identifies a proxy server to use for all OPeNDAP requests, except for requests specifically modified by the other two proxy behavior directives. The format is:
>
> <!-- -->
>
>     PROXY_SERVER=http://[username:password@]<host>[:port]
>
> The username, password and port are all optional elements. If no
> username and password is given the client will assume that none is
> needed. If the port is not given, the default value of 80 will be
> used. This syntax is new for clients using libdap 3.8.1 and newer; the
> old syntax is still supported but was error-prone and should not be
> used in new <font color='green'>.dodsrc</font> files.
>
> <font color='green'>NO_PROXY_FOR</font> : Use this parameter to say that access to a certain host should never go through a proxy.

#### Compression

Many OPeNDAP servers support compression of the returned data. This can
save network bandwidth, and transmission time. You can tell your client
to ask the server for compressed data by including a line in your
<font color='green'>.dodsrc</font> file like this:

    DEFLATE=1

Using a value of zero will make the client request only uncompressed
data. If the directive is omitted, the default value is zero.

#### SSL Validation

SSL Validation can be turned off using the
<font color='green'>VALIDATE_SSL</font> parameter. if set to '1' all SLL
hosts and certificates will be validated. The certificates must be
signed for this to work. The default is on. Set to '0' to turn off.

# Software you will need for OPeNDAP's Software

Most modern computers has all the basics in place. However, in some rare
cases you might be stuck without some tools that you'll need to unpack
our software. If that's the case here's what you'll need:

> gzip : This is the GNU compression and de-compression program. You will need to install it before you can unpack any of the other software described here.
> tar : This is used to bundle many files together into a single package and it is how we (and many others) package source code.

Most of the software you need for OPeNDAP is avaliable from the GNU
archives. Refer to
[<cite><http://www.gnu.org></cite>](http://www.gnu.org) for
instructions. Look at
[<cite><http://www.gnu.org/order/ftp.html></cite>](http://www.gnu.org/order/ftp.html)for
a list of mirrors of that archive. Use the mirror closest to you, the
transmission will be faster.

Follow the instructions to install each of the following software
packages. Typically, you would install a package called
<font color='green'>foo</font> as follows:

    gzip -dc foo.tar.gz | tar xvf -
    cd foo
    ./configure
    make
    make install

This is simply a guide, of course, and the installation instructions for
each software package should be followed carefully.

## Building OPeNDAP's Software Using the Source Code

If you need to build the OPeNDAP software, or link it to existing
libraries, you will need the following GNU software. Refer to
[<cite><http://www.gnu.org></cite>](http://www.gnu.org) forinstructions.
Look at [<cite><http://www.gnu.org/order/ftp.html>
</cite>](http://www.gnu.org/order/ftp.html)for a list of mirrors of that
archive. Use the mirror closest to you, the transmission will be faster.

> GNU C/C++ Compiler : OPeNDAP needs <font color='green'>g++</font>, the GNU C++ compiler to compile.
> binutils : The GNU linker is part of this package.
> libstdc++ : The standard C++ library. Generally part of the C++ compiler so you rarely ever need to get this on its own.
> GNU Make : GNU Make is not essential, but will make like easier.
> flex : The GNU lexical-analyzer generator
> bison : The GNU parser generator.
> CppUnit : Optional; You'll need this to run the unit tests included with our software
> DejaGNU : Optional; Needed to run the regression tests included with our software
> Doxygen : Optional; Needed to build the documentation