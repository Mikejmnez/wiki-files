Here are common issues that can arise when you first run *make
distcheck*

## A file or directory does not exist

**Symptom**: You run the build (e.g., *make check*) and everything's
fine, but *make distcheck* fails because a file or directory was not
present.
**Try**: In the code, look for places where you used the autotools
variable `@srcddir@` (or `@top_srcdir@` or `@abs_srcdir@` or
`@abs_top_srcdir@`) to refer a file or directory that is generated. In a
regular build (e.g., *make check*) that will work fine since the `src`
and `build` directories are one and the same. With *make distcheck*, not
so much since the build (the generated) files are in a different
directory from the source. To fix this, use `@builddir@` (or the obvious
variant).

## An autotest suite fails - it can't find the bes.conf file

**Symptom**: You run the build (e.g., *make check*) and the tests are
fine, but with *make distcheck* all of the tests in an autotest test
suite fail.
**Try**: First, *cd to mycode-3.14.15/_build/...* and run the tests
using *make check*. Since you're reading this, they are probably
failing. Look at how the test's script is actually run and run it using
*verbose* mode. Example: *../../../hello_world/tests/hello_handlerTest
-v*. If it says:

`Failed to initialize stand alone app`
`BES: fatal, cannot open BES configuration file /Users/jimg/src/opendap/hyrax_git/bes/bes-3.19.0/_build/hello_world/tests/bes.conf: No such file or directory.`

Then here's how to fix the problem:

## An autotest suite fails - it can't find the data

**Symptom**:
**Try**:

Then here's how to fix the problem:

In the *bes.conf* file:

``` bash
BES.modules=sample
BES.module.sample=@abs_top_builddir@/hello_world/.libs/libsample_module.so

# For most handlers, the followin gis required because they read data using
# the BES Catalog abstraction. For this handler it is not needed. However,
# the Data.RootDirectory must be defined, even if it's null.

BES.Catalog.catalog.RootDirectory=@abs_top_srcdir@
BES.Data.RootDirectory=/dev/null
```

In the *Makefile.am*, build the *bes.conf* file like this, using *sed*
to remove the '../' from the value assigned to
*BES.Catalog.catalog.RootDirectory*. Having *../* in that path triggers
a security exception in the BES.

``` bash
# Build the bes.conf used for testing so that the value substituted for
# @abs_top_srcdir@ does not contain '../'. This happens when using
# configure's value for the parameter when running the distcheck target.
bes.conf: bes.conf.in $(top_srcdir)/configure.ac
    @clean_abs_top_srcdir=`echo ${abs_top_srcdir} | sed 's/\(.*\)\/\(.[^\/]*\)\/\.\./\1/g'`; \
    sed -e "s%[@]abs_top_srcdir[@]%$$clean_abs_top_srcdir%" \
        -e "s%[@]abs_top_builddir[@]%${abs_top_builddir}%" $< > bes.conf
```

## A directory with generated content is not cleaned

**Symptom**: The *make distcheck* target complains that a directory
contains files after the various clean operations.
**Try**: Add the latent files to the `DISTCLEANFILES` variable of the
`Makefile.am`. Use a shell pattern to remove all of the files in a
directory when the directory itself should not be removed. For example,
`DISTCLEANFILES=atconfig cache/*`

## The build fails because it cannot clean *.deps*

**Symptom**: The build `make distcheck` target is failing and the output
log contains a long list of files in a directory named `.deps` and ends
in *could not delete .deps*.
**Try**: The problem is that a child Makefile.am uses the
**AUTOMAKE_OPTIONS** *subdir-objects* and the code references source or
object files using *../file.o* or *../file.cc*. This causes the
directory to run *rm -rf ../.deps .deps* and then run *rm -rf .deps* in
the parent. But since the child already removed the parent's .deps
directory, make reports an error since the parent's .deps dir no longer
exists. The fix is to remove the *subdir-objects* option. In general,
this option should be used only when building a source file in a child
directory and you want the resulting object file to be put in the child
dir too, and not the CWD.

## Efficiently debugging distcheck

An oxymoron you say, and maybe so, but here goes.

*For this section, I'm going to use real absolute paths from a real
example without trying to abstract them. I used automake 1.16 for this
example. Version 1.14 and earlier uses a slightly different value for
the build dir tree.*

After *make distcheck* is run once and fails, it is possible to *cd*
into the distcheck build tree and repeatedly run builds, editing things
in the source tree. The sources are in a directory like
*/Users/jimg/src/opendap/hyrax_git/bes/bes-3.20.7*. Edit in there. The
source tree is extracted from a tar file as write-protected so you need
to work around that (e.g., *chmod -R u+w .* or use emacs and ctrl-X
ctrl-Q). Then *cd* to the *build* directory to (re)run the Makefile. In
this example, the build directory is
*/Users/jimg/src/opendap/hyrax_git/bes/bes-3.20.7/_build/sub*.

If the tests you want to run require editing the build files
(*Makefile.am*, *configure.ac*, etc.), follow this pattern:

- Edit the Makefile.am in the source dir tree rooted at
  */Users/jimg/src/opendap/hyrax_git/bes/bes-3.20.7*
- Rerun autoreconf at the root of the source tree (*sudo autoreconf
  -fiv*). You have to be root.
- Goto the root of the build tree (*cd
  /Users/jimg/src/opendap/hyrax_git/bes/bes-3.20.7/_build/sub*)
- Run configure from there. Note that you run configure in the root of
  the build tree, but the configure scrip is actually in the root of the
  source tree. Here's how you run it: ../../configure --srcdir=../..
  --prefix="\$dc_install_base" --with-dependencies=...
  --enable-developer
- Now run *make* or whatever.