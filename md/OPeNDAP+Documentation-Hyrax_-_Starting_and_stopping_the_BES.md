## The *besctl* command

The *besctl* command is used to control the BES daemon. For Hyrax
version 1.7 and earlier, this the only way to control the BES. Starting
with Hyrax 1.8 (release date tentatively set for fall 2011) the [Hyrax
Admin Interface](Hyrax_-_Administrators_Interface "wikilink") can be
used to start, stop, reconfigure and debug the BES once the master
daemon is started using this command. The [Hyrax Admin
Interface](Hyrax_-_Administrators_Interface "wikilink") also provides
for both hard and soft restarts of Hyrax while the *besctl* command does
not.

## The most common uses of *besctl*

To start and stop the BES, use the *besctl* command. The besctl command
has a number of options, but the most important are the *start* and
*stop* arguments. To start the BES use:

`besctl start`

and to stop it, use:

`besctl stop`

The general form for the *besctl* command is:

`besctl (help|start|stop|restart|status|pids|kill) [options]`

where *options* are:

    -i back-end server installation directory
    -c use back-end server configuration file CONFIG
    -d send debugging for CONTEXT to cerr or <filename>
    -h show the help information and exit
    -p set port to PORT
    -r bes.pid file stored in directory PID_DIR
    -s specifies a secure server using SLL authentication
    -u set unix socket to UNIX_SOCKET
    -v echos version and exit

These options are used only in special circumstances; of them all the
*-d* option to turn on debugging is the most useful. The syntax for
run-time debugging/diagnostic output is:

`-d "`

<output sink>

,\<context 1\>, ...,<context n>"

where a typical example would be:

`-d "cerr,ascii,netcdf,besdaemon"`

which would tell the daemon to send diagnostic output from the ASCII
handler, the NetCDF handler and the BES daemon itself to the terminal's
standard error output.

## About each of the arguments to *besctl*

The besctl command accepts a total of seven arguments.

### help

Display help information for the besctl command. The help argument
displays, among other things, all of the main *centexts* that can be
used with the debug (*-d*) option.

### start

Start the BES

### stop

Stop the BES. This is a 'hard' stop and any active connections will be
dropped.

### restart

This is the same as using the stop and start commands separately. If you
want to issue a 'soft' restart of Hyrax, use the Hyrax Admin Interface,
which will be available in Hyrax 1.8.

### status

This returns the master BES daemon process id number and the user id
under which it is running.

### pids

The BES is actually a collection of processes; use this argument to find
the process id numbers for them all.

### kill

Sometimes the *stop* or *restart* arguments don't work. Use this
argument to stop all the processes. The *stop* command works by sending
the TERM signal to the master BES daemon process which then sends that
signal to all of the subordinate BES daemon processes, but processes can
ignore this signal in certain circumstances. Using the *kill* argument
to besctl sends the KILL signal to all of the processes; KILL cannot be
ignored by a process, so this is certain to stop the server.

## About the options accepted by *besctl*

### server installation directory

Use the *-i* option to force besctl to use a specific directory as the
server's root directory. This option is useful if you have several BES
daemons running on one machine.

`-i `<directory>

### server configuration file

Use the *-c* option to force the daemon to use a specific *bes.conf*
file instead of the file found at *server root*/etc/bes/bes.conf

`-c `<configuration file path>

An alternative to usign this option is to use the BES_CONF environment
variable to point to a configuration file. Set the value of the
environment variable to the path of the configuration file. Be sure to
export the environment variable. Also note that as of Hyrax 1.6, the BES
reads a significant amount of configuration information from the *server
root*/etc/bes/conf.d directory. You can disable this by editing the
bes.conf file; look for the *Includes* directive.

### debugging

Use the *-d* option to achieve fine-grained control over the server's
diagnostic output. The -d option takes a single double-quoted string
which must contain the name of the output sink for the diagnostic
information and a comma separated list of 'debug contexts'. The sink may
be either an open stream (e.g., *cerr*) or a file while the contexts are
defined by/in the BES source code. All modules define a context that
matches their name and you can see this using the *help* argument to
besctl, although most define additional contexts. The best way to find
out about the contexts available is to look at the source code for the
server.

`-d "cerr,besdaemon"`

Use the special context *all* to see output from all of the contexts.
This will produce very verbose output.

### help

The *-h* option prints a short online help message which lists the
option switches. Note that this option doesn't work when you supply an
argument like *start*, *stop*, et c., except for *help*.

`-h`

### port

Use the *-p* option to set the port the daemon uses for communication
with the Hyrax front-end.

`-p `<number>

### PID file

Use the *-r* option to tell the BES where to store the master daemon's
process id number.

`-r `<directory>

### SLL authentication

Use the *-s* option to force the server to use SSL authentication. This
option is not used with Hyrax. To configure Hyrax for use with SSL, see
information about running ht efront-end of the server with SSL. This is
typically done by securing a Tomcat or Apache server and is standard
procedure used by many general web sites.

### unix socket

Use the *-u* option to force the BES to use a Unix socket for
communication with the front-end instead of the TCP socket. We rarely
use this.

`-u `<socket>

### verbose

use the *-v* option to see the version of the bes. The server does not
start, ..., et cetera.

`-v`