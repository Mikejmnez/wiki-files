[\<\< back to HowTo Guides](HowTo_guides "wikilink")

## How to configure SUSE for the production of RPM binaries

This describes how to set up a vanilla SUSE computer so that we can
build RPM binaries for the Hyrax data server. This was taken from notes
describing how I configured a machine with a fresh install of SUSE
12.1to build Hyrax. If you're installing this on a *virtual* host using
VMware, see the section at the end of this document for some tips on
making that VM run smoothly.

*This assumes that the SUSE operating system install included the
software development packages*

## SUSE versions

These instructions have been amended to cover:

- SUSE-12.1 (64 bit)

## Initial configuration

Set up general build stuff that you need:

- *zypper install java-1_6_0-openjdk-devel*
- *zypper install ant*
- *zypper install ant-junit*
- *zypper install junit*
- *zypper install gcc-c++*
- *zypper install curl-devel*
- *zypper install libxml2-devel*
- *zypper install libjpeg-devel*
- *zypper install libicu-devel*
- *zypper install libcfitsio-devel*
- *zypper install libcppunit-devel*

### Autotools

- download the latest versions of autoconf, automake and libtool
- ./configure, make, make install

#### RPM Construction Tools

- *zypper install rpm-devel*
- *zypper install devel_rpm_build* [This
  failed.](ConfigureSUSE#RPM_Build_Tools_Issues "wikilink") But since we
  eren't planning on building RPMs from this VM I moved on.

### Data type dependencies: NetCDF, HDF4, HDF5

- *zypper install libnetcdf4 libnetcdf-devel*
- *zypper install libzip-devel*

#### szip

On SUSE 12.1 zipper does not have szip. I tried building szip from
source and ran into these issues:

1.  The configure found the math libs, but the examples failed to
    utilize the *-lm* flag in the link line. I edited
    szip-2.1/examples/Makefile.am and added the *-lm* flag to each of
    the two link lines. This allowed the code to compile.
2.  Once built, this failed to link with hdf4

**Solution**: Omit szip.

#### hdf4

Built from source using hdf-4.2.6.

**Note**: In a fresh *shrew* checkout, copies of the some of the more
difficult to obtain dependencies can be found in
*\$prefix/src/dependencies*. In this directory the original tar files
for software packages like hdf4, netcdf4, etc., can be found (look in
the *downloads* subdirectory) along with a script (*depends_builder*)
that will unpack and build all of these things in *downloads*. However,
while this is potentially very useful, it should be used only after
other sources for these packages have been exhausted. Look for RPM and
deb packages first, for example. If you do build the dependencies from
the source code here, edit the depends_builder script so that you
compile only the things you really need. For example, on SUSE linux,
it's very hard to find a RPM for HDF4, and we had to build that from
source. However, we skipped *szip* (see the note above) and used the
*jpeg* that was bundled with the SUSE 12.1 distribution. To do this, we
had to read and edit the part of the *depends_builder* script that
configures the hdf4 code.

Configuration
./configure --disable-fortran --enable-production --enable-shared
--disable-netcdf --prefix=/home/dev/OPeNDAP/hyrax-1.8/deps/hdf-4.2.6

<!-- -->

Build
make

<!-- -->

Test
make check [\*\*](ConfigureSUSE#HDF4_Build_Issues "wikilink")

## Hyrax

I checked out Hyarx 1.8 from the release branch and was able to build
and install Hyrax with the hdf4_handler.

## VMware configuration tips

It's best if you enable a shared directory so that you can pass stuff
back and forth between the host OS and the Guest OS (i.e., the Virtual
machine). That will only work if you have the *VMware tools* installed
in/on the guest.

- Install VMware-tools. This process varies, but it's pretty easy for
  all platforms. On VMware Fusion, look under the *Virtual Machine* menu
  for the item that says *Install VMware Tools*. This will download lump
  of code and, for CentOS, mount it on '/media/VMware Tools' (yes,
  there's a space in the directory name). Copy the \*.tar.gz file to
  some place like your home directory, unpack it and read the INSTALL
  file. For an initial installation, the typical process is to run
  *vmware-install.pl*.
- Under the configuration/options menu (the little wrench thing in
  Fusion), choose *Sharing*. Make a folder with an obvious name (e.g.,
  *vmware*) and turn sharing on. Now, anything you put in there when on
  either the OS will be available to the other OS. On linux, this
  directory is located at */mnt/hgfs/\<<name>\>*.

## problems not addressed

- There no JUnit for the OLFS build
- There's not graphviz so the *doc* targets for libdap and bes will
  fail; I tried adding graphviz but that did not fix the problem.

### HDF4 Build Issues

The HDF4 build was unable to detect the szip libraries until I specified
them as using the
<font size="2">`--with-szip=include_dir,library_dir`</font> pattern in
the configure line:


<font size="2">`./configure --disable-fortran --enable-production --enable-shared --disable-netcdf --prefix=/home/dev/OPeNDAP/shrew/deps/hdf-4.2.6 --with-szlib=/home/dev/OPeNDAP/shrew/deps/szip-2.1/include,/home/dev/OPeNDAP/shrew/deps/szip-2.1/lib64`</font>

Running <font size="2">`make check`</font> fails. These are the errors:
<font size="2">

    Testing  -- Multi-File Generic Raster Image Interface (mfgr)
    *** UNEXPECTED RETURN from GRsetcompress is -1 at line  135 in tszip.c
    *** UNEXPECTED RETURN from GRsetcompress is -1 at line  302 in tszip.c
    *** UNEXPECTED RETURN from GRsetcompress is -1 at line  469 in tszip.c
    *** UNEXPECTED RETURN from GRsetcompress is -1 at line  638 in tszip.c
    *** UNEXPECTED RETURN from GRsetcompress is -1 at line  807 in tszip.c
    *** UNEXPECTED RETURN from GRsetchunk is -1 at line 1001 in tszip.c

</font>

Based on that I decided to try again without the SZIP library:


<font size="2">`./configure --disable-fortran --enable-production --enable-shared --disable-netcdf --prefix=/home/dev/OPeNDAP/shrew/deps/hdf-4.2.6`</font>

Again, <font size="2">`make check`</font> failed. The errors:
<font size="2">

    Testing getting location info of data (tdatainfo.c)                    PASSED
    Testing getting location info of attr and annot data (tattdatainfo.c) *** glibc detected *** /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/test/.libs/hdftest: free(): invalid pointer: 0x0000000000659b10 ***
    ======= Backtrace: =========
    /lib64/libc.so.6(+0x74c06)[0x7ff6d491fc06]
    /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/libsrc/.libs/libmfhdf.so.0(SDgetoldattdatainfo+0x47a)[0x7ff6d53672fa]
    /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/test/.libs/hdftest[0x421661]
    /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/test/.libs/hdftest[0x423c27]
    /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/test/.libs/hdftest[0x4067e5]
    /lib64/libc.so.6(__libc_start_main+0xed)[0x7ff6d48cc23d]
    /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/test/.libs/hdftest[0x408d29]
    ======= Memory map: ========
    00400000-00436000 r-xp 00000000 08:02 539133                             /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/test/.libs/hdftest
    00635000-00636000 r--p 00035000 08:02 539133                             /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/test/.libs/hdftest
    00636000-00637000 rw-p 00036000 08:02 539133                             /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/test/.libs/hdftest
    00637000-0067a000 rw-p 00000000 00:00 0                                  [heap]
    7ff6d4695000-7ff6d46aa000 r-xp 00000000 08:02 1682                       /lib64/libgcc_s.so.1
    7ff6d46aa000-7ff6d48a9000 ---p 00015000 08:02 1682                       /lib64/libgcc_s.so.1
    7ff6d48a9000-7ff6d48aa000 r--p 00014000 08:02 1682                       /lib64/libgcc_s.so.1
    7ff6d48aa000-7ff6d48ab000 rw-p 00015000 08:02 1682                       /lib64/libgcc_s.so.1
    7ff6d48ab000-7ff6d4a30000 r-xp 00000000 08:02 1684                       /lib64/libc-2.14.1.so
    7ff6d4a30000-7ff6d4c30000 ---p 00185000 08:02 1684                       /lib64/libc-2.14.1.so
    7ff6d4c30000-7ff6d4c34000 r--p 00185000 08:02 1684                       /lib64/libc-2.14.1.so
    7ff6d4c34000-7ff6d4c35000 rw-p 00189000 08:02 1684                       /lib64/libc-2.14.1.so
    7ff6d4c35000-7ff6d4c3a000 rw-p 00000000 00:00 0
    7ff6d4c3a000-7ff6d4c51000 r-xp 00000000 08:02 1497                       /lib64/libz.so.1.2.5
    7ff6d4c51000-7ff6d4e50000 ---p 00017000 08:02 1497                       /lib64/libz.so.1.2.5
    7ff6d4e50000-7ff6d4e51000 r--p 00016000 08:02 1497                       /lib64/libz.so.1.2.5
    7ff6d4e51000-7ff6d4e52000 rw-p 00017000 08:02 1497                       /lib64/libz.so.1.2.5
    7ff6d4e52000-7ff6d4e8d000 r-xp 00000000 08:02 659745                     /usr/lib64/libjpeg.so.62.0.0
    7ff6d4e8d000-7ff6d508c000 ---p 0003b000 08:02 659745                     /usr/lib64/libjpeg.so.62.0.0
    7ff6d508c000-7ff6d508d000 r--p 0003a000 08:02 659745                     /usr/lib64/libjpeg.so.62.0.0
    7ff6d508d000-7ff6d508e000 rw-p 0003b000 08:02 659745                     /usr/lib64/libjpeg.so.62.0.0
    7ff6d508e000-7ff6d509e000 rw-p 00000000 00:00 0
    7ff6d509e000-7ff6d5122000 r-xp 00000000 08:02 412473                     /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/hdf/src/.libs/libdf.so.0.0.0
    7ff6d5122000-7ff6d5322000 ---p 00084000 08:02 412473                     /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/hdf/src/.libs/libdf.so.0.0.0
    7ff6d5322000-7ff6d5324000 r--p 00084000 08:02 412473                     /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/hdf/src/.libs/libdf.so.0.0.0
    7ff6d5324000-7ff6d5326000 rw-p 00086000 08:02 412473                     /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/hdf/src/.libs/libdf.so.0.0.0
    7ff6d5326000-7ff6d534f000 rw-p 00000000 00:00 0
    7ff6d534f000-7ff6d5376000 r-xp 00000000 08:02 539086                     /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/libsrc/.libs/libmfhdf.so.0.0.0
    7ff6d5376000-7ff6d5575000 ---p 00027000 08:02 539086                     /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/libsrc/.libs/libmfhdf.so.0.0.0
    7ff6d5575000-7ff6d5576000 r--p 00026000 08:02 539086                     /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/libsrc/.libs/libmfhdf.so.0.0.0
    7ff6d5576000-7ff6d5577000 rw-p 00027000 08:02 539086                     /home/dev/OPeNDAP/shrew/src/dependencies/src/hdf-4.2.6/mfhdf/libsrc/.libs/libmfhdf.so.0.0.0
    7ff6d5577000-7ff6d5578000 rw-p 00000000 00:00 0
    7ff6d5578000-7ff6d5598000 r-xp 00000000 08:02 1555                       /lib64/ld-2.14.1.so
    7ff6d5774000-7ff6d5778000 rw-p 00000000 00:00 0
    7ff6d5793000-7ff6d5798000 rw-p 00000000 00:00 0
    7ff6d5798000-7ff6d5799000 r--p 00020000 08:02 1555                       /lib64/ld-2.14.1.so
    7ff6d5799000-7ff6d579a000 rw-p 00021000 08:02 1555                       /lib64/ld-2.14.1.so
    7ff6d579a000-7ff6d579b000 rw-p 00000000 00:00 0
    7fffa470d000-7fffa472e000 rw-p 00000000 00:00 0                          [stack]
    7fffa47ff000-7fffa4800000 r-xp 00000000 00:00 0                          [vdso]
    ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0                  [vsyscall]
    make[2]: *** [check] Aborted

</font>

All other tests passed. I chose to install and continue.

### RPM Build Tools Issues

The command \* *zypper install devel_rpm_build* failed, even though
zipper search found the package: <font size="2">

    dev@linux-9nfp:~/OPeNDAP/shrew>
    dev@linux-9nfp:~/OPeNDAP/shrew> zypper search !$
    zypper search devel_rpm_build
    Loading repository data...
    Reading installed packages...

    S | Name                              | Summary                                  | Type
    --+-----------------------------------+------------------------------------------+--------
      | devel_rpm_build                   | RPM Build Environment                    | pattern
      | patterns-openSUSE-devel_rpm_build | Meta package for pattern devel_rpm_build | package
    dev@linux-9nfp:~/OPeNDAP/shrew>
    dev@linux-9nfp:~/OPeNDAP/shrew>
    dev@linux-9nfp:~/OPeNDAP/shrew>
    dev@linux-9nfp:~/OPeNDAP/shrew> zypper info devel_rpm_build
    Loading repository data...
    Reading installed packages...


    package 'devel_rpm_build' not found.
    dev@linux-9nfp:~/OPeNDAP/shrew>
    dev@linux-9nfp:~/OPeNDAP/shrew>
    dev@linux-9nfp:~/OPeNDAP/shrew>
    dev@linux-9nfp:~/OPeNDAP/shrew> zypper info patterns-openSUSE-devel_rpm_build
    Loading repository data...
    Reading installed packages...


    Information for package patterns-openSUSE-devel_rpm_build:

    Repository: openSUSE-12.1-12.1-1.4
    Name: patterns-openSUSE-devel_rpm_build
    Version: 12.1-25.21.1
    Arch: x86_64
    Vendor: openSUSE
    Installed: No
    Status: not installed
    Installed Size: 1.0 KiB
    Summary: Meta package for pattern devel_rpm_build
    Description:
    This package is installed if a pattern is selected to have a working update path
    dev@linux-9nfp:~/OPeNDAP/shrew>

</font>