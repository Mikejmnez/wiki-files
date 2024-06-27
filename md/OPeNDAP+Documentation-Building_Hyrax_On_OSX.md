## Building Hyrax Dependencies


NB: These are Nathan's original notes from the Hyrax 1.6.2 build for
OS/X.

In an effort to build a binary distribution for Hyrax I built all of our
dependancies from source and rolled them up with PackageMaker. Here's an
ordered list what got built and installed. Note that with some noted
exceptions, each of the dependancies gets installed into its own
directory.

### Development System

Operating System
OS-X 10.5.8, 10.6

<!-- -->

Hardware
MacBook Pro, 2.53GHz Intel Core 2 Duo, 4GB RAM

### szip

**Required by:** HDF4, hdf4_handler
**File:** szip-2.1.tar.gz\*

The **hdf4_handler**s configure script does not provide options to allow
the location of szip to be specified. When the **hdf4_handler**s
configure script is run it looks in the `--with-hdf4` location of szip,
after that it looks in the usual places. In order to ensure that the
correct szip (the one sent with the installer) was located we needed to
install the szip library into the HDF4 library location.

Build process
./configure --prefix=\$prefix/deps/hdf-4.2.5

make

make check

make install

### zlib

**Required by:** HDF4, hdf4_handler
**File:** zlib-1.2.3.tar.gz

The **hdf4_handler**s configure script does not provide options to allow
the location of zlib to be specified. When the **hdf4_handler**s
configure script is run it looks in the `--with-hdf4` location of zlib,
after that it looks in the usual places. In order to ensure that the
correct zlib (the one sent with the installer) was located we needed to
install the zlib library into the HDF4 library location.

Build process
./configure --prefix=\$prefix/deps/hdf-4.2.5

make

make check

make install

### jpeg

**Required by:** HDF4, hdf4_handler
**File:** jpegsrc.v6b.tar.gz

The **hdf4_handler**s configure script does not provide options to allow
the location of jpeg to be specified. When the **hdf4_handler**s
configure script is run it looks in the `--with-hdf4` location of jpeg,
after that it looks in the usual places. In order to ensure that the
correct jpeg (the one sent with the installer) was located we needed to
install the jpeg library into the HDF4 library location.

Build process
./configure --prefix=\$prefix/deps/hdf-4.2.5

make

make check

mkdir -p /usr/local/opendap/servers/hyrax-1.6.2/bin

mkdir -p /usr/local/opendap/servers/hyrax-1.6.2/man/man1

make install

cp libjpeg.a \$prefix/lib

cp \*.h \$prefix/include

### hdf5

**Required by:** hdf5_handler
**File:** hdf5-1.8.5-patch1.tar.gz

Build process
./configure --prefix=\$prefix/deps/hdf5-1.8.5-patch1

make

make check

make install

### netcdf

**Required by:** netcdf_handler
**File:** netcdf-3.6.3.tar.gz

Build process
./configure --prefix=\$prefix/deps/netcdf-3.6.3

make

make check

make install

### hdf4

**Required by:** hdf4_handler
**File:** hdf-4.2.5.tar.gz

Build process
./configure --disable-fortran --enable-production --enable-shared
--disable-netcdf --prefix=\$prefix/deps/hdf-4.2.5
--with-szlib=\$prefix/deps/hdf-4.2.5 -with-jpeg=\$prefix/deps/hdf-4.2.5
--with-netcdf=\$prefix/deps/netcdf-3.6.3

make

make check

make install

### hdf-eos2

**Required by:** hdf4_handler
**File:** HDF-EOS2.17v1.00.tgz

Build process
./configure --disable-fortran --enable-production --enable-shared
--enable-install-include --prefix=\$prefix/deps/hdf-4.2.5
--with-jpeg=\$prefix/deps/hdf-4.2.5 --with-szlib=\$prefix/deps/hdf-4.2.5
--with-zlib=\$prefix/deps/hdf-4.2.5 --with-hdf4=\$prefix/deps/hdf-4.2.5

make

make check

make install

### icu

**Required by:** ncml_handler
**File:** icu4c-3_6-src.tgz

At run time ICU dependents failed to link correctly. Using `otool -L` we
could see in our libraries all of the ICU dependancies were shown like:


`libicui18n.dylib.36 (compatibility version 36.0.0, current version 36.0.0)`

When all the other libs had a full path like this:


`/usr/local/opendap/servers/hyrax-1.6.2/lib/libbes_dap.3.dylib (compatibility version 5.0.0, current version 5.5.0)`

The ICU build was producing only dynamic libraries where the links were
only to the package name (no path). This also caused programs linking to
one of theICU libs to get "path free" links.

James found the following in the last reply to this thread:
<http://bugs.php.net/bug.php?id=34089> (Thanks James!)



*The icu build system produces MAC platform libraries with an install
name which does not include a path, this can be seen by running "otool
-L" on any of the libraries or anything you build that links against any
of the libraries.*

*My solution was to edit icu's darwin config file found at
"<icu_distribution_dir>/source/config/mh-darwin"*

*By changing line 28 from:*


*`LD_SONAME = -Wl,-compatibility_version -Wl,$(SO_TARGET_VERSION_MAJOR) -Wl,-current_version -Wl,$(SO_TARGET_VERSION) -install_name $(notdir $(MIDDLE_SO_TARGET))`*

*To:*


*`LD_SONAME = -Wl,-compatibility_version -Wl,$(SO_TARGET_VERSION_MAJOR) -Wl,-current_version -Wl,$(SO_TARGET_VERSION) -install_name $(libdir)/$(notdir $(MIDDLE_SO_TARGET))`*

I made the recommended change and everything worked great.

Build process
./runConfigureICU MacOSX --prefix=\$prefix/deps/icu-3.6

make

make check

make install

## Building Hyrax C++ (libdap, bes, handlers and modules)

**URL:** <http://scm.opendap.org/svn/branch/shrew/hyrax_1.6.2_release>
**Revision:** 23904

Summary
I patched all of the Makefile.am targets for the OS-X packages
(*`make pkg`*). I found numerous problems with small details such as
incorrect package names for PackageMaker and failure to preserve
configure options in the package build. The code works now, but there
are soma caveats for some of the packages. Details in each section
below.

### libdap

Build process
./configure --prefix=\$prefix

make pkg

make check

make install

### bes

Build process
./configure --prefix=\$prefix

make pkg

make check

make install

### dap-server

Build process
./configure --prefix=\$prefix

make pkg

make check

make install

### fileout_netcdf

The `configure` command accepts: <code>


--with-netcdf=ARG netcdf directory

--with-netcdf-include=ARG netcdf include directory

--with-netcdf-libdir=ARG netcdf library directory

</code> **However for OS-X packages ONLY** `--with-netcdf` **will
work!**

Build process
./configure --prefix=\$prefix --with-netcdf=\$prefix/deps/netcdf-3.6.3

make pkg

make check

make install

### freeform_handler

Build process
./configure --prefix=\$prefix

make pkg

make check

make install

### hdf4_handler

The `configure` command accepts: <code>


--with-hdf4=ARG hdf4 directory

--with-hdf4-include=ARG hdf 4 include directory

--with-hdf4-libdir=ARG hdf 4 library directory

</code> **However for OS-X packages ONLY** `--with-hdf4` **will work!**

Build process
./configure --prefix=\$prefix --with-hdf4=\$prefix/deps/hdf-4.2.5
--with-hdfeos2=\$prefix/deps/hdf-4.2.5

make pkg

make check

make install

### hdf5_handler

The `configure` command accepts: <code>


--with-hdf5=ARG hdf5 directory

--with-hdf5-include=ARG hdf5 include directory

--with-hdf5-libdir=ARG hdf5 library directory

</code> **However for OS-X packages ONLY** `--with-hdf5` **will work!**

Build process
./configure --prefix=\$prefix
--with-hdf5=\$prefix/deps/hdf5-1.8.5-patch1

make pkg

make check

make install

### nectdf_handler

The `configure` command accepts: <code>


--with-netcdf=ARG netcdf directory

--with-netcdf-include=ARG



netcdf include directory

--with-netcdf-libdir=ARG



netcdf library directory

</code> **However for OS-X packages ONLY** `--with-netcdf` **will
work!**

Build process
./configure --prefix=\$prefix --with-netcdf=\$prefix/deps/netcdf-3.6.3

make pkg

make check

make install

### ncml_handler

The `configure` command accepts: <code>


--with-icu-prefix=PREFIX



Prefix where icu is installed (optional)

--with-icu-exec-prefix=EPREFIX



Exec prefix where icu is installed (optional)

</code> **However for OS-X packages ONLY** `--with-icu-prefix` **will
work!**

Build process
./configure --prefix=\$prefix--with-icu-prefix=\$prefix/deps/icu-3.6

make pkg

make check

make install

## Building Hyrax Java (olfs)

This required no additional code building.

### Tomcat

I got the latest apache Tomcat (6.0.29) from
<http://tomcat.apache.org/download-60.cgi>

Build process
cd \$prefix

tar -xvf \$downloads/apache-tomcat-6.0.29.tar.gz

find . -exec xattr -d com.apple.quarantine {} \\ -print


Because I used Safari to grab the tomcat archive file and Finder to open
it OS-X marked everything in the file system with the
*com.apple.quarantine*
\[<http://en.wikipedia.org/wiki/Extended_file_attributes>\| extended
file attribute\]. This causes OS-X to produce the *This is a web
downloaded file, are you sure you want to do this* user dialogs when it
is first run. You can see this using an `ls -l` command. The presence of
extended attributes is denoted by a trailing `@` sign at the end of the
permissions string.

For example


`drwxr-xr-x@ 9 ndp staff 476 Dec 3 14:59 apache-tomcat-6.0.29`

I removed this using `find` and the `xattr` command:


`find . -exec xattr -d com.apple.quarantine {} \; -print`

### OLFS

I downloaded OLFS-1.7.1 from
<http://www.opendap.org/pub/olfs/olfs-1.7.1-webapp.tgz> and unpacked it.
From the bundle I took the opendap.war file and copied it to
\$prefix/tomcat/webapps

Build process
cd \$prefix

cp \$downloads/olfs-1.7.1-webapp/opendap.war tomcat/webapps

## Shell Scripts

I also added a shell script that can be used to configure the users
shell so that they can easily control Hyrax. There is both a bash and a
c-shell version in the top distribution directory.

## PackageMaker

### Hyrax Dependancies

In order to bundle the dependancies into an OSX package I used the
pattern established by James in
<http://scm.opendap.org/svn/trunk/libdap/Makefile.am> where he used the
automake DESTDIR property to deploy the linked libraries to a directory
of his choosing. I also created an OSX_Resources directory based on
James' work and used it to generate the dependancies package.

Build process
cd \$project_dir

mkdir -p macosx/\$prefix

cd \$prefix

tar -cv \$project_dir/deps.tar deps

cd \$project_dir/macosx/\$prefix

tar -xvf project_dir/deps.tar

cd \$project

/Developer/usr/bin/packagemaker --root macosx --id
org.opendap.hyrax_dependancies-1.6.2 --title "Hyrax-1.6.2 dependancies"
--version "1.6.2" --out hyrax-1.6.2-depends.pkg --resources
OSX_Resources

### Hyrax C++ (libdap, bes, modules and handlers)

All of the C++ components have a OS-X package target. I did have to make
modifications to the production rules (Makefile.am) and some of the
OSX_Resources for every one of these. They are all pretty clean now.

Things to do:
Most of the OSX_Resources/InstallationCheck scripts have hardwired PATH
environments that point to this (hyrax-1.6.2) install. The production
rules need to be tweaked to make this dependent on the build prefix.

### Hyrax Java (The OLFS)

In order to bundle the dependancies into an OSX package I used the
pattern established by James in
<http://scm.opendap.org/svn/trunk/libdap/Makefile.am> where he used the
automake DESTDIR property to deploy the linked libraries to a directory
of his choosing. I also created an OSX_Resources directory based on
James' work and used it to generate the dependancies package.

Basically I grabbed the tomcat tree (with the olfs opendap.war file in
place) from \$prefix, popped it into the macosx directory in my olfs
package project and called packagemaker.

Build process
cd \$project_dir

mkdir -p macosx/\$prefix

cd \$prefix

tar -cv \$project_dir/olfs.tar apache-tomcat-6.0.29

cd \$project_dir/macosx/\$prefix

tar -xvf project_dir/olfs.tar

cd \$project

/Developer/usr/bin/packagemaker --root macosx --id
org.opendap.olfs-1.7.1 --title "OPeNDAP olfs-1.7.1" --version "1.7.1"
--out olfs-1.7.1.pkg --resources OSX_Resources

### Hyrax-1.6.2

#### Meta-Package

Once I was able to build all all of the component packages I was able to
use the PackageMaker GUI to build a meteapackage that combined all of
the previously packaged components. I allowed the user to choose which
handlers they wish to install.

#### OneBigPackage

I was also able to use the PackageMaker GUI to build a single package
based on the body of installed code in \$prefix.