This is less of a How To and more of a set of notes transcribed from
paper scraps while I got our server running on
[test.opendap.org](http://test.opendap.org). It's an interesting use
case because we build that code from SVN but the machine is not a
development machine. Here, I'm talking about a CentOS 6 host.

## Host configuration

I used RPM packages for most of the dependencies. Here are the commands
I ran to set the host for a build from SVN:

Get the build environment: yum install java-1.6.0-openjdk java-1.6.0-openjdk-devel ant subversion gcc-c++ flex bison openssl-devel libuuid-devel readline-devel zlib-devel libjpeg-devel libxml2-devel curl-devel emacs
Download and build from source autoconf, automake and libtool: There seems no way around this. I'm building on CentOS 6 here; for newer version of Linux, the *autotools* in RPMs might be good enough. [libtool-2.4.2.tar.gz](http://ftp.gnu.org/gnu/libtool/libtool-2.4.2.tar.gz), [automake-1.14.1.tar.gz](http://ftp.gnu.org/gnu/automake/automake-1.14.1.tar.gz), [autoconf-2.69.tar.gz](http://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.gz)
You can use all of the dependencies we have except the icu code, which is needed for OSX but breaks CentOS: yum install libicu-devel

## Get the Hyrax source from SVN

Get the source: svn co \$svn/branch/shrew/hyrax_1.9_release
Get Tomcat from the dependencies package: cp src/dependencies/downloads/apache-tomcat-7.0.29.tar.gz; tar -xzf apache-tomcat-7.0.29.tar.gz
Configure the metaproject: source spath.sh; autoreconf -fiv; ./configure --prefix=\$prefix --enable-developer
Build the dependencies: cd src/dependencies; make -j9
Build the server: cp Makefile.am.master Makefile.am; make world -j9

## Test the server

Start the parts manually: besctl start; \$CATALINA_HOME/bin/startup.sh
Look for the right processes: *ps -el \| grep bes* and *ps -ef \| grep tomcat*. These commands should show besdaemon, beslistener and tomcat running.
Stop the server: \$CATALINA_HOME/bin/hutdown.sh

## Fiddle with the *hyraxctl* script

Get the hyraxctl script out of its hidden location: *cp src/bes/server/hyraxctl .*
Test it: sudo ./hyraxctl start
Look for the right stuff: *ps -el \| grep bes* and *ps -ef \| grep tomcat*
Install it: ''cp hyraxctl /etc/init.d
Configure the links: *sudo chkconfig --levels 23 --add hyraxctl*
Is it configured correctly?: *chkconfig --list hyraxctl* --\> *hyraxctl 0:off 1:off 2:on 3:on 4:off 5:off 6:off*

## Notes

- On CentOS 6, the ICU-3.6 source in src/dependencies did not work. It
  built and the ncml handler linked to it, but there was a run-time
  fault when the BES loaded the handler.
- I tried a pure RPM install on a VM (OpenStack) where deps were pulled
  from EPEL and the HDF4 handler package installed a handler that failed
  to work at run-time.
- Google *how to configure /etc/rc.d* for pages about how we should
  (re)write the *hyraxctl* script.
- We really need to get Tomcat from the RPM working with Hyrax.