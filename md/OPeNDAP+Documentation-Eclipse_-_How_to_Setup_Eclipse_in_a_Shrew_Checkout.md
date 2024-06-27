# Eclipse as an IDE for Shew

It can be useful to use an IDE when debugging and developing for Hyrax
due to the graphical interface to gdb as well as the extensive searching
and code indexing features that Eclipse provides. This document will go
through the steps for getting Eclipse set up pointing into a Shrew
checkout. We also explain how we set up debug configurations for
debugging modules using the besstandalone program.

Note: This was originally developed for Hyrax 1.6. It's likely still
correct, but if you run into problems, do not hesitate to contact
support at \<support at opendap.org\>

## Downloading Eclipse

Eclipse may be found at <http://eclipse.org/downloads/>

Download the Eclipse for C/C++ Development. You can get other bundles
and add the CDT features, but it is more work than getting the pre-built
C++ version.

If you also plan to do Java development, you can add these bundles in
later using the Software Update.

## A new approach to working with shrew in Eclipse

This was tested with Eclipse versions starting with Indigo and it might
work with older versions.

Overview:

1.  Use Eclipse to checkout shrew
2.  In a shell build the code by hand, nominally with the various debug
    options set
3.  Set the shrew project in Eclipse so that it uses the correct
    value(s) for \$PATH

### Checkout Shrew

In order to get Eclipse to grok that all the stuff in shrew is a single
project, its SVN client has to do the check out. Start Eclipse and open
the *SVN Repository Exploring* perspective. Navigate to the copy of
shrew you'd like to checkout. (NB: if you want to work on a branch, make
that first, with the correct values recording both in
*shrew/externals.txt* and as shrew's *<svn:externals>* property and then
do the check out.) From this perspective you can look at the properties
(e.g., <svn:external>) and individual files without actually checking
out the project.

Check out the project.

Wait. It takes some time, especially on a slow network.

### Build the code

This is described below in some detail. The short version (which is a
bit different than what's describe below because some things have been
made simpler) is:

- Copy *nbuild_info.sh.in* to *nbuild_info.sh* and edit that so it makes
  sense. You probably want something like:

``` bash
USER_PW=
os_name="linux"
make_rpm="no"
make_pkg="no"
slots=9

do_the_build="yes"
process_the_logs="yes"
record_build=""

logs_archive="logs"
```

- Run *source spath.sh* (this will set the environment variables
  *prefix* and *PATH*)
- Run *autoreconf --force --install --verbose* (this will build the
  initial configure script)
- Run *./configure --prefix=\$prefix* (this will make the top-level
  Makefile)
- Run *./nbuild_shrew.sh* (this will build all of the component
  projects, in the correct order).

NB: You may need to goto the directory \$prefix/src/dependencies and run
the Makefile there before running *nbuild_shrew.sh*

### Make it a CDT project

If the project icon displayed in Eclipse does not have a *C* on it, it's
not a CDT project. Make it one by selecting the project and then using
*<File:New:Convert> to C/C++...*

### Set the environment variables in the project

To make it so that you can run tests from within Eclipse, you need to
set it to use the correct values for PATH for/with you newly checked-out
project.

Goto set the value of the project's PATH environment variable to be
*\$prefix/bin:/usr/local/bin:\$PATH*. To get at PATH, goto
*Properties:C/C++ Build:Environment* where *Properties* for the project
(not all of Eclipse) is found by right-clicking on the project name in
the C/C++ Projects view or at the bottom of the *Project* menu. A dialog
box comes up; choose *C/C++ Build:Environment* in the left pane, then
click on PATH and Edit it.

## Old shrew/Eclipse instructions follow

## Creating a Workspace In A Shrew Directory

You need to have a shrew project checked out and built in debug mode
using the standard approach. The first-time debug build is *required*
after a fresh checkout in order to get autoconf and configure to run
properly and set up the Makefile for all the projects. Eclipse will then
piggyback on these.

If you do not have this done already, please follow the steps in the
next section "Setting Up a Debug Shrew Project"

### Setting Up a Debug Shrew Project

Go to the directory you want the shrew project and do a checkout. For
example, to get the current trunk revision of *shrew*:

`svn co `[`https://scm.opendap.org/svn/trunk/shrew`](https://scm.opendap.org/svn/trunk/shrew)

Note that *shrew* uses svn externals, so make sure you know which
versions you are getting so when you commit you are commiting to the
right place! Also note that the *trunk/shrew* checkout is what we use
for our current development. If you want software closer to 'released'
revisions, checkout *branch/shrew/hyrax_x.y_release* where *x.y* is
something like *1.8*. You can browse around on
<https://scm.opendap.org/trac/> to see what's there.

To check the externals being used, get the property in the shrew
directory you checked out:

`svn propget `[`svn:externals`](svn:externals)

which for the trunk shrew returns:

    aries@chinchilla$ svn propget svn:externals
    ^/trunk/libdap src/libdap
    ^/trunk/bes src/bes
    ^/trunk/dap-server src/modules/dap-server
    ^/trunk/freeform_handler src/modules/freeform_handler
    ^/trunk/fileout_netcdf src/modules/fileout_netcdf
    ^/trunk/netcdf_handler src/modules/netcdf_handler
    ^/trunk/hdf4_handler src/modules/hdf4_handler
    ^/trunk/hdf5_handler src/modules/hdf5_handler
    ^/trunk/ncml_module src/modules/ncml_module
    ^/trunk/wcs_gateway_module src/modules/wcs_gateway_module
    ^/trunk/wcs_module src/modules/wcs_module
    ^/trunk/olfs src/olfs

#### Configuring a Debug Build

Since you likely plan to use the shrew project for debugging in Eclipse,
you'll want to run the build_hyrax_debug script in order to make sure
the configuration for all the subprojects is set up to perform debug
builds (i.e. no optimization and full symbol tables). That way Eclipse
will be able to find breakpoints and let you view variables in a running
debug.

In a bash terminal in the top-level shrew directory, type:

`source spath.sh`
`source build_hyrax_debug`

This will run autoconf on everything, then configure, then build it. If
there are any missing dependencies, make sure to get them until
everything builds. This will ensure that all the subprojects are
configured properly so that the Eclipse Build command, which simply
calls 'make', will succeed in compiling a debug version of the specified
project.

> **NOTE**: one source of confusion with Eclipse is that when it starts
> from a window manager (like Gnome or OS/X's Finder) the value of
> *\$PATH* will not have *\$prefix/bin* prepended. This means that tests
> for the *check* targets in the Makefiles won't work from Eclipse. They
> will, however, work from the shell whee you sources *spath.sh* and
> *build_hyrax_debug* because those scripts do that for you.

### Pointing the Workspace at Shrew Using Existing Makefiles

> **NOTE**: It is important that you have run autoconf and configure in
> the shrew directory tree before you start using Eclipse. The simplest
> way to do this for a debug environment is to source the *spath.sh* and
> *build_hyrax_debug* files in the top-level shrew directory as
> mentioned above.

Now we will set up projects for the things we expect to use. In our
case, we have a separate project for libdap, bes, and the ncml_module.
We have tried to create a top-level shrew project in the workspace, but
due to the way the Makefile is set up and the manner in which Eclipse
finds source files when debugging, having a shrew project plus a module
project will cause headaches. It's better to just create one per item
you plan to debug.

> **NOTE**: You cannot expect a breakpoint to work unless it is in a
> project in the workspace! Eclipse is smart enough to find source
> matching the symbol table in the case of stepping (sometimes), but
> don't be surprised if a breakpoint you set in, say, the hdf4_handler,
> doesn't go off (though you expect it should) unless you explicitly add
> the hdf4_handler project using the section below on creating projects
> for modules.

First, however, we need a new workspace to put everything into...

#### Create a New Workspace

First, create a new workspace if you have an existing one. You can use:

File \> Switch Workspace \> Other...

and just create a new directory for the workspace to make the new one.

#### New Project for Libdap

If you plan to modify the BES or libdap, or even if you want to be able
to set breakpoints and step through code for these in the debugger, you
must make a project for each of these. We'll use libdap as the example,
but the bes can be done in the same way.

**NOTE**: it's important to get the settings right so that Eclipse
doesn't overwrite your Makefile's!

- Select File \> New C++ Project
- Give it a name: "libdap" (this doesn't need to match the directory
  structure... it could be "libdap trunk" or "libdap 1.6 release" etc.
- Uncheck "Use Default Location"
- Browse for the libdap directory and choose it. This should be in
  \$shrew/src/libdap (where \$shrew is the top-level shrew dir).
- Under "Project Type..." select "Makefile Project"
- Make sure to select "Empty Project" under the "Makefile Project"
  treeview to tell Eclipse to use existing Makefile's.
- Tell it the toolchain to use if there's a choice: for Mac it's "MaxOSX
  GCC".
- Select the "Finish" button.

A new project should be in the project view.

Now we need to tell it the make command since libdap must be installed
for it to work in shrew.

- Select the new libdap project.
- Select "Project \> Properties"
- Select the "C/C++ Build" in the treeview on the left
- Under "Builder" pane, the uncheck "Use default build command" (which
  is make).
- This ungrays the make command: enter "make install" for the command.

Now when you select the libdap project and then hit the Build command
for it, it will compile and then install it into the shrew location
properly.

#### New Project for the BES

Go through the same steps as for libdap, except just choose "bes" for
the name and project location. Also change its build command to "make
install".

#### New Project for a Module

In this section, we'll set up the subproject for a particular module
you'll want to develop or debug. I will use the ncml_module as the
example since this is the one I normally develop and I have added some
tools to the project to make setting up debugging files easier.

Create the project just like the shrew project, using an Empty Project
in the Makefile Project tab. Set the location to be the subdirectory of
the top-level shrew, e.g.

src/modules/ncml_module

This will create a separate project tab for the module. If you click on
this project and press the Build button (with the hammer icon), just
that project will be built.

##### Set the Include Path

Setting the include path in the workspace will avoid the "can't find
include" problems in the IDE. The Makefile may have the includes, but
Eclipse doesn't know where to find them.

Under the Project -\> Properties dialog (making sure to have the module
project selected):

- Click the "C/C++ General" tree to open it.
- Select "Paths and Symbols"
- On the "Includes" tab, add libdap and bes to C++ language include
  paths:
  - Click "Add..."
  - Select all checkmarks: "Add to all configurations", "Add to all
    languages" and "Is a workspace path".
  - Hit the "Workspace..." button and browse to the "shrew/src/libdap"
    and add it. It should show up as "/shrew/src/libdap" since we
    selected "is a workspace path"
  - Do the same for the bes include dir.

## Debugging a Module

In order to debug a given module, we need to set up a Debug
configuration for it. The way I do this for the ncml_module is by using
the **besstandalone** application and pointing it at the same generated
*bes.conf* file that the Autotest testsuite uses (all the modules should
have this structure at this point). This makes sure the required modules
get loaded, and in particular, the testsuite *bes.conf* points at the
locally compiled module shared object in the project *.libs* directory,
so 'make install' isn't required. We will also need a BES XML command
file to automatically run the command we want to debug. For the
ncml_module, this means we load a given NcML file and ask it for a
particular response type (DAS, DDS, DDX, DODS) and perhaps with a
constraint. Then we need to make sure Eclipse knows where to find the
dynamically loaded library. Let's go through these steps one by one.

### Create a New Debug Configuration

Go to:

Run \> Debug Configurations...

When the dialog pops up, select the "C/C++ Application" entry and "New"
to create a new configuration. This should pop up the configuration
options for the new Debug Configuration.

We'll want to make changes in several of the tabs.

#### Main Tab: Select **besstandalone** as the application to run

Now we want to set these options to run **besstandalone** with the
arguments we want. To do this:

- Select the "Browse..." button under C/C++ Application.
- Go up from the module directory to the shrew directory and into its
  "bin" directory (this should exist if you did a successful
  build_hyrax_debug).
- Select the executable **besstandalone**

The application path will then loom something like this:

`/Users/aries/src/module_dev_1.6/bin/besstandalone`

Leave "Connect process input & output to a terminal" checked. We want to
see the output in the Console.

Leave the configuration window open for the next changes.

#### Arguments Tab

Now select the "Arguments" tab.

Under the Program Arguments text field, enter the arguments to give to
**besstandalone** as follows. First, make sure the working directory is
the module directory, i.e.

`${workspace_loc:ncml_module}`

in our case.

Our testsuite directory in ncml_module is "tests", so that's where our
*bes.conf* lives. Note that other modules use the directory
*bes-testsuite* instead.

Here's the arguments list we will use for the ncml_module:

`-c tests/bes.conf -d "cerr,ncml,ncml:2" -i tests/eclipse_debug.bescmd`

Here we:

- Specify the bes.conf to use in the testsuite directory so that we load
  the locally compiled (uninstalled) module
- Turn on a few debug channels, here: "cerr", "ncml" and "ncml:2" which
  the ncml_module uses for debug output.
- Tell **besstandalone** to use the file "tests/eclipse_debug.bescmd" as
  its input file.

Note that the "eclipse_debug.bescmd" file doesn't yet exist. This is a
file that we recreate for each test we want to do. It can be made by
hand, but the ncml_module has a script in tests called "generate_bescmd"
that creates a BES XML command for a given ncml file and response type.

Here's an example eclipse_debug.bescmd for the DAS response for
"/data/ncml/fnoc1_improved.ncml":

    <?xml version="1.0" encoding="UTF-8"?>
    <request reqID="some_unique_value" >
        <setContext name="dap_format">dap2</setContext>
        <setContainer name="c" space="catalog">/data/ncml/fnoc1_improved.ncml</setContainer>
        <define name="d">
            <container name="c"></container>
        </define>
        <get type="das" definition="d" />
    </request>

If you're using some other module, you probably want to copy this into a
new file *eclipse_debug.bescmd* and modify it as your debug needs
change, such as to test different responses.

#### Debugger Tab

Next, select the Debugger tab.

- Decide whether to leave "Stop on startup at: main" selected or not. I
  usually leave it selected for the first pass just to be sure I can get
  a breakpoint in the **besstandalone** main() function in case there
  was a problem. You can edit the configuration to deselect it later.

<!-- -->

- Under "Debugger Options", select the "Shared Libraries" tab.
- Make sure "Load shared library symbols automatically" is selected
  since our modules are dynamically loaded.
- Under Directories, click the "Add..." button
- "Browse..." your way to select the *.libs* directory in the module
  directory. In our case the full path ends up being:
  `/Users/aries/src/module_dev_1.6/src/modules/ncml_module/.libs`. This
  should match the module loaded in the bes.conf!
- Leave "Stop on shared library events" unchecked since this will break
  on every library that gets loaded otherwise.

#### Environment Variables

On some flavors of Linux, the *pthreads* library must be 'preloaded'.
This can be done using *export LD_PRELOAD=/lib64/libpthread.so.0* or
using the *Environment* tab of *Debugger Configuration* dialog.

#### Apply Changes

On the bottom of the configuration, click "Apply" to save the changes.

#### Test It

If you're still in the debug configuration window, you can click the
Debug button (assuming you have created a valid *eclipse_debug.bescmd*
file) to run the debugger. If all worked out and you left Stop on
startup at main selected, you will eventually get a breakpoint in the
bes StandAloneApp.cc main() function!

From now on, if you have the module selected in the project window, you
can click the Debug button (the bug icon) (or use Run \> Debug).

You might also want to go to the top-level module initialize call and
set a breakpoint to make sure you're module gets loaded properly and
that you can stop on a breakpoint in it. For the NcML Module, this is
the file:

*NCMLModule.cc*

and the function

`NCMLModule::initialize( const string &modname )`