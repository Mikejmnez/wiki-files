This describes how to get and build Hyrax from our GitHub repositories.
Hyrax is a data server that implements the DAP2 and DAP4 protocols,
works with a number of different data formats and supports a wide
variety of customization options from tailoring the look of the server's
web pages to complex server-side processing operations. This page
describes how to build the server's source code. If you're working on a
Linux or OS/X computer, the process is similar so we describe only the
linux case; we do not support building the server on Windows operating
systems.

To build and install the server, you need to perform three steps:

1.  Set up the computer to build source code (Install a Java compiler;
    install a C/C++ compiler; add some other tools)
2.  Build the C++ DAP library (*libdap4*) and the Hyrax BES daemon
3.  Build the Hyrax OLFS web application

Quick links if you already know the process:

- [new all-in-one repo that uses shell
  scripts](https://github.com/opendap/hyrax)
- [libdap git repo](https://github.com/opendap/libdap)
- [BES git repo](https://github.com/opendap/bes)
- [OLFS git repo](https://github.com/opendap/olfs)
- [Hyrax dependencies](https://github.com/opendap/hyrax-dependencies)

# Set up a CentOS machine to build code

## Setup CentOS-7

Note that I don't like clicking around to different pages to follow
simple directions, so what follows is a short version of the CentOS 6
configuration information we've compiled for people that help us by
building RPM packages for Hyrax. You can use this to extrapolate how to
configure Ubuntu and OSX (We routinely build on those platforms as
well). The complete instructions are in [Configure
CentOS](ConfigureCentos "wikilink") and describe how to to set up a
CentOS 6 machine to build software. What follows is the condensed
version:

Update the VM
**`yum -y update`**

<!-- -->

Load a basic software development environment:
**`yum install java-1.7.0-openjdk java-1.7.0-openjdk-devel ant ant-junit junit`**
(it's likely that you can use more recent versions of Java)

**`yum install git gcc-c++ flex bison cmake autoconf automake libtool emacs openssl-devel libuuid-devel readline-devel zlib-devel bzip2 bzip2-devel libjpeg-devel libxml2-devel curl-devel libicu-devel cppunit cppunit-devel vim bc`**

<!-- -->

The whole thing, with java-1.8.0
**`yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel ant ant-junit junit git gcc-c++ flex bison cmake autoconf automake libtool emacs openssl-devel libuuid-devel readline-devel zlib-devel bzip2 bzip2-devel libjpeg-devel libxml2-devel curl-devel libicu-devel cppunit cppunit-devel vim bc`**

<!-- -->

If you're going to build RPMs
**`yum install rpm-devel rpm-build redhat-rpm-config`**

<!-- -->

Optional
Download, unpack, build and install the GNU autotools (*but **don't** do
this unless the versions installed using yum don't work*)

- autoconf
  **[`autoconf-2.69.tar.gz`](http://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.gz)**
- automake
  **[`automake-1.14.1.tar.gz`](http://ftp.gnu.org/gnu/automake/automake-1.14.1.tar.gz)**
- libtool
  **[`libtool-2.4.2.tar.gz`](http://ftp.gnu.org/gnu/libtool/libtool-2.4.2.tar.gz)**


build them (***`./configure; make; sudo make install`*** - this should
take no more than three minutes).

## Setup CentOS-8

The CentOS-8 setup is very similar to CentOS-7, but there are some minor
differences.

Update the VM
`yum -y update`

<!-- -->

You will need to enable power-tools for this setup
`yum config-manager --set-enabled powertools`

<!-- -->

Load the basic software development environment plus the additional packages of openjpeg2, jasper, and libtirpc. Note that you may not need *openjpeg2* and *jasper* if you build the dependencies successfully. If you determine that you don't need these, please let us know. JUnit support has also been dropped so we dropped the *`ant-junit junit`* packages from the install list.

<!-- -->


`yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel ant git gcc-c++ flex bison cmake autoconf automake libtool emacs openssl-devel libuuid-devel readline-devel zlib-devel bzip2 bzip2-devel libjpeg-devel libxml2-devel curl-devel libicu-devel cppunit cppunit-devel vim bc openjpeg2-devel jasper-devel libtirpc-devel`

<!-- -->

Tell the machine where to find the tirpc libraries
`export CPPFLAGS=-I/usr/include/tirpc`

`export LDFLAGS=-ltirpc`

<!-- -->

NB: As of 1/28/22 you should not need to do this. The *configure* script should find the correct way to run python on CentOS 8. However, if it does not, our Makefiles (built from *Makefile.am* files) use *python* but a vanilla CentOS 8 machine only has *python3*. Until we fix this, you need to make sure *python* runs a python program. One way is to make a symbolic link between *python3* and *python* in a directory that is on your PATH. **The TODO item here is to make sure *python* exists and can run a program**. It is generally enough to verify that the command exists:
**`which python`**

<!-- -->

Lacking that (which I was on Rocky8) install python
`sudo yum install -y python3`

<!-- -->

If you're going to build RPMs
`yum install rpm-devel rpm-build redhat-rpm-config`

Once you run through the rest of the hyrax build make sure that both
*gdal* and *hdf4* build correctly (look for their libraries in
\$prefix/deps/lib). To build them manually, run **make gdal**, **make
hdf4**, amd **make netcdf4** inside the hyrax-dependencies to build and
install gdal and hdf4

## Rocky 8

*Updated 6/6/2024*

To get the commands ps, which, etc.

`dnf install -y procps`

C++ environment plus build tools

`dnf install -y git gcc-c++ flex bison cmake autoconf automake libtool emacs bzip2 vim bc`

Development library versions

`dnf install -y openssl-devel libuuid-devel readline-devel zlib-devel bzip2-devel libjpeg-devel libxml2-devel curl-devel libicu-devel libtirpc-devel`

Java

`dnf install -y java-17-openjdk java-17-openjdk-devel ant `

Setup DNF so that we can load in some obscure packages from EPEL, etc.,
repos

`dnf install dnf-plugins-core`
`dnf install epel-release`
`dnf config-manager --set-enabled powertools`

Install CppUnit and some more development libraries

`dnf install -y cppunit cppunit-devel openjpeg2-devel jasper-devel`

Install the RPM tools

`dnf install -y rpm-devel rpm-build redhat-rpm-config`

Install the AWS CLI

`dnf install -y awscli`

# A semi-automatic build

Use git to clone the <https://github.com/opendap/hyrax> project and
follow the short instructions in the README file.


'''`git clone `[`https://github.com/opendap/hyrax`](https://github.com/opendap/hyrax)`'''`

Summarized here, those instructions are:

use bash: The shell scripts in this repo assume you are using bash.
set up some environment variables so the server will build an install locally, something that streamlines development: *source spath.sh*
clone the three code repos for the server plus the hyrax dependencies: *./hyrax_clone.sh -v*
build the code, including the dependencies: *./hyrax_build.sh -v*
test the server: Start the BES using *besctl start*
Start the OLFS using*./build/apache-tomcat-7.0.57/bin/startup.sh*

Test the server by loooking at *http://localhost:8080/opendap* in a
browser. You should see a directory named *data* and following that link
should lead to more data. The server will be accessible to clients other
than a web browser.

To test the BES function independently of the front end, use *bescmdln*
and give it the *show version;* command, you should see output about
different components and their versions.

Use *exit* to leave the command line test client.

As described in the README file that is part of the *hyrax* repo, there
are some other scripts in the repo and some options to the *clone* and
*build* script that you can investigate by using -h (help).

# The manual build

In the following, we describe only the build process for CentOS; the one
for OS/X is similar and we note the differences where they are
significant.

## Get Hyrax from GitHub

Use git to clone the <https://github.com/opendap/hyrax> project and
follow the instructions on this page (which differ a bit from ones in
the project's README)


'''`git clone `[`https://github.com/opendap/hyrax`](https://github.com/opendap/hyrax)`'''`

Once you have the *hyrax* project cloned:

set up some environment variables so the server will build an install locally, something that streamlines development
**`source spath.sh`**

clone the three code repos for the server plus the hyrax dependencies
**`./hyrax_clone.sh -v`**

proceed with the rest of the build as described in the following sections of this page

## Important Note

<font color="red">Many of the problems people have with the build stem
from not setting the shell correctly for the build.</font> In the above
section, *make sure* you run **source spath.sh** before you run any of
the building/compiling/testing commands that use the source code or
build files. While the *\$prefix* and *\$PATH* environment variables are
simple to set up, they are needed by most users. When you exit a
terminal window and then open a new one, make sure to (re)source the
*spath.sh* file in the new shell. You don't have to source spath.sh
every time you enter the *hyrax* directory, but you must run it for
every new instance of the shell.

## Compile the Hyrax dependencies

Use git to clone the hyrax-dependencies:

` git clone `[`https://github.com/opendap/hyrax-dependencies`](https://github.com/opendap/hyrax-dependencies)

And then build it. Unlike many source packages, there is no need to run
a configure script, just *make* will do. However, the Makefile in this
package expects *\$prefix* to be set as described above. It will put all
of the Hyrax server dependencies in a subdirectory called *deps*. To
build the dependencies for building RPMs, use *make -j9 for-static-rpm*.

(make sure you're in the directory set to *\$prefix*)

<tt>

git clone <https://github.com/opendap/hyrax-dependencies>
cd hyrax-dependencies
make --jobs=9
*The --jobs=N runs a parallel build with at most N simultaneous compile
operations. This will result in a huge performance improvement on
multi-core machines. **-jN** is the short form for the option.*

cd ..: *Go back up to **\$prefix***

</tt>

**Note:** You can get some of the *dependencies* for Hyrax like *netCDF*
from the EPEL repository, but the versions are often older than Hyrax
needs. Contact us if you want information about using EPEL. At the risk
of throwing people a curve ball, here's a synopsis of the process. Don't
do this unless you know EPEL well. Use
[epel-release-6-8.noarch.rpm](http://mirror.pnl.gov/epel/6/i386/epel-release-6-8.noarch.rpm)
and install it using *sudo yum install epel-release-6-8.noarch.rpm*.
Then install packages needed to read various file formats: *yum install
netcdf-devel hdf-devel hdf5-devel libicu-devel cfitsio-devel
cppunit-devel rpm-devel rpm-build*

## Build *libdap* and the *BES* daemon

#### Get and build libdap4

WARNING: If you have *libdap* already, uninstall it before proceeding.

Build, test and install libdap4 into \$prefix: <b>

    git clone https://github.com/opendap/libdap4
    cd libdap4
    autoreconf -fiv
    ./configure --prefix=$prefix --enable-developer
    make -j9
    make check -j9
    make install
    cd .. # Go back up to ''$prefix''

</b>

#### Get and build the BES and all of the modules shipped with Hyrax

Build, test and install the BES and its modules <b>

    git clone https://github.com/opendap/bes # Clone the BES from GitHub

</b> cd bes \# enter the bes dir. git submodule update --init \# update
the submodules

</pre>

</b> That will clone some additional modules into the directory
*modules*; you need to do this! (Previously it was an optional step).
See [git submodule](http://git-scm.com/docs/git-submodule) for
information about all you can do with git's submodule command. Also note
that this does not checkout a particular branch for the submodules; the
modules are left in the 'detached head' state. To checkout a particular
branch like 'master', which is important if you'll be making changes to
that code, use *git submodule foreach 'git checkout master'*. <b>

    autoreconf --force --install --verbose # You can use -fiv instead of the long options.

</b>

These means, when starting from a freshly cloned repo, run all of the
autotools commands and install all of the needed scripts. <b>

    ./configure --prefix=$prefix  --with-dependencies=$prefix/deps --enable-developer

</b>


Notes:

- The --with-deps... is not needed if you load the dependencies from
  RPMs or otherwise have them installed an generally accessible on the
  build machine.
- The --enable-developer option will compile in all of the debugging
  code which may affect performance even if the debugging output is not
  enabled.

<b>

    make -j9
    make check -j9

</b> Some tests may fail and adding *-k* ignores that and keeps make
marching along. *Note that you must run **make** before **make check**
in the bes code*. <b>

    make install
    cd .. # Go back up to $prefix

</b>

#### Test the BES

Start the BES and verify that all of the modules build correctly. <b>

    besctl start # Start the BES.

</b> Given that *\$prefix/bin* is on your *\$PATH*, this should start
the BES. You will not need to be root if you used the
*--enable-developer* switch with configure (as shown above), otherwise
you should run *sudo besctl start* with the caveat that as root
*\$prefix/bin* will probably not be n your *\$PATH*.


If there's an error (e.g., you tried to start as a regular user but need
to be root), edit bes.conf to be a real user (yourself?) in a real group
(use 'groups' to see which groups you are in) and also check that the
bes.log file is *not* owned by root.

Restart.

<b>

    bescmdln # Now that the BES is running, start the BES testing tool
    BESClient> show version; # Send the BES the version command to see if it's running

</b>


Take a quick look at the output. There should be entries for libdap, bes
and all of the modules.

<b>

     BESClient> exit; # Exit the testing tool

</b>

Note that even though you have exited the *bescmdln* test tool, the BES
is still running. That's fine - we'll use it in just a bit - but if you
want to shut it down, use *besctl stop*, or *besctl pids* to see the
daemon's processes. If the BES is not stopping, *besctl kill* will stop
all BES processes without waiting for them to complete their current
task.

## Build the Hyrax *OLFS* web application

The OLFS is a java servlet built using ant. The OLFS is a java servlet
web application and runs with Tomcat, Glassfish, etc. You need a copy of
Tomcat, but our servlet does not work with the RPM version of Tomcat.
Get [Tomcat 7 from Apache](http://tomcat.apache.org/download-70.cgi).
Note that if you built the dependencies from source using the
*hyrac-dependencies-1.10.tar* then there is a copy of Tomcat in the
*hyrax-dependecies/extra_downloads directory. You can unpack the Tomcat
tar file in*\$prefix*. I'll assume you have the Apache Tomcat tar file
in*\$prefix''.

tar -xzf apache-tomcat-7.0.57.tar.gz: Expand the Tomcat tar ball
git clone <https://github.com/opendap/olfs>: Get the OLFS source code
cd olfs: change directory to the OLFS source
ant server: Build it
cp build/dist/opendap.war ../apache-tomcat-7.0.57/webapps/: Copy the opendap web archive to the tomcat webapps direcotry.
cd ..: Go up to *\$prefix*
./apache-tomcat-7.0.57/bin/startup.sh: Start Tomcat

## Test the server

You can test the server several ways, but the most fun is to use a web
browser. The URL *<http://><machine>:8080/opendap* should return a page
pointing to a collection of test datasets bundled with the server. You
can also use *curl*, *wget* or any application that can read from
OpenDAP servers (e.g., Matlab, Octave, ArcGIS, IDL, ...).

## Stopping the server

Stop both the BES and Apache

besctl stop
./apache-tomcat-7.0.57/bin/shutdown.sh

Note that there is also a *hyraxctl* script that provides a way to start
and stop Hyrax without you (or *init.d*) having to type separate
commands for both the BES and OLFS. This script is part of the BES
software you cloned from git.

## Building select parts of the BES

Building just the BES and one of more of its handlers/modules is not at
all hard to do with a checkout of code from git. In the above section on
building the BES, simply skip the step where the submodules are cloned
(*git submodule update --init*) and link configure.ac to
*configure_standard.ac*. The rest of the process is as shown. The end
result is a BES daemon without any of the standard Hyrax modules (but
support for DAP will be built if *libdap* is found by the configure
script).

To build modules for the BES, simply go to *\$prefix*, clone their git
repo and build them, taking care to pass set *\$prefix* when calling the
module's *configure* script.

Note that it is easy to combine the 'build it all' and 'build just one'
processes so that a complete Hyrax BES can be built in one go and then a
new module/handler not included in the BES git repo can be built and
used. Each module we have on GitHub has a *configure.ac*, *Makefile.am*,
etc., that will support both kinds of builds and [Configuration of BES
Modules](Configuration_of_BES_Modules "wikilink") explains how to take a
module/handler that builds as a standalone module and tweak the build
scripts so that it's fully integrated into the Hyrax BES build, too.

# Building on Ubuntu

This was tested using Xenial (Ubuntu 16)

*sudo apt-get update*

Packages needed:

*sudo apt-get install ...*

**ant junit git flex bison autoconf automake libtool emacs openssl bzip2
libjpeg-dev libxml2-dev curl libicu-dev vim bc make cmake uuid-dev
libcurl4-openssl-dev libicu-dev g++ zlib1g-dev libcppunit-dev
libssl-dev**