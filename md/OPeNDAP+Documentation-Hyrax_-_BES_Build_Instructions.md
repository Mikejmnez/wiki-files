How to build the BES from either a source code distribution, or directly
from our [Subversion](http://subversion.tigris.org/) repository. These
instructions are for the latest release of the BES, which is version
3.9.0 used for Hyrax 1.6.2.

------------------------------------------------------------------------

# Dependancies

## [GCC, the GNU compiler](http://directory.fsf.org/gcc.html)

***Minimum***: gcc 4.x

***Recommended***: gcc 4.2.1

This software was developed and tested using gcc 4.0.1 on Intel OS-X.
The default compiler was used, version 4.0.1. You can also obtain the
compiler using such tools as
[DarwinPorts](http://darwinports.opendarwin.org/) on OS-X, package
managers on Unix-like machines.

## [autoconf](http://directory.fsf.org/autoconf.html)

***Minimum***: autoconf 2.61

***Recommended***: autoconf 2.65

This software was developed and tested using autoconf 2.63 on Intel
OS-X.

## [automake](http://directory.fsf.org/automake.html)

***Minimum***: automake 1.10

***Recommended***: automake 1.11.1

This software was developed and tested using automake 1.11 on Intel
OS-X. The reason for requiring 1.10 is that there is an issue with
building some of the configuration and tests for the BES.

## [libtool](http://directory.fsf.org/libtool.html)

***Minimum***: libtool 1.5.18

***Recommended***: libtool 2.2.6

This software was developed and tested using libtool 2.2.6 on Intel
OS-X.

## [readline](http://directory.fsf.org/readline.html)

This library allows users to edit command lines as they are being typed
in. This is used by the BES command line application bescmdln in the
interactive mode (the default mode).

***Minimum***: readline 4.3

***Recommended***: readline 4.3

This software was developed and tested using readline 4.3 on Intel OS-X.

## [libxml2](http://xmlsoft.org/)

Libxml2 is the XML C parser and toolkit developed for the Gnome project
(but usable outside of the Gnome platform), it is free software
available under the MIT License. This is used by the BES to parse
incoming request documents and to create xml response documents.

***Minimum***: readline 2.6.16

***Recommended***: readline 2.7.3

This software was developed and tested using libxml2 2.7.3 on Intel
OS-X.

## [libdap](http://opendap.org/download/hyrax.html)

The BES does not specifically require the libdap libraries. But, to get
Dap 2 responses you must have libdap. With libdap you get the DAS, DDS,
DataDDS, DDX responses as well as catalog support for THREDDS.

***Minimum***: libdap 3.11.0

***Recommended***: libdap 3.11.0

This software was developed and tested using libdap 3.9.2 on Intel OS-X.

# Optional Dependencies

## [openssl](http://www.openssl.org/)

Openssl is used for a secure server. If you specify BES.ServerSecure=yes
(default is no) in the [BES
Configuration](Hyrax_-_BES_Configuration "wikilink") file then a client
will be required to authenticate with the BES.

***Minimum***: openssl 0.9.7

***Recommended***: openssl 0.9.7

This software was developed and tested using openssl 0.9.7i on Intel
OS-X.

------------------------------------------------------------------------

## [dejagnu](http://www.gnu.org/software/dejagnu/)

In order to run many of the tests in the BES you will need dejagnu. We
are in the process of converting our tests to use autotest, which will
remove this dependency. If you wish to run 'make check', then you will
need dejagnu.

***Minimum***: dejagnu 1.4.4

***Recommended***: dejagnu 1.4.4

This BES software was tested using dejagnu 1.4.4 on Intel OS-X.

------------------------------------------------------------------------

## [cppunit](http://sourceforge.net/projects/cppunit/)

In order to run many of the tests in the BES you will need cppunit. If
you wish to run all of the tests using 'make check', then you will need
cppunit, although it is not required.

***Minimum***: cppunit 1.12.1

***Recommended***: cppunit 1.12.1

This BES software was tested using cppunit 1.12.1 on Intel OS-X.

------------------------------------------------------------------------

# Getting The Source Code

## As A Source Code Distribution

The most recent source code distribution will available at both the
[Hyrax download page](http://opendap.org/download/hyrax.html) and the
[BES download page](http://opendap.org/download/BES.html). If you are
attempting to build the BES in conjunction with the OLFS - for example
you are building Hyrax - then get both the source distributions from the
same Hyrax release on the [Hyrax download
page](http://opendap.org/download/hyrax.html), other wise you may
experience compatibility issues.

Go get the source code bundle. It will be a file named something like
bes-*x.x.x*.tar.gz

Unpack the bundle using the command:

`tar zxvf bes-`*`x.x.x`*`.tar.gz`

This will unpack the source code distribution into a directory called
*bes.x.x.x*

## From Subversion

- Check out the software for the BES from our
  [Subversion](http://subversion.tigris.org/) repository located at
  <http://scm.opendap.org/svn/trunk/bes>:

`svn co `[`http://scm.opendap.org/svn/trunk/bes`](http://scm.opendap.org/svn/trunk/bes)

------------------------------------------------------------------------

# Building the code

Building the BES software should be very simple.

1.  Enter the top level directory of the bes source code tree (*bes* if
    you got the source code from Subversion, *bes-.x.x.x* if you got it
    from a distribution bundle.)

2.  If you checked out the latest from the Subversion repository, run
    the command:

    `autoreconf --force`

    This will create the configuration scripts

3.  Run the command:

    `./configure`

    This will run the configuration script to make sure that you have
    all the required dependencies met and will also generate Makefiles
    and other configuration files needed to build the BES and to run the
    BES.
    You may want to change some of the configuration options. Please
    refer to the README and INSTALL files in the top level directory for
    configuration options. You can also run

    `./configure --help`

    to get more options. The most commonly used option is the
    --prefix=<directory> option, which tells the build to install the
    bes into the specified directory. The default directory is
    /usr/local.

4.  Run the command:

    `make`

    This will build the BES code

5.  Run the command:

    `make check`

    This will test to make sure that you have a successful build of the
    BES.


------------------------------------------------------------------------

# Installing the BES from source code

Run the command:

`make install`

This will install the BES into /usr/local if no --prefix was specified
on the configure line, or in the specified directory if you did use the
--prefix option.

Make sure that the BES bin directory is in your PATH. For example, if
you installed the BES in the default location, the directory
/usr/local/bin should be on your path. If you installed it in a
non-standard location, then you will need to update your PATH
environment variable. For example, if you installed the bes into
/usr/local/bes, then make sure that /usr/local/bes/bin is on your PATH.

------------------------------------------------------------------------

# Testing the install

Try running the following to make sure that the install was successful.

`bes-config --version`

You should see the version of the bes that you just installed.

Now, let's try to run the BES. Try typing the following on the command
line:

`besctl start`

You should see something like this:

`BES install directory: /usr/local`
`BES configuration file: /usr/local/etc/bes/bes.conf`
`Starting the BES daemon`
`Unix socket name: /tmp/opendap.socket`
`PID: 10491 UID: 6779`

And then you should be able to run the bescmdln application. After
running it you should see a prompt. Type in the command `show version;`,
and then type in the command `exit`.

    bescmdln -h localhost -p 10022


    Type 'exit' to exit the command line client and 'help' or '?' to display the help screen

    BESClient> show version;
    <?xml version="1.0" encoding="UTF-8"?>
    <showVersion>
        <response>
            <BES>
                <lib>
                    <name>bes</name>
                    <version>3.4.1</version>
                </lib>
            </BES>
            <Handlers>
                <DAP>
                    <version>2.0</version>
                    <version>3.0</version>
                    <version>3.2</version>
                </DAP>
            </Handlers>
        </response>
    </showVersion>
    BESClient> exit