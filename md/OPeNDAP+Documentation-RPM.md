[\<\< back to Developer Info](Developer_Info "wikilink")

## About Building RPMs

Once a [release is made](ReleaseGuide "wikilink"), it's time to make the
binaries.

The page on [Building Hyrax rom
GitHub](Hyrax_GitHub_Source_Build "wikilink") has good information on
setting up a CentOS 6 machine for source and binary builds - the key is
to use *yum* to install the rpm packages needed to make rpm packages ;-)


**`yum install rpm-devel rpm-build redhat-rpm-config`**

## About RPMs

Documentation for RPM and rpmbuild, in particular is hard to come by.
Here's some good information: [Fedora RPM
Guidelines](http://fedoraproject.org/wiki/Packaging/Guidelines)

# Building RPMs for use with EPEL

There are some tricks to building the RPMs, but for the most part, its
just *make rpm*. However, you can make this easier if you do two things:

1.  Install the EPEL *yum* repository. Get
    [epel-release-6-8.noarch.rpm](http://mirror.pnl.gov/epel/6/i386/epel-release-6-8.noarch.rpm)
    and install it using *sudo yum install epel-release-6-8.noarch.rpm*.
    Then install packages needed to read various file formats: *yum
    install netcdf-devel hdf-devel hdf5-devel libicu-devel
    cfitsio-devel*
2.  Set up the *rpmbuild* directory structure in \$HOME and then build
    as yourself and not root. (Here are [instructions for
    CentOS](http://wiki.centos.org/HowTos/SetupRpmBuildEnvironment))

build the libdap rpms: *cd libdap4*, *make rpm*
install (temporarily) the libdap rpm packages: You need to do this because *libdap* and *libdap-devel* are dependencies for the BES rpms and their builds. Use *sudo yum install ~/rpmbuild/RPMS/x86_64/libdap\*.rpm*
build the bes rpms: *cd bes*, *make rpm*
test the bes rpms (if you want): *sudo yum install ~/rpmbuild/RPMS/x86_64/bes.rpm*, then run *besctl start* and *bescmdln*. Type *show version;* in to the latter, you should get an xml document that lists the BES's components. Stop the server with *besctl stop*
remove the packages you just installed:Do this step if you're on a build host an you don't want libdap, etc., permanently installed. *sudo yum remove bes bes-devel libdap libdap-devel*

# Building RPMs that have Statically Linked Handlers

Build RPMs that are usable for NASA takes a bit more work because NASA
uses hdf4/hdfeos2 for lots of its data and there is no rpm for hdfeos2.
As a result, you will need to build a version of the hdf4 handler that
is statically linked to the hdfeps2 and hdf4 libraries. Not a huge
problem, but it's an extra step. However, there is a bonus for doing
this extra work: You also get the handlers that build GeoTIFF and
JPEG2000 responses, an updated FITS handler and a handler that uses GDAL
to read any of the formats it supports, including data stored in GeoTIFF
files.


Note: There are actually two different 'static' RPM build options for
the BES, and here I describe the one that builds *every* handler using
static linking so that EPEL is not required at all. Read the Makefiles,
and spec files to find out more. The 'it uses EPEL' and 'it uses static
linking' are the two most useful options.

There's considerable overlap with the instructions from the above
section, but there are key differences for the BES parts, highlighted in
red below.

Build the dependencies for the BES handlers that have no (usable) RPM in CentOS 6: *cd hyrax-dependencies*, *make -j9 <font color="red">for-static-rpm*</font>
Build the libdap rpms: *cd libdap4*; *make rpm*
install (temporarily) the libdap rpm packages: You need to do this because *libdap* and *libdap-devel* are dependencies for the BES rpms and their builds. Use the command
*sudo yum install ~/rpmbuild/RPMS/x86_64/libdap\*.rpm*

build the bes rpms
*cd bes*; *make <font color="red">all-static-rpm</font>*

test the bes rpms (if you want)
Install BES


*sudo yum install ~/rpmbuild/RPMS/x86_64/bes\*.rpm*

Start BES


*besctl start*

Run client


*bescmdln* (Type *show version;* in to the bescmdln client, you should
get an xml document that lists the BES's components.)

Stop BES


*besctl stop*

remove the packages you just installed
Do this step if you're on a build host and you don't want libdap, etc.,
permanently installed.

*sudo yum remove bes bes-devel libdap libdap-devel*