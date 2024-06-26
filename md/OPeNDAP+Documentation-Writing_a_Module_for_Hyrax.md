Other guides:

- [Hyrax_-_Create_BES_Module](Hyrax_-_Create_BES_Module "wikilink")
- [Hyrax_-_Extending_BES_Module](Hyrax_-_Extending_BES_Module "wikilink")
- [Hyrax_-_Example_BES_Modules](Hyrax_-_Example_BES_Modules "wikilink")

Autoconf documentation:
<http://www.gnu.org/savannah-checkouts/gnu/autoconf/manual/autoconf-2.69/html_node/index.html#Top>

## Including the library ...

It's often easiest for people building from source code if the data
access API used by the handler is included in the source.

Support three options:

1.  using --enable-local-API
2.  using --with-API-prefix
3.  finding a copy installed on the build OS

### Here's how

First, look to see if the *configure* was run with the *--with-API*
option, then test for the *--enable...* option, and then test for a copy
of the API library on the host. The first of those found is used; if
none are found/given, it's a configuration error.

In the Makefile.am:

``` make
# distclean should remove everything built by make or configure
distclean-local:
    cd gdal-1.9.1 && make distclean && rm -rf build

# Not nearly as clean as it could be, but this removes .svn directories
# in subdirs. This also cleans out the gdal library's binaries, without
# removing them from the 'real' directory
dist-hook:
    (cd $(top_distdir)/gdal-1.9.1 && make clean && rm -rf build)
    rm -rf `find $(distdir) -name .svn`
```

In configure.ac:

``` sh
# Which copy of GDAL should be used for the build?
GDAL_FOUND=

AC_ARG_WITH(gdal, AS_HELP_STRING([--with-gdal], [Use the copy of GDAL at this location]),
            with_gdal_prefix="$withval", with_gdal_prefix="")

if test -n "$with_gdal_prefix" -a -x $with_gdal_prefix/bin/gdal-config
then
    AC_MSG_NOTICE([Using $with_gdal_prefix as the GDAL prefix directory.])
    GDAL_LDFLAGS="`$with_gdal_prefix/bin/gdal-config --libs`"
    GDAL_CFLAGS="`$with_gdal_prefix/bin/gdal-config --cflags`"
    GDAL_FOUND="yes"
elif test -n "$with_gdal_prefix"
then
    AC_MSG_ERROR([You set the gdal-prefix directory to $with_gdal_prefix, but gdal-config is not there.])
fi

AC_ARG_ENABLE(local-gdal, AS_HELP_STRING([--enable-local-gdal], [Build and use the local copy of GDAL]) )

# if we are going to build our own copy of GDAL and the JASPER JPEG2000 library
# was built, be sure to include it here, too.
if test -n "$enable_local_gdal" -a "$enable_local_gdal" = "yes"
then
    AC_MSG_NOTICE([Building the local GDAL library.])
    jasper=
    if test "$JASPER_FOUND" = "yes"
    then
        AC_MSG_NOTICE([Building JASPER support in the GDAL library.])
        if test -n "$with_jasper_prefix"
        then
            jasper="--with-jasper=$with_jasper_prefix"
        else
           jasper="--with-jasper=jasper-1.900.1/build"
        fi
    fi
    gdal_prx=`pwd`/gdal-1.9.1/build
    if (cd gdal-1.9.1 && ./configure --prefix=$gdal_prx --disable-shared $jasper && make -j9 && make install)
    then
        GDAL_LDFLAGS="-Lgdal-1.9.1/build/lib -lgdal"
        GDAL_CFLAGS="-Igdal-1.9.1/build/include"
        AC_SUBST([GDAL_LDFLAGS])
        AC_SUBST([GDAL_CFLAGS])
        GDAL_FOUND="yes"

        # AC_MSG_NOTICE(["GDAL_LDFLAGS status >$GDAL_LDFLAGS<"])
        # AC_MSG_NOTICE(["GDAL_CFLAGS status >$GDAL_CFLAGS<"])
    else
        AC_MSG_ERROR([I could not build GDAL.])
    fi
fi

if test -z "$GDAL_FOUND"
then
    AX_LIB_GDAL([1.7.2])
    if test ! -z "$GDAL_CFLAGS" -a ! -z "$GDAL_LDFLAGS"; then
        GDAL_FOUND="yes"
    fi
fi

if test -z "$GDAL_FOUND"
then
    AC_MSG_ERROR([I could not find GDAL.])
fi
```

### An alternative using Autoconf

``` sh
# Save the configure arguments so we can pass them to any third_party
# libraries that we might run configure on (see
# third_party/Makefile.am). One downside of our strategy for shipping
# and building third_party libraries is that we can't expose options
# from nested third_party configure scripts.
CONFIGURE_ARGS="$ac_configure_args"
AC_SUBST(CONFIGURE_ARGS)

# Force configured third_party libraries (currently only libprocess)
# to only build a static library with position independent code so
# that we can produce a final shared library which includes everything
# necessary (and only install that).
ac_configure_args_pre="$ac_configure_args"
ac_configure_args_post="$ac_configure_args --enable-shared=no --with-pic"
ac_configure_args="$ac_configure_args_post"

# Make sure config.status doesn't get the changed configure arguments,
# so it can rerun configure in the root directory correctly. This is
# necessary for Makefile rules which would regenerate files (e.g.,
# 'Makefile') after configure.ac was updated.
AC_CONFIG_COMMANDS_PRE([ac_configure_args="$ac_configure_args_pre"])
AC_CONFIG_COMMANDS_POST([ac_configure_args="$ac_configure_args_post"])

AC_CONFIG_SUBDIRS([third_party/libprocess])
```