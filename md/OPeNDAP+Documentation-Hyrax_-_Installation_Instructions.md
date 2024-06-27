<font size="5">

This page is deprecated
These instructions apply to Hyrax version 1.10 and older.

[Current Installation Instructions Can Be Found
Here.](Hyrax#Install "wikilink")

</font> Hyrax is fairly easy to configure, but because there are several
different components involved, it can seem complicated. Essentially you
need to install two pieces of software: the BES and the OLFS. The BES
reads data files and builds various kinds of responses and the OLFS is
an interface to the BES that provides a web presence for the data and
services. Because Hyrax uses a web server to work with web browsers and
other kinds of programs, you will need to install the Apache Tomcat
software too.

What you will need to install a binary distribution:

- 5 minutes
- Java 1.7 Runtime
- Tomcat 7 or newer
- The two pieces of software the make up Hyrax

## System Requirements

Hyrax is supported on Linux and OS/X, although we only build binary
versions for Linux. We do not support Hyrax on Windows or Vista,
although see the [Using Virtual Machines to Serve
Data](Using_Virtual_Machines_to_Serve_Data "wikilink") project for a
solution applicable to Win32 platforms. While that information is
somewhat dated since it describes an older version of Hyrax, the
approach is generally applicable to the current version of Hyrax.

For our binary distribution of Hyrax for CentOS/RedHat/Fedora, use
either *yum* or *rpm* to install the libdap and BES RPM packages. We
also have DEB packages for Ubuntu/Debian. Either *yum* or *apt-get* will
find and install any dependencies the libdap or BES package needs.

## Secure Installation Guidelines

[Making Hyrax Secure](Hyrax_-_Secure_Installation_Guidelines "wikilink")

## Install the BES

[Install and configure the BES.](Hyrax_-_BES_Installation "wikilink")

## Install the OLFS

[Next You need to install and configure the OLFS inside of a Tomcat
server](Hyrax_-_OLFS_Installation "wikilink")

## Starting and Stopping Hyrax

To correctly start and stop Hyrax, follow these simple recipes.

##### Starting

1.  Start the BES first using the *besctl* program with the *start*
    argument. See [Starting and stopping the
    BES](Hyrax_-_Starting_and_stopping_the_BES "wikilink") for more
    information.
2.  Start the OLFS using the Tomcat startup script. See [Starting
    Tomcat](Hyrax_-_OLFS_Installation#Start_Tomcat "wikilink") for more
    information.

##### Stopping

1.  Stop the OLFS first using the tomcat shutdown script.
2.  Stop the BES using the *besctl* program with the *stop* argument.

## Test the server

To test/use the server, open a web browser and open the URL:
<http://localhost:8080/> and you should see the Tomcat default page.

Open the URL: <http://localhost:8080/opendap/> to see an HTML directory
of the top of your data archive.

You can get the HTML view THREDDS catalog by appending "catalog.html" to
the end of any URL that returns an HTML directory, like so:
<http://localhost:8080/opendap/catalog.html> But you may need to do some
additional configuration that to work.

## Configure Your Hyrax Installation

At this point, Hyrax should be working. When you install Hyrax for the
first time it is pre-configured to serve test data sets that come with
each of the installed data handlers. This will allow you to test the
server and make sure it is functioning correctly. After that you will
want to configure so that it works with your data.

## Miscellaneous

Once opendap.war is expanded, manually copy the file favicon.ico file:

`$CATALINA_HOME/webapps/opendap/docs/images/favicon.ico`

to

`$CATALINA_HOME/webapps/ROOT/`

The favicon.ico file is mostly a convenience to keep browsers from
constantly asking for it (substitute your own icon if you like!).