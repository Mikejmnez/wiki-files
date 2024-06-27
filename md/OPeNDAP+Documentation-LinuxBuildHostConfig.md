[\<\< back to HowTo Guides](HowTo_guides "wikilink")

## Configuring a Linux host for a Hyrax build

Starting with a completely clean CentOS 5.3 64-bit linux machine, here's
what I did to so that the *shrew* project will build. This assumes that
the target machine is going to use only 'special' builds where
absolutely needed and rely on *yum* as much as possible.

## sudo

Edit /etc/sudoers so that the wheel group is allowed to use sudo.

Then edi /etc/group so that the build account ('opendap') is in the
wheel group.

## Update the autotools

For this you cannot use rpm. Google autoconf, get the latest,
./configure and make install. You must be root to do 'make install'. Put
it in the default location.

Repeat for automake and libtool.

## Yum

I used *yum* to install packages. Because CentOS does not include
*extra* packages (like netcdf), the 'extras' repository has to be added
to the base configuration for yum. To do that, find EPEL on the wen and
get the information for the *EL5* repo. The information to be added to
yum should be in a single *rpm* file; download and install it:

<font size="+">`rpm -i epel-release-5.4.noarch.rpm`</font>

Can be retrieved from <http://mirror.seas.harvard.edu/epel/5/> and then
choose i386 or x86_64

### Add packages for the hyrax dependencies

The yum commands *yum search <string>* and *sudo yum install <package>*
are most useful. I installed:

1.  readline-devel (needed by bes)
2.  bzip2-devel (needed by bes)
3.  netcdf-devel
4.  hdf-devel (this is hdf 4)
5.  hdf5-devel
6.  libicu-devel
7.  java-1.6.0-openjdk (this gets CentOS java 1.6) and the -devel
    package
8.  ant
9.  ant-junit, junit
10. subversion (not 'svn'; this gets me every time)
11. graphviz (needed for the *doc* targets but I've removed these from
    the build because *dot* seems to be broken in CentOS for us since it
    cannot make 'png' files)

Yum might work to update *autoconf*, *automake* and *libtool*, but I did
these by hand and installed them in /usr/local/{bin,lib,...}. Google
them.

## Building

Use *svn* to checkout shrew.

Edit the first few lines of the *nbuild_shrew.sh* script. This script
runs in the Bourne shell and sets the few environment variables needed
for the build.

Build time on a 4-core machine: about 42 minutes, including upload of
the build results to the build database. Time to build the *daemon*
target: about 9 minutes. (of course, java people will gloat because the
time to build the OLFS is only seconds).

## Issues

I did not test the OLFS junit tests using the nbuild_shrew script
because of a configuration goof. I think it'll work...