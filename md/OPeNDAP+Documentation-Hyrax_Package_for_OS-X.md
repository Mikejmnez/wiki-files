## Overview

The goal of this build was to produce a set of OS-X package installers
for Hyrax. This includes bundling all of the Hyrax dependencies in an
installer. In addition, all of the installed files (Hyrax and
dependencies) should be confined to a single directory (tree) for the
release. For any given release I chose
*`/usr/local/opendap/servers/hyrax-`<version>*. This abbreviated is
throughout this section as **\$prefix**.

**Note:** This is based on Nathan's original detailed instructions for
building on OS/X so that a MetaPackage installer can be made for Hyrax.
I modified the *shrew* project for the Hyrax 1.6.2 release to do most of
this in a semi-automated way. See [Building Hyrax
Dependencies](BuildHyraxOnOSX "wikilink") for Nathan's original
narrative of the process required.

You can check out the shrew project at
[<https://scm.opendap.org/svn/trunk/shrew>](https://scm.opendap.org/svn/trunk/shrew).
We often do not use that but a copy of the shrew project on a branch.
This enables everyone to know when changes are going to be slated for
the next (trunk) or current (branch) release. The branch for a
particular set of releases can be found by browsing the SVN sources
using [Trac](https://scm.opendap.org/trac/browser). A typical example
is:
[<https://scm.opendap.org/trac/browser/branch/shrew/hyrax_1.7_release>](https://scm.opendap.org/trac/browser/branch/shrew/hyrax_1.7_release),
where the libdap, bes, et c., projects are referenced by the shrew
project.

## Build a Hyrax MetaPackage for OS/X Using Shrew

Based on the work that Nathan did, I've modified the **shrew** project
to accommodate most of this process. It's almost automatic...

### Preconditions

A completely up to date checkout

### Options

You can use these targets to build a set of dependencies that are then
used to build the various handlers and/or you can build a set of OS/X
packages that can be combined into a single, huge, metapackage. There
may be some utility in just doing the former, but this process has been
developed with the idea that it will be used to make an OS/X metapackage
for Hyrax.

### Set environment variables

You must set **\$prefix**, **\$PATH**, **\$TOMCAT_DIR** and
**\$CATALINA_HOME** for this all to work. There are several scripts that
will do this for you; you need to *source* these for them to work. In
the top-level shrew directory, *source spath.sh* or *source spath.sh
<prefix>* will set all of these (the former sets \$prefix to the current
working directory and the latter sets it to *<prefix>*). The *sptah.sh*
script is for the bash shell only. There are also two scripts in
shrew/src/dependencies/scripts, one for bash and one for csh, that set
these variables as well. Below we show you how to do this by hand...

#### \$prefix

Set **\$prefix** to the full path of the desired installation location.
This should be ***/usr/local/opendap/servers/hyrax-<version>***. We
chose this because we want to make sure several things are true:

- When the build is done, components of the server must be linked to the
  libraries they use and those same libraries must be in the same place
  when the server is run.
- Since OS/X's various installers don't have an uninstall option,
  putting the entire server in one place makes it easy to remove
- We're Unix geeks and we like things to be in /usr/local ;-)

#### \$PATH

Set **\$PATH** to ***\$prefix/bin:\$PATH***

#### \$TOMCAT_DIR

Set **\$TOMCAT_DIR** to ***\$prefix/apache-tomcat-6.0.29***


NB: Building the dependencies installs Tomcat for you.

We use the evironment variable *\$TOMCAT_DIR* instead of
*\$CATALINA_HOME* because the latter is actually used by tomcat. That
said, make sure that if you have a value set for CATALINA_HOME it is the
same as TOMCAT_DIR. If that's not the case, if CATALINA_HOME points to a
different copy of tomcat, when you run the start up script
(*shrew/apache-tomcat-6.0.29/bin/startup.sh*) it will start the copy of
tomcat that CATALINA_HOME points to and not the one in
*shrew/apache-tomcat-6.0.29*.

- In any case, make sure that the copy of tomcat you run actually has
  the **opendap.war** file in its *webapps* directory.
- If you're building a package/metapackage for a software distribution,
  make sure that the copy of the OLFS has a version number! To do that,
  build using *ant -DHYRAX_VERSION=1.6.2 -DOLFS_VERSION=1.7.1* for
  example. Note that the *shrew* Makefile does this for you if you have
  set the **\$(olfs_version_arg)** Makefile variable in *Makefile.am*
  correctly. That is, following this prescription, *make world* will
  build the OLFS, using the version numbers set in the *Makefile* and
  put the *opendap.war* file in tomcat's *webapps* directory.

### Edit the Makefile.am

The Makefile.am used by *shrew* must be generic. It has a number of
configuration values set for OS/X, but they are commented out since they
break the Linux builds. These are particularly important for this
package build because they are designed to help with configuring and
building the handlers to use a completely self-contained set of
dependencies. The **make depends-pkg** call below will build these and
install them in **\$prefix/deps**; these variables will ensure that the
handlers use those and not some other set of libraries.

Here's what the relevant part of the *Makefile.am* looks like in svn:

`olfs_version_arg = -DHYRAX_VERSION=1.6.2 -DOLFS_VERSION=Nightly.Build`
`prefix_arg = --prefix=$(prefix)`
`# Only use these if you need them and make sure to double check the values.                                                                `
`# If they are wrong, configure in the modules dir will fail and that means                                                                 `
`# other parts of the build will fail too.`<font  color="red">`                                                                                                  `
`#netcdf_arg = --with-netcdf=$(prefix)/deps/netcdf-3.6.3`
`#hdf4_arg = --with-hdf4=$(prefix)/deps/hdf-4.2.5 --with-hdfeos2=$(prefix)/deps/hdf-4.2.5 --enable-short-name`
`#hdf5_arg = --with-hdf5=$(prefix)/deps/hdf5-1.8.5-patch1`
`#icu_arg = --with-icu-prefix=$(prefix)/deps/icu-3.6`</font>
`# Users may change add the external lib variables                                                                                          `
`# to specify locations of the external libraries as needed.                                                                                 `
`#`
`release_params = $(prefix_arg) $(netcdf_arg) $(hdf4_arg) $(hdf5_arg) $(icu_arg) `
`# release_params = $(prefix_arg)                                                                                                           `
`#`
`debug_params = $(debug_flags) $(prefix_arg) $(netcdf_arg) $(hdf4_arg) $(hdf5_arg) $(icu_arg)`
`# debug_params = $(debug_flags) $(prefix_arg)                                                                                              `

Where the *\$(release_params)* or *\$(debug_params)* are used as the
value to *./configure* for each of the handlers. So, uncommenting the
lines shown in read will cause the handlers to look for the HDF4, HDF5,
NetCDF, etc., libraries in *\$(prefix)/deps* which was made by the
*depends-pkg* target (in fact, the *depends-install* target also builds
that directory).

### Build some code

To build the OS/X packages for the entire C/C++ part of the server, run
the following commands:

autoreconf --verbose --force --include: This rebuilds *configure* from *configure.ac* and *Makefile.in* from *Makefile.am*
./configure --prefix=\$prefix : This builds *Makefile* from *Makefile.in* using the value of **\$prefix** for the autoconf directory *prefix*
make depends-install : Build the dependencies, install them in **\$prefix** and then make an OSX package to be used later
make world : Make the server and install the bits and pieces in **\$prefix**. This makes sure that the modules all link against the correct versions of the libraries (but it depeends on having **\$PATH** set as described above.
make pkg : Given that the code is all built and installed (and this includes the OLFS), make OSX packages for all the pieces. The packages will be stored in the different directories, but that doesn't matter so long as you followed the advice above about starting out in you OSX home directory and not /usr/local/src, or some other place that OSX file chooser dialogs won't find. What you wind up with is an OS/X Package for each of: libdap, bes, and the handlers in a top-level directory called *osx-packages*. In a subsequent step these will be loaded into a Metapackage by hand, but there's more to do...
make depends-pkg : This step builds a single package that holds all of the dependencies, something not technically needed but a practical necessity for OS/X where things like netcdf4 and hdf5 are not standard parts of the OS. This package is left in the *<top level>/src/dependencies* directory.

### Make the MetaPackage

Installing all of the packages one-by-one is too complicated for most
mac users, even those who are going to run a server. To get around this
we combine all these packages in one giant blob called a *metapackage*.
To build the metapackage, you need to combine the discrete packages by
hand, using *PackageMaker*. The PackageMaker application is not hard to
use, but you must attend to a fair amount of detail to get a MetaPackage
that works correctly. Also, be wary of PackageMaker's tendency to crash
when making large things like the metapackage; save often.

#### Edit the OS/X Resources for the MetaPackage

Prepare the *.txt* files in *OSX_Resources* at the top level of the
shrew project:

- Edit *Introduction.txt*. This is probably the most important thing to
  edit since it's the first thing the installer shows a user.
- Edit *ReadMe.txt*.
- Don't edit *License.txt*

#### Open PackageMaker and build the metapackage by hand

Start PackageMaker and set the *Organization* to **org.opendap** and the
*Minimum Target* to **10.5**.
![](Basic_PM_Window.png "Basic_PM_Window.png")

#### Configure the installer UI

In the upper right corner, click *Edit Interface*. This is where you set
the installer's background image, license file, introductory text, etc.
These are the files in *OSX_Resources* that were edited in the steps
before starting PackageMaker. Click through the panes of the installer's
interface and set the Welcome text to *Introduction.txt*, Read Me text
to *ReadMe.txt an License to*License.txt''.
![](Edit_interface.jpg "Edit_interface.jpg")

#### Add and configure the dependencies

Now add the *dependencies* package as the first 'child' package of this
metapackage. Use drag and drop from a Finder window to copy the package
into PackageMaker's window for the new metapackage.
![](Dependencies_first.png "Dependencies_first.png")

Make sure to set the options for the child package options so that the
metapackage does not use any resources present in the child package
![](Do_not_use_res.png "Do_not_use_res.png") Also make sure to check
that the paths used when this child package is installed are correct
(e.g., may sure the child package won't install into your */Users/...*
directory!). ![](Check_paths.png "Check_paths.png") Lastly, and very
important, make sure the child package won't trigger a restart - this is
very tiring and is not necessary for Hyrax.
![](Restart_none.png "Restart_none.png")

#### Now add the Hyrax packages

Now add the Hyrax packages: libdap, bes, handlers and olfs. *In that
order*. Make sure to set all of the options for *each* package (not to
use the child package's resources, not to require a restart and to check
the install paths) using the same values as were set for the
dependencies package.

------------------------------------------------------------------------

### Notes

These things would complete the hyrax configuration as part of the
installation process:

1.  If possible we should ask for user and group names in the install
    process.
2.  We should create the logs directory as part of the install and
    configure the bes to use it.
3.  Notes on the process for [building Hyrax on
    OS/X](BuildHyraxOnOSX "wikilink") that has now been mostly codified
    in the *shrew* project's Makefile.am and related scripts and/or here
    (for the parts regarding building the OS/X MetaPackage).