Under construction 11/20/14

Use the *modules* branch of *bes* to automatically build modules.

Tis pages describes how to take an existing BES module and incorporate
it into the *modules* branch of the BES so that it can build both as a
standalone module and as part of an ensemble of modules built using a
single *configure* script and *Makefile.* The first step is to add the
module as a git submodule and then make a handful of edits to the bes
and module's configure.ac and Makefile.am files. The changes are minimal
and the resulting module code will build both as a standalone module and
as part of the 'combined modules' build. The BES will also build as a
standalone program, and because of the way git's submodules work, it's
easy to checkout just the BES with the baggage of the modules.

## To add a new handler to the BES repo on github

The following assumes that you have cloned the BES repo from github and
are on the *master* branch.

For each module to be added:

check it out in the *bes/modules* directory: git submodule add <github repo url>
make a *modules* branch: cd <module dir>; git branch modules
switch to the new branch: git checkout modules
set the *modules* branch in the *.gitmodules* file: emacs .gitmodules; *branch = master*

Now follow the recipe outlined in the following sections of the page...

## Changes to the module's *Makefile.am*

Hack the module's Makefile.am so that CPPFLAGS and LIBADD reference the
correct places given that the code will build w/o bes first being
installed

``` bash
if DAP_MODULES
AM_CPPFLAGS = -I$(top_srcdir)/dispatch -I$(top_srcdir)/dap $(DAP_CFLAGS)
LIBADD = $(DAP_SERVER_LIBS) $(DAP_CLIENT_LIBS)
else
AM_CPPFLAGS = $(BES_CPPFLAGS) # or wahtever was set here or in ..._CPPFLAGS
LIBADD = $(BES_DAP_LIBS)      # and ..._LIBADD
endif
```

then

``` bash
# comment this out to force use of AM_CPPFLAGS libcsv_module_la_CPPFLAGS = ...
libcsv_module_la_LIBADD = $(LIBADD)
```

For unit-test code you may need to add some of the bes code to the link
line. To do that, use the makefile variables:

- BES_DISPATCH_LIB
- BES_XML_CMD_LIB
- BES_PPT_LIB
- BES_EXTRA_LIBS

where you almost certainly want to use \$BES_DISPATCH_LIB
\$BES_EXTRA_LIBS

Look for a line like

``` bash
xml_data_handler.conf: xml_data_handler.conf.in config.status
```

and add *\$(top_srcdir)/* in front of *config.status*

If the module uses *besstandalone,* add *bes.conf.modules.in* to the
EXTRA_DIST variable in the *Makefile.am.*

## Changes to the module's *configure.ac*

Add "AM_CONDITIONAL(\[DAP_MODULES\], \[false\])" to the modules
configure.ac

If the module uses autotest to run a set of regression tests using
*besstandalone,* add generation of *bes.conf* from *bes.conf.in.* You'll
also need to make corresponding changes in other places, documented on
this page.

## Additions to the module's files

Add *bes.conf.modules.in*. The key parts of this file that are different
from the existing *bes.conf.in* are:

``` bash
BES.modules=dap,cmd,csv
BES.module.dap=@abs_top_builddir@/dap/.libs/libdap_module.so
BES.module.cmd=@abs_top_builddir@/xmlcommand/.libs/libdap_xml_module.so
BES.module.csv=@abs_top_builddir@/modules/csv_handler/.libs/libcsv_module.so

BES.Catalog.catalog.RootDirectory=@abs_top_srcdir@/modules/csv_handler
BES.Data.RootDirectory=/dev/null
```

Note how the BES libraries are referenced
(*@abs_top_builddir@/dap/.libs/...* ...) and how the
*BES.Catalog.catalog.RootDirectory* is specified.

Add a *.gitignore*

Make sure to add the *bes.conf.modules.in* file to git.

## Changes to the BES' *configure.ac*

Add files to be build by BES' configure:

``` bash
    AC_CONFIG_FILES([
    ...
    modules/dap-server/Makefile
    modules/dap-server/asciival/Makefile
    modules/dap-server/asciival/unit-tests/Makefile
    modules/dap-server/asciival/unit-tests/test_config.h
    modules/dap-server/www-interface/Makefile

    modules/dap-server/bes-testsuite/bes.conf:modules/dap-server/bes-testsuite/bes.conf.modules.in
    ...])

    AC_CONFIG_FILES([modules/dap-server/www-interface/js2h.pl], [chmod +x modules/dap-server/www-interface/js2h.pl])
```

using the trusty cut and paste and prefixing them all with ''modules/

<dir name>

.*Put that in the the AM_COND_IF that's at the*configure.ac*file.
**NB:** The highlighted line show how to make autoconf use a template
with an arbitrary name (*bes.conf*is made
from*bes.conf.modules.in*instead of*bes.conf.in'' in this case).

If the module uses a m4 macro defined in a file in its conf directory,
copy that to the bes/conf dir. You then should add a call to that macro
to the bes' configure.ac script mirroring the call in the modules's
configure.ac script. If the macro looks for a library, etc., that will
possibly/likely be in the hyrax-dependencies bundle, hack the macro so
that it takes an extra parameter that is the 'master deps' directory.
Look at a macro like the ones for hdf5 or netcdf to see how to do that
easily. If the module looks for a library using autoconf calls in line
(in configure.ac), just copy those in, hacking as needed. Most of the
handler-specific code in configure.ac is grouped toward the end of the
script.

## Changes to the BES' *modules/Makefile.am*

Given the code is in ''modules/

<dir name>

,*add*

<dir name>

*to the*modules/Makefile.am'' as a subdir.

## Pushing the new branch to the remote repo

double check the *.gitignore* file and that you have *git add* the *bes.conf.modules.in* file: git status
commit changes to the local repo: git commit -a
push those local changes to the remote repo and set this new branch to be the one the code tracks: git push --set-upstream origin modules
check the bes and commit there: git commit -a
and push those changes (the upstream repo was already set): git push

## Git Hacks

To manage an empty dir in git, put a .gitignore file in it that ignores
everything except itself:

``` bash
# Ignore everything in this directory; this hack enables git to track
# and fetch, etc., an otherwise empty directory.
*
# Except this file
!.gitignore
```