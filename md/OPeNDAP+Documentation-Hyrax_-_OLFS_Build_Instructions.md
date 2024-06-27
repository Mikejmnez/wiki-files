How to build the OLFS from either a source code distribution, or
directly from our [Subversion](http://subversion.tigris.org/)
repository.

------------------------------------------------------------------------

# Dependencies

## [Java 5 Enterprise Edition (J2EE)](http://java.sun.com/javaee/index.jsp)

***Minimum***: Java 1.5

***Recommended***: Java 1.5.0 or newer.

This software was developed and tested against the 1.5.0_06-112 JVM on
OS-X.

## [Apache Tomcat](http://tomcat.apache.org/index.html)

***Minimum***: 5.5

***Recommended***: 6.x

The servlets were developed and tested using Jakarta/Tomcat 6.0.14. In
particular, the code was linked against the servlet.jar bundled with
this particular version of Tomcat.

## [ANT](http://ant.apache.org/)

***Minimum***: 1.6.5

***Recommended***: 1.6 or higher

This software now builds entirely with ANT. If you haven't already got
ANT, then go get it: You need it. There is a build file in the top level
directory, *olfs/build.xml* which relies on a couple of build files
located in the *olfs/buildfiles* directory. The serious developer will
want to read and understand all of the build related files.

***Compiling the source***: This should be straight forward. Just go to
the top level directory and type "ant compile". For more detailed
instructions you should read the master build file, *olfs/build.xml*

## Additional Libraries

The core software depends on several third part libraries. All of these
are included with the distribution, and these (should) be the ones that
the code was compiled against for development and testing purposes. It
is possibly (in fact likely) that newer versions of these libraries are
available from the various development teams that create these products.
Replace them at your own risk.

- The [Apache Commons Command Line
  Interface](http://jakarta.apache.org/commons/cli/) package, version
  1.0
- The [JDOM](http://www.jdom.org/) package, version 1.1.1

<!-- -->

- The [NLOG4J](http://www.slf4j.org/nlog4j/) package, version 1.5.8 and
  [logback](http://logback.qos.ch/), version 0.9.16
- The [Xerces XML Parser](http://xerces.apache.org/xerces2-j/index.html)
  package, version 2.8.1 (you will need both the ***xercesimpl.jar**''
  and the***xml-apis.jar**'')

------------------------------------------------------------------------

# Getting The Source Code

### As A Source Code Distribution

The most recent source code distribution will available at both the
[Hyrax download page](http://opendap.org/download/hyrax.html) and the
[OLFS download page](http://opendap.org/download/olfs.html). If you are
attempting to build the OLFS in conjunction with the BES - for example
you are building Hyrax - then get both the source distributions from the
same Hyrax release on the [Hyrax download
page](http://opendap.org/download/hyrax.html), other wise you may
experience compatibility issues.

Got get the source code bundle. It will be a file named something like
olfs-*x.x.x*-src.jar

Unpack the bundle using the command:

`jar -xvf olfs.`*`x.x.x`*`-src.jar`

This will unpack the source code distribution into a directory called
*olf.x.x.x-src*

### From Subversion

- Check out the software for the OLFS from our
  [Subversion](http://subversion.tigris.org/) repository located at
  <https://scm.opendap.org/svn/trunk/olfs>:

`svn co `[`https://scm.opendap.org/svn/trunk/olfs`](https://scm.opendap.org/svn/trunk/olfs)

------------------------------------------------------------------------

# Building the code

Building the OLFS software should be very simple.

1.  Make sure you have installed the current J2EE and ANT (see above)

2.  Enter the top level directory of the OLFS source code tree (*olfs*
    if you got the source code from Subversion, *olfs-.x.x.x-src* if you
    got it from a distribution bundle.)

3.  Run the command:

    `ant compile`

    This will compile all of the distributed software, but nothing more.
    For more detailed information you should read (and try to
    understand) the ANT build file, *build.xml* and the related files in
    the *buildfiles* directory. *For those of you using an IDE you will
    have to follow your own "path" to get this going.*

4.  To perform an end to end compile that builds the webb application
    and bundles it in a *.war* file, run the command:

    `ant server`

    Find the file *olfs/build/dist/opendap.war*, that's the thing you
    want!

------------------------------------------------------------------------

# Installing the OLFS from a source code build

1.  Copy the file *olfs/build/dist/opendap.war* tp
    *\$CATALINA_HOME/webapps*
2.  (Re)start Tomcat.
3.  Done!