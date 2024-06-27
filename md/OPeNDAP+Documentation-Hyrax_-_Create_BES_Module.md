Building a new OPeNDAP BES Module

This document will cover the creation of a new BES module using the
besCreateModule script. It is quite simple to create a new module, once
you understand the power of the BES framework.

For more information on the server architecture, please refer to the
document BES_Server_Architecture.doc. For more information on
configuration the BES server, please refer to the document
BES_Configuration.doc.

------------------------------------------------------------------------

# Creating a new OPeNDAP module

We have provided you with a script that can build code that will
immediately compile. This script will create source code files that you
will eventually add your code to, and some code that will not need to be
changed, as well as configuration scripts and make files.

------------------------------------------------------------------------

## Using the besCreateModule script

The besCreateModule script will ask you, the developer, a few simple
questions in order to create some default source code. The good thing
is, once this code is written you can run make and your code will build.
Of course, it won't do much because you need to write specific code to
provide OPeNDAP objects for the requested data. There are no parameters
to the script, so, from the command line, do the following:

`% ./besCreateModule`

The script will ask the user the following questions:

`Enter server type, e.g. cedar, fits, netcdf:`

So, for example, if you are providing cedar data, you would respond with
cedar. Or, if you are providing hdf5 data you would respond hdf5.

The second question is:

`Enter C++ class prefix, e.g. Cedar, Fits, Netcdf:`

There are some C++ class files that are created that need a prefix. What
do you want that prefix to be? For example, if you are writing a new
cedar server then the prefix could be Cedar. We suggest that the first
character be uppercase, but does not need to be. Another example, if you
are providing hdf5 data, then you could respond HDF5.

The third question is:

`Enter responses handled by this server (das dds data):`
`(space separated. help and version are added for you, no need to include them here)`

So, if your module will provide OPeNDAP DAS, DDS, and Data objects (and
DDX) then you would simply hit enter (default is das dds data). Here is
where some of the new extensibility comes in. You could provide a new
way of viewing the data. For example, Cedar provides four new response
types, tab, flat, info and stream. We will explain this in more detail
in another document. For cedar, then, you would respond with 'das dds
data tab flat info stream.' If you are not providing any views of data,
then you would enter 'none'.

And the final question is:

`Enter new commands you are implementing:`

If you are writing new commands for this module, then you would enter
them here. New commands would be something like "say hello to world;",
something that the BES has not implemented.

------------------------------------------------------------------------

## What gets created?

Once you have answered these questions, then there are a bunch of files
that get created. Let's say that you enter 'hdf', 'HDF', and accept the
default for response types (das dds data). The files created are:

`HDFModule.cc`
`HDFModule.h`
`HDFRequestHandler.cc`
`HDFRequestHandler.h`
`HDFResponseNames.h`
`Makefile.am`
`configure.ac`
`bes.conf`

If you specified new response types then for each new response type two
files will be created:

`HDF`<response_type>`ResponseHandler.cc`
`HDF`<response_type>`ResponseHandler.h`

If you specified new commands then for each new command four files will
be created:

`HDF`<command_name>`Command.cc`
`HDF`<command_name>`Command.h`
`HDF`<command_name>`ResponseHandler.cc`
`HDF`<command_name>`ResponseHandler.h`

A new configuration directory is also created, conf, that holds build
configuration files and information.

The besCreateModule script then runs autoreconf, configure, and make.
The new code will compile and build the BES module library that can be
dynamically loaded into the BES. We are assuming that you have already
built the OPeNDAP code (libdap and bes)

And that's it!

Now the task is to write the code that will actually build your
responses. Of course, you are free to add whatever you need to the
configure.ac file and Makefile.am file in order to build your new
module. You'll need to add information about libraries and includes that
your project will need.

[Create BES Module](Category:BES_Modules "wikilink")