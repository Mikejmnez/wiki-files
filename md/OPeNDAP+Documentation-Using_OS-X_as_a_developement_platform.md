There are a number of tricks to get OS-X to work as an OPeNDAP
development platform. Apple provides their own versions of some of these
tools and they are not compatible with the production rules for OPeNDAP.
You do not need to install thses in any particular order. Once you've
got them installed **make sure that they are on your path**! Otherwise
the exisiting Apple versions may be used and that would be a bad thing.

# Building and Installing Software

OS-X occupies a fairly unique place in the collection of modern
operating systems: It is a full featured variant of the UNIX ooperating
system that provides all of the modern conveniences of a user centric
desktop (Apple Macintosh). As such many software applications can be
installed using a GUI based install tool. Other applications, such as
the majority of the software listed below, must be installed using the
traditional UNIX style code distribution mechanisms.

This process typically works like ths:

1.  **Download the software bundle.** This is typically a compressed
    *tar* file, usually named after the software component and version
    thereof.
2.  **Unpack the software.** This can be done from the command line
    (either using a *Terminal* or an *xterm*) or using the finder. The
    finder is able to uncompress and unpack compressed *tar* files.
    However, later steps will *'require* a comand line so you might as
    well start here. To unpack the software using the command line:
    - Open a "terminal" window (Lanuch the *Terminal* application or
      launch *X11* and open an *xterm*)
    - Change directory to the directory containing the compressed *tar*
      file.
    - Run the command:
      <font style="font-family:courier">    zcat filename.tar.gz \| tar
      -xvf -</font>
      This should unpack the software, typically creating a single
      directory containing the software.
3.  **Configure the software.**In a *terminal* window change directory
    to the directory containing the software. Run the command:
    <font style="font-family:courier">    ./configure</font>
4.  **Build the software.** In the same *terminal* run the command:
    <font style="font-family:courier">    make</font>
5.  **Test the software.** In the same *terminal* run the command:
    <font style="font-family:courier">    make check</font>
6.  **Install the software.** In the same *terminal* run the command:
    <font style="font-family:courier">    sudo make install</font>
    This command installs the software using the *root* or *superuser*
    shell (via the *sudo* command). You will be prompted for your
    password. If you do not have administrator privleges on you OS-X
    system then, well... Get Them.
7.  **Adjust Your Path** Most of these packages install themselves into
    */usr/local* you will need to modify your *path* environment
    variables to ensure that */usr/local/bin* appears on your path
    before other things (like */usr/bin*). You can do this by modifying
    the "dot" files for your shell in your home directory (For **bash**,
    the default shell for OS-X, that's *.profile*) You may also wish to
    edit the path variable in the OS-X environment plist. This can be
    accomplished by issuing the command:
    <font style="font-family:courier">     open
    ~/.MacOSX/environment.plist </font>
    In a *terminal*. This will open the PlistEditor and from there
    you're on your own.
8.  **Fini** At this point the software package should be installed.

# libdap core development

These are the things you need to compile/modify the core OPeNDAP
software known as **'libdap**'.

## GCC, The Gnu Compiler (Apple Developer Tools)

The easy way to get the Gnu compiler is to install the developers bundle
from the OS-X installation disks. This is appears as an "Xcode Tools" on
(my) Tiger distribution disks. Open that folder and run the
**XcodeTools** installer. The default install should be enough. This
will give you the GNU gcc 4.x compiler, which you must have.

Once you have that installed I suggest that you run the appliction
*/Developer/Applications/Utilities/CrashReporterPrefs* and use it to set
the system crash reporting level to "Developer". You'll thank me for
this later.

## [Subversion](http://subversion.tigris.org/)

Although OS-X comes with a bundled Subversion client I suggest that you
get a recent one.

***Minimum***: subversion 1.4.4

***Recommended***: subversion 1.4.4

You will probably want to get a to get a binary of the Subversion
client. I had good luck with [This
One](http://downloads.open.collab.net/binaries.html) which comes with a
nice GUI based installer.

## [autoconf](http://www.gnu.org/software/autoconf/)

Apple ships OS-X with an *unusual* version of autoconf. Go get the Gnu
one, compile, test, and install it.

***Minimum***: autoconf 2.57

***Recommended***: autoconf 2.61

## [automake](http://www.gnu.org/software/automake/)

Apple ships OS-X with an *unusual* version of automake. Go get the Gnu
one, compile, test, and install it.

***Minimum***: automake 1.9.6

***Recommended***: automake 1.9.6

## [libtool](http://www.gnu.org/software/libtool)

Apple ships OS-X with an *unusual* version of libtool. Go get the Gnu
one, compile, test, and install it.

***Minimum***: libtool 1.5.18

***Recommended***: libtool 1.5.22

## [DejaGnu](http://www.gnu.org/software/dejagnu/)

If you intend to actually test your changes to the software you will
need to install the GNU DejaGnu package to run the bundled tests.

***Minimum***: dejagnu 1.4.4

***Recommended***: dejagnu 1.4.4

# Additional Software Packages

There are several addtional software packages that you will need if you
wish to work with different OPeNDAP componets. Since the Hyrax server
has handlers for HDF4, HDF5, and NetCDF datatypes you will need to
install those libraries in order to work with the handlers.

## [HDF4](http://hdf.ncsa.uiuc.edu/hdf4.html)

*(This discussion is based on the HDF4.2r1 release)*

In order to build and use the
[hdf4_handler](http://scm.opendap.org:8090/svn/trunk/hdf4_handler/)
package used by the BES you will need to successfully build and install
the HDF4 libraries and dependancies (szip, zlib, and jpeg). We have had
no success using the OS-X binary distributions of HDF4 and it's
dependancies offered at the [HDF4 home
page](http://hdf.ncsa.uiuc.edu/hdf4.html). We recommend that you build
HDF4 and it's components from source. Because it's so difficult to get
this done successfully I am providing a detailed accounting of how to
make it work:

Go get HDF4 from <http://hdf.ncsa.uiuc.edu/hdf4.html>
This entails retrieving the source for the HDF4, szip, zlib, and jpeg
software packages. Get the source for all of these via the links found
at the [HDF4 home page](http://hdf.ncsa.uiuc.edu/hdf4.html) so that you
are assured that the versions are compatible.

You will need to Unpack, configure, build, test, and install each
software component as described above.

1.  **szip**: Download, unpack, configure, build, test, and install the
    szip package.

2.  **zlib**: Download, unpack, configure, build, test, and install the
    zlib package.

3.  **jpeg**: Download, unpack, configure, build, test, and install the
    jpeg package.
    Once it's installed you will need to perform the following
    additional steps:
    1.  Copy the jpeg library to */usr/local/lib* with the command:
        <font style="font-family:courier">    sudo cp libjpeg.a
        /usr/local/lib</font>
    2.  Update the table of contents of archive libraries (to add the
        new library) with the command:
        <font style="font-family:courier">     sudo ranlib
        /usr/local/lib/libjpeg.a </font>
    3.  Copy the required include files to */usr/local/include* with the
        command:
        <font style="font-family:courier">    sudo cp jerror.h jpeglib.h
        jconfig.h jmorecfg.h /usr/local/include</font>

4.  **HDF4**: After downloading and unpacking the HDF4 software do the
    following:
    1.  Configure it using the command:
        <font style="font-family:courier">    ./configure
        --prefix=/usr/local --disable-fortran</font>
    2.  Build it using the command:
        <font style="font-family:courier">    make</font>
    3.  Test it using the command:
        <font style="font-family:courier">    make check</font>
    4.  Install it using the command:
        <font style="font-family:courier">    sudo make install</font>

## [NetCDF](http://www.unidata.ucar.edu/software/netcdf/)

In order to build and use the
[netcdf_handler](http://scm.opendap.org:8090/svn/trunk/netcdf_handler/)
package used by the BES you will need to download and install the NetCDF
libraries from our friends at [Unidata NetCDF
project](http://www.unidata.ucar.edu/software/netcdf/)

***Minimum***: NetCDF 3.6.0-p1

***Recommended***: NetCDF 3.6.2

## [HDF5](http://hdf.ncsa.uiuc.edu/HDF5/)

In order to build and use the
[hdf5_handler](http://scm.opendap.org:8090/svn/trunk/hdf5_handler/)
package used by the BES you will need to download and install the HDF5
libraries from the [HDF5 project](http://hdf.ncsa.uiuc.edu/HDF5/).

***Minimum***: HDF5 1.6.5

***Recommended***: HDF5 1.6.5

If you have **already** installed the HDF4 package then the installation
is straight forward.

Otherwise you will need to:

1.  **szip**: Download, unpack, configure, build, test, and install the
    szip package.

2.  **zlib**: Download, unpack, configure, build, test, and install the
    zlib package.

3.  **HDF5**: Download, unpack, configure, build, test, and install the
    HDF 5 package.


## [GNU tar](http://www.gnu.org/software/tar/)

You may or may not find that there are issues with the *tar* program
supplied with OS-X. IF you suspect that there is a problem go get the
latest from GNU: <http://www.gnu.org/software/tar/> build and install
it. (Make sure it's on your path before the old one!!)