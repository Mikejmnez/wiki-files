## Why

Why use *valgrind* with unit tests? Aside from why not, because it can
catch errors like memory corruption, uninitialized value use and leaks.

This addition drops into autotools very easily and works well with
CppUnit.

## How

Modify *configure.ac* and *Makefile.am*

### Changes to configure.ac

First, modify configure.ac so that it takes an option to turn this
feature on. The *m4* file gcov_valgrind.m4 from libdap defines a very
simple macro that will do this so only one line needs to be added to
configure.ac. Copy and add the macro and add the line:

`DODS_GCOV_VALGRIND`

to configure.ac. This macro adds the --enable-valgrind and the
--enable-gcov (see below) options to the configure script and defines an
automake variable that can be used in a Makefile.am file.

Note that gcov is the GNU compile coverage analysis tool. To use it, you
must configure using *./configure --enable-coverage* (assuming you use
the macro described above). Once configured for coverage analysis, run
the tests and coverage data will be written to disk which can be
analyzed using the *gcov* and *gprof* tools. The *gcov* program provides
coverage data while the *gprof* tool provides profiling information.

### Changes to Makefile.am

Add a conditional line to the unit tests Makefile.am that will set the
Makefile variable *TEST_ENVIRONMENT* to the following: *valgrind --quiet
--trace-children=yes --error-exitcode=1 --dsymutil=yes
--leak-check=yes"*. Here's what the options do:

quiet: suppress the usual (non-error) output of valgrind
trace-children: when a test starts a subprocess, it's trace too (not the default). This is important when starting programs made using libtool since they are scripts by default that fork/exec to start the 'real' program.
error-exitcode: valgrind does not usually return a non-zero code when it finds a leak, etc., but instead returns the program's exit code. Setting this to *1* means that leaks will show up as errors when the tests are run by *make*.
dsymutil: useful for OS/X, but can increase runtime; does nothing on Linux
leak-check: Using *yes* gets leak details (the default is *summary*)

To add this in a Makefile.am use:

``` make
if USE_VALGRIND
TESTS_ENVIRONMENT=valgrind --quiet --trace-children=yes --error-exitcode=1 \
--dsymutil=yes --leak-check=yes
endif
```

Other *valgrind* options that might be useful:

log-file: Write any output detail errors to a file with the PID. I stopped using this with tests that are run from a Makefile since it writes files even when there's no output (i.e., using --quiet and there are no errors) and that is confusing.
show-reachable: this will show all memory in use when the process exits
track-origins: the *memcheck* tool in *valgrind* shows returns errors for uninitialized use, but not where the uninitialized memory/variable was defined. setting this to *yes* wil get that information, but at the cost of about doubling the runtime of the test/program.