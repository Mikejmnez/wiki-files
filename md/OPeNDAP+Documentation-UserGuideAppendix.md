# Installing the OPeNDAP Software

The current version of the Distributed Oceanographic Data System core
software is \OPDversion . Note that this number applies only to the core
software. The individual servers, clients, and client libraries have
their own version numbers.

Full information about the latest versions of the OPeNDAP software is
available from the OPeNDAP web site: \OPDhome .

This version of OPeNDAP uses CGI standard 1.1.1.

## Acquiring the OPeNDAP Software

#### The OPeNDAP Web Site

We recommend that you use the \OPDhome to obtain the most current
version of the software. The "Software" page includes a form for
selecting OPeNDAP components. Completing the form automatically creates
a custom compressed archive of all the software components you selected,
which you can then download to your own machine.

Whenever possible, you should use the provided binary software rather
than t rying to compile and link OPeNDAP yourself. Compiling will work,
but the OPeNDAP software is large, and it takes a long time to compile.

Don't forget to select the core software as well as the specific data
access API or server you need. For an OPeNDAP client, you will also need
<font color='green'>Tcl/Tk</font> and <font color='green'>gzip</font>.

#### By Anonymous FTP

The OPeNDAP software may be down-loaded by anonymous
<font color='green'>ftp</font> from the \OPDftp .

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
free software, and are descibed in \appref{req-software}.

### Installing the OPeNDAP Libraries

In order to link and run client programs, the only OPeNDAP software that
must be installed are the OPeNDAP libraries. The following libraries,
described in ([<cite> opd-client,link</cite>](http://www)) must be
installed in the <font color='green'>/usr/lib</font> directory, or
somewhere else where the linker will find them. Simply copy them into
that directory from the OPeNDAP distribution.

- <font color='green'>libdap++.a</font>
- The OPeNDAP version of the API your software uses.

In addition to the OPeNDAP libraries, the following software is also
required to link an OPeNDAP client. You can obtain them from the
\OPDhome or \OPDftp . (Look under your machine type, then under
<font color='green'>DODS/packages/lib</font>.) The first two libraries
should be part of the Tcl/Tk distribution. The other three libraries are
GNU software.

- <font color='green'>libtcl.a</font>
- <font color='green'>libexpect.a</font>
- <font color='green'>libstdc++.a</font>
- <font color='green'>librx.a</font> (Regular expression software. Part
  of the

<font color='green'>regex</font> package.)

- <font color='green'>libz.a</font> (Compression software. Part of the
  <font color='green'>gzip</font>

distribution.)

To run an OPeNDAP client, you should have the
<font color='green'>wish</font> and <font color='green'>gzip</font>
programs in the <font color='green'>PATH</font>, and the following Tcl
programs must be either in the <font color='green'>DODS_ROOT/etc</font>
directory:

- <font color='green'>dod_gui.tcl</font>
- <font color='green'>progress.tcl</font>
- <font color='green'>error.tcl</font>

An OPeNDAP client will still execute without these programs, but
important functionality, including the GUI manager and the data
compression will be absent. Refer to the
<font color='green'>INSTALL</font> file included in each distribution
for specific information about setting up the client.

## The OPeNDAP Client Initialization File (<font color='green'>.dodsrc</font>)

The following section refers only to OPeNDAP clients from release 3.2
and after.

When an OPeNDAP client starts up, it checks to see whether the user has
an initialization file available to control the setting of a number of
parameters having to do with caching, proxy servers, and other http
issues. If this file is not found, one is created in the user's home
directory, using default values.\footnote{The default values enable a
maximum cache size of 20

megabytes, a cache expiration time of 24 hours, and no proxy servers.}

The client initialization file is usually called
<font color='green'>.dodsrc</font>, and is usually located in the user's
home directory. You can change this by creating an environment variable
called <font color='green'>DODS_CACHE_INIT</font> and setting it to the
full pathname of the configuration file. As of OPeNDAP version 3.4, you
should use <font color='green'>DODS_CONF</font> instead.
<font color='green'>DODS_CACHE_INIT</font> is deprecated, and will
disappear in future releases.

Here is a sample configuration file:

\begin{vcode}{inb}

1.  Sample configuration file

USE_CACHE=1 MAX_CACHE_SIZE=20 MAX_CACHED_OBJ=5 IGNORE_EXPIRES=0
CACHE_ROOT=/home/user/.dods_cache/ DEFAULT_EXPIRES=86400
PROXY_SERVER=http,http://dcz.dods.org/
PROXY_FOR=<http://dax.dods.org/>.\*,http://dods.org/
NO_PROXY_FOR=<http://dcz.dods.org> DEFLATE=1 ALWAYS_VALIDATE=0

</pre>

#### Comments

Starting a line with a <font color='green'>\\</font> makes that line a
comment.

#### Caching

The parameters on lines 2 through 7 control caching. The OPeNDAP client
can store data you've requested on your local computer. If you repeat a
request, the data can be retrieved from this local cache, saving the
expense of a network connection. Most web browsers operate the same way.
You can control the caching behavior with the following configuration
file parameters.

> <font color='green'>CACHE_ROOT</font> : This parameter contains the pathname to the
>
> cache's top directory. If two or more users want to share a cache,
> then they must both have read and write permissions to the cache root.
>
> <font color='green'>MAX_CACHE_SIZE</font> : The value of <font color='green'>MAX_CACHE_SIZE</font> sets the maximum size of the cache in megabytes. Once the cache reaches
>
> this size, caching more objects will cause cache garbage collection.
> OPeNDAP will first purge the cache of any stale entries and then
> remove remaining entries starting with those that have the lowest hit
> count. Garbage collection stops when 90\\ purged.
>
> <font color='green'>MAX_CACHED_OBJ</font> : This parameter sets the maximum size of
>
> any individual object in the cache in megabytes. Objects received from
> a server larger than this value will not be cached even if there's
> room for them without purging other objects.

Many web documents, and OPeNDAP data, are delivered with an expiration
date in their header information. Generally, this is done for
time-sensitive information that may not be valid after the expiration
date. You can control the behavior of the OPeNDAP client with respect to
expiration dates with two configuration file parameters.

> <font color='green'>IGNORE_EXPIRES</font> : If the value of this parameter is 1
>
> (one), then expiration dates will be ignored, and the caching behavior
> will be ruled by the <font color='green'>DEFAULT_EXPIRES</font>
> parameter.
>
> <font color='green'>DEFAULT_EXPIRES</font> : Any data received without an expiration
>
> time will expire in the number of seconds given by this parameter. If
> <font color='green'>IGNORE_EXPIRES</font> is zero, this will apply to
> all data, whether or not it comes with an expiration date. The value
> is given in seconds. The configuration in the example is set for 24
> hours.

#### Proxy Servers

An OPeNDAP client can negotiate proxy servers, with help from directions
derived from its configuration file. There are three parameters that
control proxy behavior. There can be more than one of each of these
declarations.

> <font color='green'>PROXY_SERVER</font> : This identifies a proxy server to use for
>
> all OPeNDAP requests, except for requests specifically modified by the
> other two proxy behavior directives. The format is:
>
>     PROXY_SERVER=\var{protocol},\var{proxy_URL}
>
> Where \var{protocol} is the name of an internet protocol, and
> \var{proxy_URL} must be a full URL to the host running the proxy
> server. HTTP is the only internet protocol supported by DODS, so
> \var{protocol} will always read <font color='green'>http</font>. There
> can be more than one proxy declaration, in which case, the OPeNDAP
> client will use the first proxy server on the list that responds.
>
> <font color='green'>PROXY_FOR</font> : The <font color='green'>PROXY_FOR</font> parameter provides a way to specify that URLs which match a regular expression should be
>
> accessed using a particular proxy server. The syntax for PROXY_FOR is:
>
>     PROXY_FOR=\var{regular expression},\var{proxy_URL}[,\var{flags}]
>
> Where \var{regular expression} is an expression which matches the URL
> or group of URLs. For example \`http://dax.dods.org/.\*\\hdf' would
> match a URL ending in \`.hdf' at dax.dods.org. The regular expression
> uses the POSIX basic syntax.\var{proxy_URL} is the same as above.
>
> The \var{flags} parameter is an optional integer that configures the
> regular expression matcher. A value of zero sets the default. The four
> flag values and their meanings are:
>
> > <font color='green'>REG_EXTENDED</font> : If set, use the POSIX extended syntax
> >
> > regular expressions. To set this, add 1 to the value of \var{flags}.
> >
> > <font color='green'>REG_NEWLINE</font>\] If set, then <font color='green'>.</font> and <font color='green'>\[\\{ </font>... :}
> >
> > don't match newline. Also, the regular expression matcher will try a
> > match beginning after every newline. Set this by adding 2 to
> > \var{flags}.
> >
> > <font color='green'>REG_ICASE</font> : If set, then we consider upper- and
> >
> > lowercase versions of letters to be equivalent when matching. Set by
> > adding 4 to \var{flags}.
> >
> > <font color='green'>REG_NOSUB</font> : If set, then when <font color='green'>PREG</font> is passed to regexec, that routine will report only success or failure, and nothing about the
> >
> > registers. Add 8 to \var{flags} to set this.
>
> You can find a brief tutorial to regular expressions in the OPeNDAP
> bookshelf\texorhtml{. See the OPeNDAP documentation page at
> \OPDhome}{at \OPDregex}.
>
> <font color='green'>NO_PROXY</font> : Use this parameter to say that access to a
>
> certain host should never go through a proxy without using the more
> complicated regular expression syntax. The syntax of
> <font color='green'>NO_PROXY</font> is:
>
>     NO_PROXY=\var{protocol},\var{hostname}
>
> Where \var{protocol} is as for
> <font color='green'>PROXY_SERVER</font>, \var{hostname} is the name of
> the host, not a url.
>
> #### Compression
>
> Many OPeNDAP servers support compression of the returned data. This
> can save network bandwidth, and transmission time. You can tell your
> client to ask the server for compressed data by including a line in
> your <font color='green'>.dodsrc</font> file like this:
>
>     DEFLATE=1
>
> Using a value of zero will make the client request only uncompressed
> data. If the directive is omitted, the default value is zero.
>
> #### Validation
>
> Caching data locally can be risky if the data in question change
> often. The <font color='green'>ALWAYS_VALIDATE</font> option controls
> whether the OPeNDAP client checks the validity of the cached data. The
> validity check in this case is simply a comparison of the date the
> data was cached with the "Last-modified" date of the remote data. If
> the configuration value is set to 1, the client will always validate
> the cached data. If set to 0, the data will not be validated (but may
> expire, according to the cache policy set by the
> <font color='green'>DEFAULT_EXPIRES</font> configuration directive).
>
> If the directive is omitted, the default value is zero. That is, the
> default behavior is not to validate the data.

# Software you will need for OPeNDAP

To do anything with DODS, you'll need to be able to unpack the archive
files you can download from the OPeNDAP site. To save space and
transmission time, the archive files are compressed with the
<font color='green'>gzip</font> program. You will have to have a copy of
that program to unpack the OPeNDAP software.

Most of the software you need for OPeNDAP is avaliable from the GNU
archives. Refer to \xlink{<http://www.gnu.org>}{<http://www.gnu.org>}
forinstructions. Look at
\xlink{<http://www.gnu.org/order/ftp.html>}{<http://www.gnu.org/order/ftp.html%7Dfor>
a list of mirrors of that archive. Use the mirror closest to you, the
transmission will be faster.

> gzip : This is the GNU compression and de-compression program.
>
> You will need to install it before you can unpack any of the other
>
> software described here. This package is *not*
>
> available in the OPeNDAP distribution, since it is used to unpack the
>
> distribution archive files.

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

## Running an OPeNDAP Server

If you use one of the platforms for which OPeNDAP supplies a binary
distribution, you only need the following software to run an OPeNDAP
server.

> Perl : Perl is used for the server dispatch script. (See
>
> ([<cite> opd-server,arch</cite>](http://www)).) This is the main CGI
> program
>
> constituting the OPeNDAP server. You must have Perl version 5 or
>
> later. (Alternatively, you can also rewrite the dispatch script to
>
> use another scripting language, such as your shell. However, we
>
> think installing Perl is generally a simpler task.) You can get
>
> Perl from the GNU archives, or from
>
> \xlink{<http://www.perl.com>}{<http://www.perl.com>}.

## Running an OPeNDAP Client

If you use one of the pre-compiled, out-of-the-box, OPeNDAP clients, you
will need no additional software to run OPeNDAP. However, you can use
the "GUI" feature of the OPeNDAP client\footnote{This is not to be
confused with the OPeNDAP Matlab or IDL GUIs, which are clients of their
own. This is simply a client feature that can display transmission and
error information to the user.} by installing the following software. We
recommend this, as it provides useful information about the progress of
data transmission or error conditions.

> Tcl/Tk : The Tcl language and Tk libraries are available from
>
> \xlink{<http://www.scriptics.com>}{<http://www.scriptics.com>}. You
> should install the entire package, including the
> <font color='green'>wish</font> interpreter program\footnote{You can
> also use a safe Tcl interpreter. Refer to the Tcl documentation for
> information.} and the <font color='green'>expect</font> package. The
> <font color='green'>wish</font> interpreter is part of the Tcl/Tk core
> distribution package. This package is also available in the OPeNDAP
> distribution, but the one available from the Tcl site may be more
> current.

## Building OPeNDAP

If you need to build the OPeNDAP software, or link it to existing
libraries, you will need the following GNU software. Refer to
\xlink{<http://www.gnu.org>}{<http://www.gnu.org>} forinstructions. Look
at
\xlink{<http://www.gnu.org/order/ftp.html>}{<http://www.gnu.org/order/ftp.html%7Dfor>
a list of mirrors of that archive. Use the mirror closest to you, the
transmission will be faster.

> GNU \Cpp Compiler : OPeNDAP needs <font color='green'>g++</font>, the GNU \Cpp compiler
>
> to compile.
>
> binutils : The GNU linker is part of this package.
> libstdc++ : The standard \Cpp library.
> GNU Make : GNU Make is not essential, but will make like easier.
> flex : The GNU lexical-analyzer generator
> bison : The GNU parser generator.