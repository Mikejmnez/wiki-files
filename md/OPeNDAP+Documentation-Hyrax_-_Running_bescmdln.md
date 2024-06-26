## Basic Commands

First we will issue some simple commands to make sure that the client is
talking to the server. First, start the command-line client:

`% bescmdln -h localhost -p 10022`

The -h option specifies the machine on which the BES is running. In this
case, it's your local machine. The -p option specifies the port the BES
is running on. The default, set in the BES configuraiton file, is 10022.
If you changed this, or if you started the server with the -p option,
then you need to use that port number here.

If you just use these options then you will start using the command line
version of the client. There are other options, but we'll start here.
From here you should get a prompt. Let's try a simple command (remember
to terminate each command with a semicolon):

`BESClient> show status;`

You should get a response showing the status of the server:

`Listener boot time: MDT Thu Jun  9 14:12:22 2005`

Try another one:

`BESClient > show help;`

This one should show both the BES core commands, DAP commands, and your
help information.

If you have installed a data handler, let's take a look at your data. By
executing this request you should see the root node of your data
directory.

`BESClient > show catalog;`

If you can't see your data, then make sure that the RootDirectory
parameters in the [BES Configuration
file](Hyrax_-_BES_Configuration "wikilink") are correct.

`BESClient > exit`

This one will exit out of interactive mode.

## Commands for Hyrax testing

### Poke around in the RootDirectory to see what's actually visible to the BES

##### show the root catalog

`show catalog;`

##### will show the contents of "pathname"

For example, show catalog for "/data/nc"; will show all the stuff in the
/data/nc directory.

`show catalog for "pathname";`

### Get the BES to return a DAP response object

You need three commands to do this:

##### bind the dataset to a container in a catalog

`set container in catalog values c,/data/nc/feb.nc;`

##### make a definition so you can access that container

`define d as c;`

##### request a particular response

`get ddx for d;`

## Command line options

Other command line options available to the bescmdln program:

`-u specifies the name of a Unix socket for connecting to the server.`
`-h specifies the name of a host for TCP/IP connection`
`-p specifies the port where the server is listening for TCP/IP connection.`
`-x makes the client execute a command and exit. This flag requires the -f flag.`
`-f sets the target file name for the return stream from the server.`
`-i sets the target file name for a sequence of input commands.`
`-t sets the timeout in seconds and is optional.`
`-d "cerr|`<filename>`,`<context>`" sets the client session for debugging and is optional.`
`-v forces the client to show the version and exit`

Connection Flags: -u or -p -h are required to connect to a server and
specify either a Unix socket or a TCP/IP socket.

Input/Output Flags: you can specify that the input is from the command
line with the -x flag or that the input must be read from a file with
the -i flag. If you specify either -x or -i you must specify the name of
a file for the output stream of the server with the -f flag. If neither
the -x nor the -i flags are specified then the client goes into
interactive mode. To exit out of interactive mode just type 'exit'
(without the quotes) at the BESClient\> prompt.

For debugging information either specify cerr to have debugging
information dump to standard error, or the name of a file. The context
option is a comma separated list of debugging context (component
debugging). Specify all to get debugging from all components.