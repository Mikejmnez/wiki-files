# Testing the Installation

It is possible to test the server to see whether an installation has
been properly done. The easiest way to test the installation is with a
simple Web client like Netscape. (A command-line web client called
<font color='green'>getdap</font> is provided with
<font color='green'>libdap</font> which can retrieve text from Web
servers, and print it on standard output. Look for it in the
<font color='green'>/usr/local/bin</font> directory. If you try it out
on a couple of URLs you are familiar with, you'll quickly see how it
works.)

You can test the web server installation first, and when you are sure
the server is working well, test the OPeNDAP server.

## Testing the Web Server

To begin, confirm that the web server works properly by simply sending a
URL for a simple web page, or without a file at all:

    http://yourmachine

If you don't get anything with this, examine the document root directory
(<font color='green'>htdocs</font>), and make a request for a file in
that directory:

    http://yourmachine/somefile.html

Debugging these early steps is essential to getting an OPeNDAP server to
work, but troubleshooting any problems you have with these steps is an
issue specific to the web server you are using.

Once you have a web server that can return a web page, you should
exercise the CGI configuration. Here is the simplest possible CGI
program:

    #!/usr/bin/perl
    print "Content-type: text/html\n\n";
    print "Hello World!\n\n";

Put this text in a file, and try to execute it from the command line.
You may have to edit the first line in case your copy of Perl isn't
stored in the <font color='green'>/usr/bin</font> directory. Don't
forget to make the file executable. (Use the
<font color='green'>chmod</font> command.)

When you can execute this from the command line, put it into the CGI
directory (and make sure that the permissions will allow the
<font color='green'>httpd</font> to execute it), and try to execute it
with a URL like this:

    http://yourmachine/cgi-bin/test-cgi

If you are configuring your site to use suffixes instead of a directory
to identify CGI files, name it accordingly:

    http://yourmachine/test.cgi

Entering one of these URLs in a web server should cause the "Hello,
World!" text to appear in the web browser window. If not, check the
permissions of the CGI program, then consult the documentation for the
web server.

If everything works so far, it's time to move on to testing the OPeNDAP
server installation.

## Testing the OPeNDAP Server

You can run the handler programs locally, that is, without going over
the Internet. Sometimes this is the simplest way to make sure that
everything is running.

    ./dap_nc_handler -o dds  datafile.nc

The program should print the dataset's DDS on standard output. Use the
<font color='green'>-h</font> option to see the list of options the
handlers accept.

The simplest remote test is simply to ask for the version of the server,
sending a URL like this:

    http://yourmachine/cgi-bin/nph-dods/version

The server will respond to this URL with some text containing release
numbers. If you enter this URL into a standard web browser you're doing
fine if you see a message like this:

    Core version: DODS/3.2.5
    Server version: nc/3.2.2

The dispatch script can respond to a number of requests without the
assistance of its helper programs, or the existence of any data. Besides
the version request, you can also ask for the help and info messages:

    > getdap http://test.opendap.org/opendap/nph-dods/data/nc/test.nc.ver
    > getdap http://test.opendap.org/opendap/nph-dods/data/nc/test.nc.info
    > getdap http://test.opendap.org/opendap//nph-dods/data/nc/test.nc.help

Now we need to test the requests that use the handler programs, such as
<font color='green'>dap_nc_handler</font>. These programs compose their
output by looking at data files, so testing these requires data to be in
place.

To return the data attribute structure of a dataset, use a URL such as
the following:

    > getdap http://test.opendap.org/opendap/nph-dods/data/nc/test.nc.das

The <font color='green'>getdap</font> program knows about the DAP
protocol, so you can also omit the <font color='green'>.das</font>
suffix, and use the <font color='green'>-a</font> option to the
<font color='green'>geturl</font> command. This tells
<font color='green'>getdap</font> to append
<font color='green'>.das</font> for you:

    > getdap -a http://test.opendap.org/opendap/nph-dods/data/nc/test.nc

Refer to \[<http://docs.opendap.org/index.php/UserGuide><cite> The
OPeNDAP User Guide</cite>\] for a description of a data attribute
structure. You can compare the description against what is returned by
the above URL to test the operation of the server.

Check the list of services and helper programs in ([Section
1.3](Wiki_Testing/ServerInstallationGuide1 "wikilink")). From a web
browser, you can access all the DAP services, except the data service,
which returns binary, not ASCII, data. That one can only be easily
tested from a DAP-enabled client. However, if all the service programs
work, and the data service is configured the same way, the odds are on
your side.

Using the <font color='green'>.html</font> suffix produces the WWW
interface, providing a forms-based interface with which a user can query
the dataset using a simple web browser. There's more about the interface
in \[<http://docs.opendap.org/index.php/UserGuide><cite> The OPeNDAP
User Guide</cite>\] .

## Error Logs

When troubleshooting an OPeNDAP data server, the logs of the web server
are quite useful. The Apache server keeps two logs, an Access log and an
Error log. (You can find these in the <font color='green'>logs</font>
subdirectory where you installed Apache.) The OPeNDAP server software
writes any messages it issues to the error log.

You can make the error messages slightly more verbose by editing the
<font color='green'>DODS_Dispatch.pm</font> file (in
<font color='green'>/usr/share/dap-server</font>). Find the
<font color='green'>\$debug</font> variable, and set it to 1 or 2
instead of zero. Note that these messages are, as of version 3.5, now
written to the <font color='green'>dbg_log</font> file which is usually
in <font color='green'>/usr/share/dap-server</font>. Check the
<font color='green'>DODS_Dispatch.pm</font> file to determine the actual
path.

## User Support

As a last resort, you can use the OPeNDAP technical support service.
OPeNDAP provides technical support by email:
[support@opendap.org](support@opendap.org "wikilink").