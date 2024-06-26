## Tricks

- Set the beslistener to run in single, not multiprocess, mode. Do this
  in the *bes.conf* file (use the *BES.ProcessManagerMethod* parameter).
- Build the bes using *developer* mode (so it won't need to be root,
  among other things). Do this with *./configure --enable-developer*

## Use the BESDEBUG Macro

Use the macro *BESDEBUG* defined in *BESDebug.h*.

Set the macro's 'context' as "bes" (nominally, or you can make up
whatever you want) and then use the "cerr \<\< "text: " \<\< var \<\<
endl" style output *except* that you should leave off the initial "cerr
\<\<" and start with the first argument of the stuff to be output - the
marco will take care of getting the output sink and using the output
operator.

Example:

`#include <BESDebug.h>`
`...`
`BESDEBUG( "h4", "File Id:" << _file_id << endl);`

Notes:

1.  You'll need to include the BES_DAP_LIBS when you link an executable
    or a libtool library and you'll need BES_CPPFLAGS when you compile
    (for libdap code)
2.  The trailing semicolon is not needed but including it makes
    automatic code indent software (eclipse, emacs, ...) much happier.

## Start the BES with Debuging on

Use the *-d* option to *besctl* and give *-d* one argument, a string,
with two parts: "

<output sink>

,<context>". For example,

`besctl start -d "cerr,bes"`

would start up *beslistener* with the *bes* debug context active and
write all the debugging info to *cerr*, which is standard error. You can
provide several contexts. For example, you could say

`besctl start -d "./bes.dbg,bes,nc"`

This will send debug statements to the file ./bes.dbg for the context
bes and nc (netcdf_handler). You can also specify the context *all*,
that will send debugging statements for all context.

The BES has debug statements for *bes*, *ppt* and *server*. Each of the
modules that you install will also have debug context. And, you can
create your own context when writing your own module. In your Module
class you would register your context, so as to be available with the
help command, by using the following code:

        BESDebug::Register( "<context>" ) ;

Where context is the string that will be used for your module's debug
context. For example, nc for the netcdf_handler.

To see what debug context is available, when you start the BES using
*besctl*, use the help option:

`besctl help`

    BES install directory: /Users/westp/opendap/opendap
    BES configuration file: /Users/westp/opendap/opendap/etc/bes/bes.conf
    Developer Mode: not testing if BES is run by root
    /Users/westp/opendap/opendap/bin/beslistener: -i <INSTALL_DIR> -c <CONFIG> -d <STREAM> -h -p <PORT> -s -u <UNIX_SOCKET> -v

    -i back-end server installation directory
    -c use back-end server configuration file CONFIG
    -d set debugging to cerr or <filename>
    -h show this help screen and exit
    -p set port to PORT
    -s specifies a secure server using SLL authentication
    -u set unix socket to UNIX_SOCKET
    -v echos version and exit

    Debug help:

    Set on the command line with -d "file_name|cerr,[-]context1,[-]context2,...,[-]contextn"
      context with dash (-) in front will be turned off

    Possible context:
      ascii: off
      bes: off
      dap: off
      ff: off
      h4: off
      h5: off
      nc: off
      ppt: off
      server: off
      usage: off
      www: off

    USAGE: besctl (help|start|stop|restart|status) [options]
    where [options] are passed to besdaemon; see besdaemon -h

## Send Commands to the BES

Now run some commands using bescmdln. You should see debugging being
output to either cerr, or the file you specified when you started the
BES. Here's an example:

    BESClient> set context dap_format to dap2;
    BESClient> set container in catalog values c,/data/nc/fnoc1.nc;
    BESClient> define d as c;
    BESClient> get das for d;
    Attributes {
        u {
            String units "meter per second";
            String long_name "Vector wind eastward component";