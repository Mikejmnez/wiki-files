libdap on OS-X 10.5.3 (Leopard)

## PowerPC

June 5th, 2008

Installed Leopard (OS-X 10.5) n a newly formatted disk.

System: PowerMac quad core G5, 6GB RAM

Installed:

- bison: /usr/local/bin/bison



version: bison (GNU Bison) 2.3

- libtool: /usr/local/bin/libtool



version: ltmain.sh (GNU libtool) 1.5.26 (1.1220.2.492 2008/01/30
06:40:56)

- autoconf: /usr/local/bin/autoconf



version: autoconf (GNU Autoconf) 2.62

- automake: /usr/local/bin/automake



version: automake (GNU automake) 1.10.1

- m4: /usr/local/bin/m4



version: m4 (GNU M4) 1.4.11

In the top level of libdap ran:


`gnulib-tool --import --dir=. --lib=libgnu --source-base=gl --m4-base=gl/m4 --doc-base=--dir=. --aux-dir=conf --lgpl --libtool --macro-prefix=gl regex`

`autoreconf`

`./configure`

`make`

`make check`

libdap fails 3 tests:

- FAIL: test.yb
- FAIL: test.yd
- FAIL: test.yg

But I think that happened under Tiger (10.4.x) too.

Same results for trunk and 3.8.0 source release.

### Update

Looks like all tests pass now!
<https://scm.opendap.org/trac/ticket/1146>

## MacBook Pro

October 26th, 2008

Installed Leopard (OS-X 10.5) on a newly formatted disk.

System: MacBook Pro, 2.53GHz Core 2 Duo, 4GB RAM.

Installed:

- bison: /usr/local/bin/bison



version: bison (GNU Bison) 2.3

- libtool: /usr/local/bin/libtool



version: ltmain.sh (GNU libtool) 2.2.6b from source

- autoconf: /usr/local/bin/autoconf



version: autoconf (GNU Autoconf) 2.65 from source

- automake: /usr/local/bin/automake



version: automake (GNU automake) 1.11 from source

- m4: /usr/local/bin/m4



version: m4 (GNU M4) 1.4.13 from source

- subversion: /usr/local/bin/svn



version: subversion-1.5.2 from Collab.net binary

- dejagnu: /usr/local/bin/runtest (and others)



version: dejagnu-1.4.4 from source

- cppunit: /usr/local



version: cppunit-1.12.1 from source

- Checked libdap out from subversion (I am currently working on the
  *xmlrequest* branch: <https://scm.opendap.org/svn/branch/xmlrequest>)
  so I took the libdap located there.

In the top level of libdap ran:


`autoreconf`

`./configure`

`make`

`make check`

And it failed one test:

`..F...`


`!!!FAILURES!!!`
`Test Results:`
`Run:  5   Failures: 1   Errors: 0`


`1) test: DODSFilterTest::get_das_last_modified_time_test (F) line: 195 DODSFilterTest.cc `
`assertion failed`
`- Expression: df3->get_das_last_modified_time() == st.st_mtime`


`FAIL: DODSFilterTest`


installed it anyway...

- szip: /usr/local



version: szip-2.1 from source (./configure --prefix=/usr/local)

- zlib: /usr/local



version: zlib-1.2.3 from source

- jpeg lib: /usr/local



version: jpeg-6b from source
(ftp://ftp.uu.net/graphics/jpeg/jpegsrc.v6b.tar.gz)


\[note: jpeg-7 is now shipping, but hdf as of hdf4.2r4 won't pass tests
with it! If you need 7, best to install it in it's own dir and NOT
/usr/local ... (mjohnson 1 oct 2009)\]

Notes:

\- Doing a *make install* will only install the executable files. To
install the libraries do the following:


`cp libjpeg.a /usr/local/lib`

`cp *.h /usr/local/include`

\- You must install the jpeg library and includes in /usr/local or the
./configure script for the hdf4_handler won't find it.

- netcdf: /usr/local



version netcdf-3.6.3 from source (./configure --prefix=/usr/local)

- HDF4: /usr/local



version: HDF4.2r3 from source, (./configure --disable-fortran
--with-jpeg=/usr/local --with-szlib=/usr/local --prefix=/usr/local)

<!-- -->



**Updated**

<!-- -->



Configure with: *./configure --disable-fortran --with-szlib=/usr/local
--disable-netcdf --enable-production --enable-shared*


If you don't configure this correctly it's possible to install the HDF4
version of netcdf on top of the regular version and that will make all
kinds of trouble.

<!-- -->



**Notes on libjpeg and hdf4.2r4** \[mjohnson 1 oct 2009\]: HDF4.2r4
won't pass tests with jpeg-7, the new dist. If you have it installed,
you need to remove it and install jpeg-6b. Also, 4.2r4 fails to build
with --enable-shared.

- hdf5: /usr/local



version hdf5-1.8.1 from source (./configure --prefix=/usr/local
--with-szlib=/usr/local --enable-cxx)

Continuing in the *xmlrequest* branch I checked out, built, and
installed:

- bes
- dap-server
- freeform_handler
- hdf4_handler
- hdf5_handler
- netcdf_handler

## [Building Hyrax-1.6.2 Package Installer(s) for OS-X 10.5 (Leopard)](Hyrax-1.6.2_Packages_for_OS-X "wikilink")

## Snow Leopard 2.6.8

1.  Update to SnowLeopard
2.  Update autoconf from 2.61 to 2.68