[\<\< back to HowTo Guides](HowTo_guides "wikilink")

## How to configure an Amazon Linux AMI for EC2 to build Hyrax

This describes how to set up a vanilla Amazon Linux AMI virtual machine
so that we can build the Hyrax data server.

## Amazon Linux AMI versions

These instructions have been amended to cover:

- Amazon Linux AMI 2012.09

## Initial configuration

Set up general build stuff that you need:

- *yum install java-1.6.0-openjdk java-1.6.0-openjdk-devel*
- *yum install junit*
- *yum install ant*
- *yum install ant-junit.noarch*
- *yum install make*
- *yum install subversion*
- *yum install gcc-c++*
- *yum install flex*
- *yum install bison*
- *yum install curl-devel*
- *yum install libxml2-devel*
- *yum install libjpeg-devel*
- *yum install zlib-devel*
- *yum install readline-devel*
- *yum install libuuid-devel*
- *yum install openssl-devel*

## Other dependencies

- *yum install libicu-devel*

#### Autotools

- download the latest versions of autoconf, automake and libtool
- ./configure, make, make install

## Building Hyrax from the shrew project

In a (ba)sh shell:

Check out the shrew project
<font size="2">`svn co https://scm.opendap.org/svn/trunk/shrew`</font>

Change your working directory to the shrew directory.
<font size="2">`cd shrew`</font>

<!-- -->

Copy Tomcat from *src/dependencies/downloads* and expand the tar file:
<font size="2">`cp src/dependencies/downloads/apache-tomcat-*.tar.gz .`</font>

<font size="2">`tar -xzf apache-tomcat-*`</font>

<!-- -->

Source the spath file:
<font size="2">`. spath`</font>

<!-- -->

Build the dependancies
<font size="2">`cd src/dependencies`</font>

<font size="2">`make`</font>

> **Note** There's another way to handle the 'dependencies' needed to
> build the server and that is to get the software you need from yum.
> The problem you'll run into is that most of these 'scientific data
> format' packages are not in yum proper and you need to configure an
> additional yum repository. That's not that bad, and it has the
> advantage of basing the build on 'stock' software. The word 'stock' is
> in quotes because those RPMs come from a somewhat non-stock repo... Be
> that as it may, see [How to configure CentOS for the production of RPM
> binaries](ConfigureCentos "wikilink") for the low-down on that option.

### Using the shrew top level Makefile

Change your working directory to the shrew directory.
<font size="2">`cd $prefix`</font>

<!-- -->

Copy the Makefile.am.master to Makefile.am
<font size="2">`cp Makefile.am.master Makefile.am`</font>

<!-- -->

Edit the Makefile.am to be certain that the various dependancies are correctly configured.
<font size="2">`vim Makefile.am`</font>

*Since I am trying to get a new package up I edited this file, mostly I
think you should just be able to make the copy and proceed. However, be
sure to check the versions/directories named in the --with... options
listed at teh start of the Makefile.am with the actual directories they
reference in \$prefix/deps. Mismatches will break the build, especially
down in the handlers where most of those dependencies are used.*

<!-- -->

Engage the autotools process and see how far you get...
<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix`</font>

<font size="2">`make world`</font>

> **Note** Adding *-j N* where N is an integer that is the number of
> *processors on your host + 1* will enable a parallel build. You'll be
> happy about that if you have more than one processor...

*If the above fails then you need to build each Hyrax component by
hand.*

> **Note** But all hope for an easy time is not lost. If the BES and
> libdap built but the handlers fail, it's often because one of the
> handlers' configure script failed and that's often because it could
> not find a dependency (e.g., hdf4 didn't find the correct hdf4
> library). There are two targets you should know about: *modules* and
> *modules-configure*. The latter runs configure for all of the modules.
> If the build fails running configure for one of the modules (which is
> fairly obvious because of the messages that include the word
> 'configure'), then look for the name of thing that failed. Then looks
> at the configure params in the Makefile.am and see that the name of
> the package matches the name of the dependency in \$prefix/deps. For
> example, if \$prefix/deps has hdf-4.2.8 but the Makefile.am says
> *hdf4_arg = --with-hdf4=\$(prefix)/deps/hdf-4.2.10* the target
> *modules-configure* is going to fail when it goes to configure the
> hdf4 handler. Edit the line so that instead of *.../hdf-4.2.10* it
> matches what's in \$prefix/deps, *hdf-4.2.8* in this example. Once
> hacked, you can run *make modules-configure*, *make modules* just like
> that. Since the *world* target stopped, you need to run the tests by
> hand. Either read the Makefile.am to see how or run *make
> daemon-check*. Note that you'll also need to build the OLFS using its
> targets if you go this route. Using *make olfs* should work.

### Building the OLFS

Some targets in the Makfile.am also build the OLFS, but the *world*
target, which otherwise builds the whole server, does not. However,
*ant* was already loaded using the RPMs, so the OLFS build is easy:

Build and install the OLFS
<font size="2">`cd src/olfs`</font>

<font size="2">`ant server`</font>

<font size="2">`cp build/dist/opendap/.war $prefix/apache-tomcat-7.0.29/webaps`</font>

<font size="2">`cd $prefix`</font>

### Start/Test the server

Start and Test the server
<font size="2">`./apache-tomcat-7.0.29/bin/tartup.sh`</font>

<font size="2">`besctl start`</font>

In a web browser, you should see the tomcat page at
*<http://localhost:8080/>* and the Hyrax initial page at
*<http://localhost:8080/opendap/>*

## Building by hand

If the *make world* target fails and work-around in the Note doesn't
work, here's how to build the BES piece by piece. It's actually pretty
simple, but involves repetition and baby-sitting the build machine for
the whole process.

Build and install libdap
<font size="2">`cd $prefix/src/libdap`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the BES
<font size="2">`cd $prefix/src/bes`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the dap-server module
<font size="2">`cd $prefix/src/modules/dap-server`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the csv_handler module
<font size="2">`cd $prefix/src/modules/csv_handler`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the fileout_gdal module
<font size="2">`cd $prefix/src/modules/fileout_gdal`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix --with-gdal=$prefix/deps/gdal-12.15.12`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the fileout_netcdf module
<font size="2">`cd $prefix/src/modules/fileout_netcdf`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix --with-netcdf=$prefix/deps/netcdf-4.1.2`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the fits_handler module
<font size="2">`cd $prefix/src/modules/fits_handler`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix --with-cfits=$prefix/deps/cfitsio`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the freeform_handler module
<font size="2">`cd $prefix/src/modules/freeform_handler`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the gateway_module module
<font size="2">`cd $prefix/src/modules/gateway_module`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the gdal_handler module
<font size="2">`cd $prefix/src/modules/fileout_netcdf`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix --with-gdal=$prefix/deps/gdal-12.15.12`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the hdf4_handler module
<font size="2">`cd $prefix/src/modules/hdf4_handler`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix --with-hdf4=$prefix/deps/hdf-4.2.8`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the hdf5_handler module
<font size="2">`cd $prefix/src/modules/hdf5_handler`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix --with-hdf5=$prefix/deps/hdf5-1.8.6`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the ncml_module module
<font size="2">`cd $prefix/src/modules/ncml_module`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the netcdf_handler module
<font size="2">`cd $prefix/src/modules/fileout_netcdf`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix --with-netcdf=$prefix/deps/netcdf-4.1.2`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the xml_data_handler module
<font size="2">`cd $prefix/src/modules/xml_data_handler`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix`</font>

<font size="2">`make install`</font>

<!-- -->

Build and install the ugrid_functions module
<font size="2">`cd $prefix/src/modules/ugrid_functions`</font>

<font size="2">`autoreconf -vif`</font>

<font size="2">`./configure --prefix=$prefix --with-gridfields=$prefix/deps/gridfields-1.0.1`</font>

<font size="2">`make install`</font>

## Building the ncml_handler when incompatible versions of ICU are resident on the system

### Is there a system ICU?

Check to see what's on the machine in terms of ICU:
<font size="2"><code>


\[root@ip-10-152-156-244 shrew\]# rpm -qa \| grep icu

libicu-4.2.1-9.9.amzn1.x86_64

libicu-devel-4.2.1-9.9.amzn1.x86_64

\[root@ip-10-152-156-244 shrew\]#

</code></font> (no need to be root)

So we can see that icu is there but it's the version 4 code and that is
massively incompatible with the version 3 code. You probably knew all of
that, I think we talked about it, but I just thought I'd show the useful
'rpm -qa' (query all) command.

### Where for art thou, ICU files?

Here's how to see where stuff is: <font size="2"><code>


\[ec2-user@ip-10-152-156-244 shrew\]\$ rpm -qil
libicu-devel-4.2.1-9.9.amzn1.x86_64Name : libicu-devel Relocations: (not
relocatable)

Version : 4.2.1 Vendor: Amazon.com

Release : 9.9.amzn1 Build Date: Mon 09 Jan 2012 04:52:37 PM UTC

Install Date: Wed 30 Jan 2013 05:39:05 AM UTC Build Host:
build-31003.build

Group : Development/Libraries Source RPM: icu-4.2.1-9.9.amzn1.src.rpm

.

.

.

Description :

Includes and definitions for developing with icu.

/usr/bin/icu-config

/usr/include/layout

/usr/include/layout/LEFontInstance.h

/usr/include/layout/LEGlyphFilter.h

/usr/include/layout/LEGlyphStorage.h

/usr/include/layout/LEInsertionList.h

/usr/include/layout/LELanguages.h

/usr/include/layout/LEScripts.h

/usr/include/layout/LESwaps.h

/usr/include/layout/LETypes.h

/usr/include/layout/LayoutEngine.h

/usr/include/layout/ParagraphLayout.h

/usr/include/layout/RunArrays.h

/usr/include/layout/loengine.h

/usr/include/layout/playout.h

/usr/include/layout/plruns.h

/usr/include/unicode

/usr/include/unicode/basictz.h

/usr/include/unicode/bms.h

/usr/include/unicode/bmsearch.h

.

.

.

</code></font>

So if configure looks for icu-config, it'll find the one for icu 4.x
which our handler cannot use - at least not without some tweaking
things. I think the includes are all different (nice, eh?)

### Make the build find the ICU in our dependencies

Put our icu-3.6/bin dir on the PATH in front of everything:
<font size="2"><code>


\[ec2-user@ip-10-152-156-244 shrew\]\$ export
PATH=/home/ec2-user/opendap/shrew/deps/icu-3.6/bin:\$PATH

</code></font> And configure in the ncml_handler should work just fine

But when you try to start the server, massive library not found crap.
That's because on linux you have to run ldconfig for a library to be
found 'automatically'. As a convenience to users, icu left this step
out…

### Make the dynamic library load find our depencencies ICU at runtime

Add our icu-3.6/lib dir to LD_LIBRARY_PATH <font size="2"><code>


\[root@ip-10-152-156-244 shrew\]# export
LD_LIBRARY_PATH=/home/ec2-user/opendap/shrew/deps/icu-3.6/lib/

\[root@ip-10-152-156-244 shrew\]# besctl start

Starting the BES

OK: Successfully started the BES

PID: 11975 UID: 0

\[root@ip-10-152-156-244 shrew\]#

</code></font> Start tomcat and the server is up!

<http://ec2-54-242-224-73.compute-1.amazonaws.com:8080/opendap/data/ncml/fnoc1_improved.ncml>

Why we don't have this these issues on CentOS is likely that they use
icu-3.6 and even if we build our own version, it's not being used. In
fact, the reason we used 3.6 and not 4.2 was that most linux distros
came with 3.6.