## MacBook Pro

June 6th, 2012

Installed Lion (OS X 10.7.4) on a newly formatted disk.

System: MacBook Pro, 2.4 GHz Core 2 Duo, 2GB RAM.

Install command line gcc compiler.

1.  Install XCode
2.  Open XCode.
3.  Goto XCode-\>Preferences-\>Downloads-\>Components
4.  Install the Command Line Tools component.
5.  Quit XCode after install completes.
6.  Verify: In a shell type the command *gcc --version*. It should work,
    as in not fail.

Installed:

- libtool: /usr/local/bin/libtool



version: ltmain.sh (GNU libtool) 2.4.2 from source

- m4: /usr/local/bin/m4



version: m4 (GNU M4) 1.4.13 from source

- autoconf: /usr/local/bin/autoconf



version: autoconf (GNU Autoconf) 2.65 from source

- automake: /usr/local/bin/automake



version: automake (GNU automake) 1.11 from source

- dejagnu: /usr/local/bin/runtest (and others)



version: dejagnu-1.4.4 from source

- cppunit: /usr/local



version: cppunit-1.12.1 from source

- Checked the shrew project out from subversion (I am currently working
  on the *xmlrequest* branch:
  <http://scm.opendap.org/svn/branch/shrew/hyrax_1.8_release>).

In the top level I ran:


`source spath.sh`

`autoreconf -fi`

`./configure --prefix=$prefix`

`make`

`make check`

And it failed one test:

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