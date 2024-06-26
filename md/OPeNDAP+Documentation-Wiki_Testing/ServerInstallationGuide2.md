# Installing the OPeNDAP Server

Most of the task of installing the OPeNDAP server consists of getting
the required Web server installed and running. The variety of available
Web servers make this task beyond the scope of this guide. Proceed with
the following steps only after the Web server itself works. Look at
([Chapter 6](Wiki_Testing/ServerInstallationGuide6 "wikilink")) for
hints on how to tell whether the server is working.

If you want to install the DODS Relational Database Server (DRDS), to
serve data stored in a relational DBMS, like Oracle or SQL Server, see
([Chapter 4](Wiki_Testing/ServerInstallationGuide4 "wikilink")).

## Step by Step

Here are the steps to installing the server's base software and data
handlers (these are the components of the server) and data to be served.

1.  Install the web server.
2.  Download and install the OPeNDAP server base software.
3.  Choose one or more data handlers, download and install.
4.  Configure your data.

In a little more detail, here are those same steps:

1.  Install the web server to be used. This is not an OPeNDAP program,
    and will have its own documentation. If the server is already
    installed, figure out how to run a CGI program with that server.
    *Testing:* If your web server is running, you should be able to
    request a web page from it. From a web browser, try sending a simple
    request containing only the machine name:
    <font color='green'><http://machine></font>, where machine should be
    replaced by the name of the computer you're doing the installation
    on. When the simple page works, try executing a CGI program. (The
    simplest CGI Perl program is on [Chapter
    6](Wiki_Testing/ServerInstallationGuide6 "wikilink").) See Chapter 6
    for more ways to check if the web server is working.
2.  Download and install the OPeNDAP server base software. It is usually
    the easiest to use the pre-compiled binary distributions. Look at
    the [\| OPeNDAP](http://opendap.org) to get the software. See
    [Apendix](Wiki_Testing/ServerInstallationGuideAppendix "wikilink")
    for more information. *Testing:* The software should all be
    installed in three directories: 1) The Perl software in
    <font color='green'>/usr/local/share/dap-server</font>; 2) The CGI
    program in
    <font color='green'>/usr/local/share/dap-server-cgi</font>; and 3)
    The binary programs <font color='green'>dap_usage</font>,
    <font color='green'>dap_asciival</font> and
    <font color='green'>dap_www_int</font> in
    <font color='green'>/usr/local/bin</font>. If you build the software
    from sources you can install the server software in a root that is
    different from <font color='green'>/usr/local</font>.
3.  Once the server software has been installed, you'll need to install
    one or more data handlers. OPeNDAP provides data handlers for
    NetCDF, HDF4 (and HDF-EOS), DSP, JGOFS, and FreeForm. You need to
    download these separately and follow their installation
    instructions.
4.  Next you will need to configure the server's CGI so that it can find
    the handlers. During this step a handful of configuration parameters
    must be set. Locate the file named
    <font color='green'>dap-server.rc</font> in
    <font color='green'>/usr/local/share/dap-server-cgi</font>. See
    ([Section 2.2](Wiki_Testing/ServerInstallationGuide2 "wikilink"))
    for instructions about how to configure this file. *Testing:* When
    the server is working, you should get a response to a *version*
    request, where you query the software for its version (release)
    number. To do this, enter a URL like the following into a web
    browser:
         http://machine/cgi-bin/nph-dods/version

    Remember to replace machine with the machine you're using. Also, the
    CGI directory you're using may not be called
    <font color='green'>cgi-bin</font>. If you're not using a CGI
    directory, see the next step.

    1.  If your server uses name conventions to identify CGI programs,
        change the names of the dispatch script to conform with the
        local convention: e.g. <font color='green'>nph_dods</font> to
        <font color='green'>nph_dods.cgi</font>. The other service
        program names do not need to be changed. *Testing:* Try to get a
        version number from the server. If your CGI programs are
        identified with a suffix like <font color='green'>.cgi</font>,
        try a URL like this:
             http://machine/nph-dods.cgi/version
    2.  If you are using DODS release 3.5 or later, you also need to
        make sure that the dispatch configuration file
        <font color='green'>dap-server.rc</font> is also copied into the
        directory with the CGI and correctly protected. See ([Section
        2.2](Wiki_Testing/ServerInstallationGuide2 "wikilink")) for
        instructions about how to configure this file. Note that earlier
        servers (from version 3.2 through 3.4 used a configuration file
        named <font color='green'>dods.in</font> or
        <font color='green'>dods.rc</font>). *Testing:* This cannot be
        tested without having some data installed.
5.  See ([Chapter 3](Wiki_Testing/ServerInstallationGuide3 "wikilink"))
    for instructions on installing and testing the data to be served.
    See ([Chapter 6](Wiki_Testing/ServerInstallationGuide6 "wikilink"))
    for more tests to make sure you've done each step correctly.

> NOTE: In addition to some specialized Perl modules, the OPeNDAP server
>
> ` uses the HTML::Parser Perl module. You should have installed this`
> ` prior to installing the dap-server package.`

The CGI configuration of a web server is dependent on the particular web
server you use. Consult the documentation for that server for more
information. Our observations are that for most servers, having a CGI
directory is the default situation, and there are a couple of potential
security holes avoided with that configuration.

To find which directory is the cgi-bin directory, you can look in the
server's configuration file for a line like:

    ScriptAlias /cgi-bin/ /var/www/cgi-bin/

For both the NCSA and Apache servers, the option
<font color='green'>ScriptAlias</font> defines where CGI programs may
reside. In this case they are in the directory
<font color='green'>/var/www/cgi-bin</font>. URLs with
<font color='green'>cgi-bin</font> in their path will automatically
refer to programs in this directory.

At this point, you will be wondering if things are working yet or not.
Again, check out ([Chapter
6](Wiki_Testing/ServerInstallationGuide6 "wikilink")) for a detailed set
of tests to get you going.

## Configure the Server

Starting with version 3.5, the OPeNDAP server makes much more extensive
use of its configuration file. The
<font color='green'>dap-server.rc</font> configuration file is used to
tailor the server to your site. The file contains a handful of
parameters plus the mappings between different data sources (typically
files, although that doesn't have to be the case) and hander-programs.
The format of the configuration file is:

         <parameter> <value> ... <value>

The configuration file is line-oriented, with each parameter appearing
on its own line. Blank lines are ignored and the
<font color='green'>\\</font> character is used to begin comment lines.
Comments have to appear on lines by themselves.

The parameters recognized are:

- data_root

<!-- -->

      data_root <path>

Define this if you do not want the server to assume data are located
under the web server's DocumentRoot. The value
<font color='green'><path></font> should be the fully qualified path to
the directory which you want to use as the root of your data tree. For
example, we have a collection of netcdf, hdf, {\it et c.}, files that we
store in directories named
<font color='green'>/usr/local/test_data/data/nc</font>,
<font color='green'>/usr/local/test_data/data/hdf</font>, {\it et c.},
and we set <font color='green'>data_root</font> to
<font color='green'>/usr/local/test_data</font>. The value of
<font color='green'><path></font> should not end in a slash.

> NOTE: The OPeNDAP server's directory browsing functions do not work
> when using the <font color='green'>data_root</font> option. They do
> still work when locating data under DocumentRoot. Also, it's not an
> absolute requirement that this path be a real directory or that your
> data are in \`files.' Data can reside in a relational database, for
> example, and in that case the base software will use
> <font color='green'><path></font> as a prefix to the path part of the
> URL it receives from the client.

- timeout

<!-- -->

       timeout <seconds>

This sets the OPeNDAP server timeout value, in seconds. This is
different from the httpd timeout. OPeNDAP servers run independently of
httpd once the initial work of httpd is complete. Setting
<font color='green'>timeout</font> ensures that your OPeNDAP server does
not continue indefinitely if something goes wrong ({\it i.e.}, a user
makes a huge request to a database). Default is 0 which means no time
out.

- cache_dir

<!-- -->

     cache_dir <directory>

When data files are stored in a compressed format such as gzip or UNIX
compress, the OPeNDAP server first decompresses them and then serves the
decompressed file. The files are cached as they are decompressed. This
parameter tells the server where to put that cache. Default:
<font color='green'>/usr/tmp</font>

- cache_size

<!-- -->

       cache_size <size in MB>

How much space can the cached files occupy? This value is given in
MegaBytes. When the total size of all the decompressed files exceeds
this value, the oldest remaining file will be removed until the size
drops below the parameter value. If you are serving large files, make
*sure* this value is at least as large as the largest file.

- maintainer

<!-- -->

       maintainer <email address>

The email address of the person responsible for this server. This email
address will be included in many error messages returned by the server.
Default: <font color='green'>support@unidata.ucar.edu</font>

- curl

<!-- -->

       curl <path>

This parameter is used to set the path to the curl executable. The curl
command line tool is used to dereference URLs when the server needs to
do so. In some cases the curl executable might not be found by the CGI.
This can be a source of considerable confusion because a CGI program run
from a web daemon uses a very restricted PATH environment variable, much
more restricted than a typical user's PATH. Thus, even if you, as the
server installer have curl on your PATH, nph-dods may not be able to
find the program unless you tell it exactly where to look.

- exclude

<!-- -->

       exclude <handler> ... <handler>

This is a list of handlers whose regular expressions should not be used
when building the HTML form interface for this server. In general, this
list should be empty. However, if you have a handler that is bound to a
regular expression that is very general (such as
<font color='green'>.\*</font> which will match all files), then you
should list that handler here, enclosing the name in double quotes. See
the next item about the 'handler' parameter. Default: No handlers are
excluded.

- handler

<!-- -->

       handler <regular expression> <handler name>

The handler parameter is used to match data sources with particular
handler programs used by the server. In a typical OPeNDAP server setup,
the data sources are files and the regular expressions choose handlers
based on the data file's extension. However, this need not be that case.
The OPeNDAP server actually matches the entire pathname of the data
source when searching for the correct handler to use. Here are the
values assigned to the <font color='green'>handler</font> parameter in
the default <font color='green'>dap-server.rc</font> file:


    # Look for common file extensions.
    handler .*\.(HDF|hdf|EOS|eos)(.Z|.gz)*$ /usr/local/bin/dap_hdf_handler
    handler .*\.(NC|nc|cdf|CDF)$ /usr/local/bin/dap_nc_handler
    handler .*\.(dat|bin)$ /usr/local/bin/dap_ff_handler
    handler .*\.(pvu)(.Z|.gz)*$ /usr/local/bin/dap_dsp_handler

    # For JGOFS datasets, match either the dataset name or the absence of an
    # extension. The later case is sort of risky, but if you have lots of JGOFS
    # datasets it might be appealing. handler .*/test$ /usr/local/bin/dap_jg_handler
    # handler .*/[^/]+$ /usr/local/bin/dap_jg_handler

Consider a URL like this:

     http://test.opendap.org/opendap/nph-dods/data/nc/fnoc1.nc

When the <font color='green'>nph-dods</font> script is executed, the
"file name" part of the URL is
<font color='green'>/data/nc/fnoc1.nc</font>. Testing this against the
default initialization file matches the second line, which indicates
that the <font color='green'>dap_nc_handler</font> (NetCDF) data handler
should be used to process this request. The request is then dispatched
to the handler for processing.

> NOTE: The default configuration file used to be set up so that files
> without extensions are handled by the JGOFS data handler. This caused
> some problems for sites where data files did not always use common
> extensions for data files. If you need to serve many JGOFS data files,
> then uncomment this line of write special regular expressions for the
> JGOFS data sources.

"Regular expressions", advanced pattern-matching languages, are a
powerful feature of Perl and many other computer languages. Powerful
enough, in fact, to warrant at least one book about them (Mastering
Regular Expressions by Jeffrey Friedl, O'Reilly, 1997). (For a complete
reference online, which is not a particularly good place to learn about
them for the first time, see
\[<http://www.perldoc.com/perl5.6/pod/perlre.html><cite><http://www.perldoc.com/perl5.6/pod/perlre.html></cite>\].)
Briefly, however, the above patterns test whether a filename is of the
form
\var{file}<font color='green'>.</font>\var{ext}<font color='green'>.</font>\var{comp},
where \var{comp} (if present) is <font color='green'>Z</font> or
<font color='green'>gz</font>, and \var{ext} is one of several possible
filename extensions that might indicate a specific storage API. If these
default rules will not work for your installation, you can rewrite them.
For example, if all your files are HDF files, you could replace the
default configuration file with one that looks like this:

     handler .* /usr/local/bin/dap_hdf_handler

The <font color='green'>.\*</font> pattern matches all possible patterns
(the <font color='green'>.</font> matches a single character and
<font color='green'>\*</font> matches zero or more occurrences of the
previous character or \new{metacharacter}), and indicates that whatever
the name of the file sought, the HDF service programs are the ones to
use. If you have a situation where all the files in a particular
directory (whatever its extension) are to be handled by the DSP service
programs, and all other files served are JGOFS files, try this:


    handler \/dsp_data\/.*$ /usr/local/bin/dap_dsp_handler
    handler .*/[^/]+$ /usr/local/bin/dap_jg_handler

The rules are applied in order, and the first rule with a successful
match returns the handler that will be applied. The above set of rules
implies that everything in the <font color='green'>dsp_data</font>
directory will be processed with the DSP handler, and everything else
will be sent to the JGOFS handler.

## Compression

Data compression can be a confusing subject for people installing the
OPeNDAP server. Part of the confusion arises because there are two
entirely separate issues here. The first is that data may be *stored* in
a compressed format, and the second is that data may be *transmitted* in
a compressed format. These issues are completely independent of one
another; data stored in compressed form can be transmitted in
uncompressed form, and *vice versa* .

### Storing Your Data In Compressed Form

The OPeNDAP server can process compressed data, even when the native API
which should be used to read the data cannot process it. The nph-dods
CGI program will decompress the file(s) and cache them in a temporary
directory. If a request URL arrives for a data file ending with
<font color='green'>.Z</font> or <font color='green'>.gz</font>, the
dispatch script uses <font color='green'>uncompress</font> or
<font color='green'>gzip</font> to produce a decompressed file in a
temporary location.

If you have compressed data and need to take advantage of this feature,
follow these steps:

1.  Check to make sure that <font color='green'>uncompress</font> or
    <font color='green'>gzip</font> is installed on your computer.
2.  Create a temporary directory somewhere. Make sure that this
    directory is owned (or at least writable) by the
    <font color='green'>httpd</font> process
    [6](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink").
3.  Edit the <font color='green'>dap-server.rc</font> configuration file
    to change the value of <font color='green'>cache_dir</font> to point
    to the new temporary directory. By default, this is
    <font color='green'>/usr/tmp</font>.

That should be enough to make your server serve compressed files.

The dispatch script will keep your temporary directory from overflowing
by occasionally deleting old files. The default maximum cache size is 50
megabytes. If you have more files in the directory than that, the script
starts deleting the oldest ones first until the size is lower than the
given limit. Edit <font color='green'>dap-server.rc</font> and change
the value of <font color='green'>cache_size</font> to the maximum size
that you want the contents of this directory to be.

If you need to serve files compressed with another compression technique
(besides <font color='green'>gzip</font> or
<font color='green'>compress</font>), you can edit the
<font color='green'>DODS_Cache.pm</font> perl module.

First change the regular expression that marks compressed files to
something like this:

    my $compressed_regex = "(\.gz|\.Z|\.bz2)";

Then edit the <font color='green'>decompress_and_cache</font> function
to contain a test for bzip2 files. This might read like this:

    sub decompress_and_cache {
        my $pathname = shift;
        my $cache_dir = shift;

        my <math>cache_entity = cache_name(</math>pathname, $cache_dir);


    if ((! -e <math>cache_entity) && (-e </math>pathname)) {
          if ($pathname =~ m/.*\.bz2/) {
            my <math>uncomp = "bzip2 -c -d " . </math>pathname . " > " .  $cache_entity;

    } else {

    my <math>uncomp = "gzip -c -d " . </math>pathname . " > " . $cache_entity;
          }
            system($uncomp);
        }

        return $cache_entity;
    }

### Enabling Transmission of Compressed Data

To save communication bandwidth, the OPeNDAP server can send compressed
data to certain clients who are equipped to handle it. When making a
request for data, a client can signal the server that data may be sent
in a compressed form. If a server receives such a signal, it looks for a
helper program called <font color='green'>deflate</font>, and it runs
the outgoing data through that program on its way back to the client.

All the OPeNDAP servers can handle data compression for transmission if
the <font color='green'>deflate</font> program is installed on the
<font color='green'>\$PATH</font> used by the web server. In version 3.5
of libdap, <font color='green'>deflate</font> is installed in
<font color='green'>\$prefix/bin</font> (libdap is required to run the
server).

## Security

Many data providers ask whether the OPeNDAP server can be configured to
limit access to a particular file or sets of files. The answer is that
it is as flexible as the http server you use to implement it. Clients
built using the OPeNDAP DAP2 library are capable of prompting the user
for usernames and passwords if the http daemon instructs them to. Most
web servers will also allow usernames and passwords to be embedded in
the requesting URL like this:

    http://user:password@test.opendap.org/nph-dods/...

Depending on the specific web server, you can restrict access to the CGI
programs or to the individual data files or directories.

By using the web server's authorization software we are ensuring that
the security checks used by the server have been tested by many many
sites. In addition, WWW servers already support a very full set of
security features and many system administrators are comfortable with,
and confidant in, them. The tradeoff with using the web server's
security system for our servers is that two security settings must be
made for each group of data to be configured and more than one copy of
the nph-dods CGI program and dap-server.rc configuration file may be
needed even if you're serving only one type of data.

Because the security features rely almost entirely on the host machine's
WWW server, the steps required to install a secure the server will vary
depending on the WWW server used. Thus, before installing a secure
server, check over your WWW server's documentation to make sure it
provides the following security features: Access limits to files in the
document root on a per user and/or per machine basis, and; The ability
to place CGI scripts in protected directories.

There are two levels of security which the OPeNDAP server supports:
Domain restrictions and user restrictions. In conjunction with a WWW
server, access to the OPeNDAP server can be limited to a specific group
of users (authenticated by password), specific machine(s) or a group of
machines within a given domain or domains.

> NOTE: Because security features are used to protect sensitive or
>
> ` otherwise important information, once set-up they should be tested`
> ` until you are comfortable that they work. You should try accessing`
> ` from at least one machine that is not allowed to access your data.`
> ` If you would like, we will help you evaluate your set-up.`

It is important to distinguish securing a server from securing data. If
data are served, then those data may also be accessible through a web
browser. If so the data themselves need to be stored in directories that
have limited access. If all data access will take place through the
server this limitation can exclude all access except the local machine.
This is the case because some the server's directory function requires
being able to read the data through the local host's web server.

It bears repeating: If you're serving sensitive information with the
OPeNDAP server and those data are located under the WWW daemon's
DocumentRoot, that information is accessible two ways: via the OPeNDAP
server and through the WWW server. You need to make sure *both* are
protected.

> NOTE: With version 3.5 of the OPeNDAP server, you can use the
>
> ` `<font color='green'>`data_root`</font>` parameter in the `<font color='green'>`dap-server.rc`</font>` configuration file to serve data that are not accessible using your WWW server. This simplifies securing the data but has the drawback that the directory response is not supported by the OPeNDAP server.`

### About serving both limited- and open-access data from the same server

In the past it was possible to install two or more OPeNDAP servers on a
computer and assign different protections to each one. However, in
practice this has proven to be very hard to configure correctly. In many
cases where this feature was used, a secure server was setup up for one
group of data while an open server was set up for another. It was often
the case that all the data were accessible using the open server! Thus,
if you need to secure some data, it is best to host all the sensitive
information on one machine and put other data on a second machine with
an open-access server. If you must run two or more servers from the same
physical host, we suggest that you configure your web server to see two
(or more) virtual hosts. This will provide the needed separation between
the groups of data.

### Using a secure opendap server

Using a secure sever is transparent if the server is configured to allow
access based on hosts or domains. Give the URL to a client; the server
will respond by answering the request if allowed or with an error
message if access is not allowed.

Accessing a server which requires password authentication is a little
different and varies depending on the type of client being used. All
OPeNDAP clients support passing the authentication information along
with the URL. To do this add
<font color='green'><username>:<password>@</font> before the machine
name in a URL. For example, suppose I have a secure server set up on
\`test.opendap.org' and the user \`guest' is allowed access with the
password \`demo'. A URL for that server would start out:

        http://guest:demo@test.opendap.org/...

For example,

        http://guest:demo@test.opendap.org/secure/nph-dods/sdata/nc/fnoc1.nc.info

will return the info on the data set fnoc1.nc from a secure server. You
cannot access the data without including the username and password
<font color='green'>guest</font> and <font color='green'>demo</font>.

Some clients will pop up a dialog box and prompt for the username and
password. Netscape, and some other web browsers, for example, will do
this. Similarly, some DODS clients may also popup a dialog.

### Configuring a server

In the following I'll use the Apache 1.3.12 server as an example[^1] and
describe how to install a server which limits access to a set of users.
While this example uses the Apache server, it should be simple to
perform the equivalent steps for any other WWW server that supports the
set of required security features.

#### Create a directory for the server

To limit access to a dataset to particular machine, begin by creating a
special directory for the server. This maybe either an additional CGI
bin directory or a directory within the web server's document root. In
this example, I chose the latter.

        cd /var/www/html/
        mkdir secure

#### Establish access limitations for that directory

Establish the access limitations for this directory. For the Apache
server, this is done either by adding lines to the server's
<font color='green'>httpd.conf</font> file or by using a per-directory
access control file. Note: The use of per-directory access control files
is a configurable feature of the Apache server; look in the server's
\lit{httpd.conf

` file} for the value of the AccessFileName resource.`

I modified Apache's <font color='green'>httpd.conf</font> file so that
it contains the following:

        # Only valid users can use the server in 'secure'. 7/6/2000 jhrg
        <Directory /var/www/html/secure>
            Options ExecCGI Indexes FollowSymLinks

            Order deny,allow
            Deny from all
            # ALLOW SERVER (IP OF SERVER) MACHINE TO REQUEST DATA ITSELF
            Allow from __YOUR_SERVER_HERE__
            Require valid-user
            # ALL VISITORS NEED USERNAME AND PASS BUT NOT SERVER
            Satisfy any

            AuthType Basic
            AuthUserFile /etc/httpd/conf/htpasswd.users
            AuthGroupFile /etc/httpd/conf/htpasswd.groups
            AuthName "Secure server"
        </Directory>

        # Protect the directory used to hold the secure data.
        <Directory /var/www/html/sdata>
            Options Indexes

            Order deny,allow
            Deny from all
            # ALLOW SERVER (IP OF SERVER) MACHINE TO REQUEST DATA ITSELF
            Allow from __YOUR_SERVER_HERE__
            Require valid-user
            # ALL VISITORS NEED USERNAME AND PASS BUT NOT SERVER
            Satisfy any

            AuthType Basic
            AuthUserFile /etc/httpd/conf/htpasswd.users
            AuthGroupFile /etc/httpd/conf/htpasswd.groups
            AuthName "Secure data"
        </Directory>

and

        ScriptAlias /secure/ "/var/www/html/secure/"

The first group of lines establishes the options allowed for the
<font color='green'>secure</font> directory, including that it can
contain CGI programs. The lines following that establish that only users
in the Apache password file can access the contents of the directory,
with the exception that this server is allowed to access the directory
without authentication. This last bit is important because OPeNDAP
servers sometimes make requests to themselves (e.g., when generating the
directory response) but don't pass on the authentication
information.[8](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink")

The ScriptAlias line tells Apache that executable files in the directory
are CGIs. You can also do this by renaming the
<font color='green'>nph-dods</font> script to
<font color='green'>nph-dods.cgi</font> and making sure
<font color='green'>httpd.conf</font> contains the line:

        AddHandler cgi-script .cgi

The AuthType directive selects the type of authentication used. Apache
2.0 supports 'Basic' and 'Digest' while other servers may also support
GSS-Negotiate and NTLM. Version 3.4 onward of the DAP software supports
all these authentication schemes, although only Basic and Digest have
been thoroughly tested. Configuration of Apache 2.0 for Digest
authentication is slightly different then for Basic authentication, but
is explained well in Apache's on line documentation.

#### Copy the server into the new directory

Copy the CGI program (<font color='green'>nph-dods</font>) and the
server configuration file to the newly created directory. Note that if
you're using the extension \`.cgi' to tell Apache that
<font color='green'>nph-dods</font> is a CGI you must rename it to
<font color='green'>nph-dods.cgi</font>. If you forget to do that then
you will get a Not Found (404) error from the server and debugging
information generated by the server won't appear in Apache's
<font color='green'>error_log</font> even if it has been turned on.

You are done.

### Tips

Here are some tips on setting up secure servers:

- Using the per-directory limit files makes changing limits easier since
  the server reads those every time it accesses the directory, while
  changes made to the <font color='green'>httpd.conf</font> file are not
  read until the server is restarted or sent the HUP signal. However,
  using <font color='green'>httpd.conf</font> for your security
  configuration seems more straightforward since all the information is
  in one place.
- If the protections are set up so that it is impossible for the server
  host to access the data and/or the OPeNDAP server itself, then an
  infinite loop can result. This can be frustrating to debug, but if you
  see that accesses generate an endless series of entries in the
  access_log file, it is likely that is the problem. Make sure that you
  have <font color='green'>allow from <server host name></font> set for
  both the directory that holds the OPeNDAP server and that holds the
  data. Also make sure that the server's name is set to the full name of
  the host.
- Configuring a secure server can be frustrating if you're testing the
  server using a web browser that remembers passwords. You can turn this
  feature off in some browsers. Also, the getdap tool supplied with
  OPeNDAP's libdap (which is required to build the server) can be useful
  to test the server since it will not remember passwords between runs.

[^1]: \ldots also tested on Apache 2.0.40, 07/25/03 jhrg