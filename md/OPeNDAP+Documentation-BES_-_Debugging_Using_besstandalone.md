When you need to debug a handler, there are several ways to go about it.
First, you can use a run-time debugger like the one included with
Eclipse (really GNU's *gdb*), fall back on the tried and true 'print
statements' or some other combination of program instrumentation and
run-time diagnosis. In all cases, being able to run your handler *as
part of a command line program* and not a server, will speed up and
simplify the process.

We have designed a run-time debugging tool for just this purpose.

# Review: Debugging using a client and the server

To test (and/or debug) your handler using the BES (server), you first
build and install your new handler version. Then you modify the
*bes.conf* file so that the handler will be loaded at run-time and start
the server. Next you start up the *bescmdln* client and point it at the
server using the hostname (likely *localhost*) and port number (again,
likely the default of *10022*). You can issue commands directly to
*bescmdln* or pass it the name of a file of commands to run in sequence.
Either way bescmdln prints whatever it get back from the BES to standard
output.

# What you would like

It would be far easier to be be able to run you handler as if it *was*
the command line client and see the output directly. This is exactly
what the command *besstandalone* does for you. Like the *besctl start*
command, *besstandalone* reads an optional *bes.config* file and takes
optional debugging switches. Unlike *besctl* it does not start a server
but, after performing the server initialization routines, it switches to
'client mode' and reads commands, printing the response from the BES
*plus* any debugging output that was generated.

The command files used by *bescmdln* and *besstandalone* are identical,
so files developed for one can be used with the other.

# Examples

A sample command file that asks the BES for a DDS response (from
\$prefix/src/modules/hdf4_handler/bes-testsuite):

``` xml

<?xml version="1.0" encoding="UTF-8"?>
<request reqID="some_unique_value" >
    <setContext name="dap_format">dap2</setContext>
    <setContainer name="data" space="catalog">/data/1990-S1700101.HDF.gz</setContainer>
    <define name="d">
    <container name="data" />
    </define>
    <get type="dds" definition="d" />
</request>
```

Here's how to run these commands using the server and *bescmdln*:

``` bash
besctl start

bescmdln -h localhost -p 10022 -i 1990-S1700101.HDF.dds.bescmd
```

Here's the version using besstandalone:

``` bash
besstandalone -c bes.conf -i 1990-S1700101.HDF.dds.bescmd
```

Note that if you try this you will see that most of the regression tests
use bes command files that reference local data and the paths are a bit
different than when those same data sets are installed in a running
server.

# Some Tricks

## Using besstandalone and the BESDEBUG instrumentation macro

The BES supports a flexible run-time instrumentation system that you can
access using besstandalone (there are other ways too, see the [Hyrax
Admin Interface](Hyrax_Admin_Interface "wikilink")). Here's an example
of instrumentation:

``` Cpp
void
HDF4Module::initialize(const string & modname)
{
    BESDEBUG("h4", "Initializing HDF4 module " << modname << endl) ;

    BESRequestHandler *handler = new HDF4RequestHandler(modname);
    BESRequestHandlerList::TheList()->add_handler(modname, handler);

    ...
```

The instrumentation macro is *BESDEBUG*. To 'turn on' this and see the
output, start *besstandalone* using the *-d* option. When you specify
the debug option, you need to provide one argument which names the
output sink for the debugging information and one or more 'selector
names'. An example will make things clearer. To see the output from the
above BESDEBUG call, you would pass this option to besstandalone: *-d
"cerr,h4"*. Here's how you would run it:

``` bash
besstandalone -d "cerr,h4" -c bes.conf -i 1990-S1700101.HDF.dds.bescmd
```

Of course, *cerr* is C++'s standard error output sink; you can use
*cout* or the name of a file instead. Also, you can list a number of
selectors, so your handler may have separate BESDEBUG calls for
initialization, run-time status, some tricky parts of the code, ...

## Filtering binary output using getdap

One thing that's nice about DAP is that so many of the responses are
ASCII or XML. That makes them easy (relatively) to debug when compared
to binary outputs. But data is binary in most of these datasets and DAP
transmits it that way for obvious efficiency reasons. The *getdap*
command line client can be used to filter the binary data response
object returned by the bes so that the data values are rendered as ASCII
(but without using the server's ASCII output capability). Here's what
that looks like.

Given:

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<request reqID="some_unique_value" >
    <setContext name="dap_format">dap2</setContext>
    <setContainer name="data" space="catalog">/data/foo2.hdf.gz</setContainer>
    <define name="d">
    <container name="data" />
    </define>
    <get type="dods" definition="d" />
</request>
```

``` bash
besstandalone -d "cerr,h4" -c bes.conf -i foo2.hdf.data.bescmd | getdap -M -
```

Where the option *-M* tells getdap to strip off any MIME headers in the
response and the dash tells it to read from stdin.