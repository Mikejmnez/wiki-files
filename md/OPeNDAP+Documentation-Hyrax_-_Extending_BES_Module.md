# Writing modules and handlers

## Putting it all together into a Module

Once you have created your extensions to the BES you need to be a able
to add them to the BES. This is done in the Module class. For example,
in the netcdf_handler directory you will see a NCModule class. Within
this Module class there is a static function that gets called when the
Module is loaded.

    extern "C"
    {
        BESAbstractModule *maker()
        {
            return new NCModule ;
        }
    }

This function must exist in your shared library in order for your module
to be loaded into the BES. In the above example, the netcdf_handler
module NCModule is created in the maker function.

The Module classes have two methods:

    void
    NCModule::initialize( const string &modname )
    {
        BESDEBUG( "Initializing NC Handlers:" << endl )

        // Your code here

        BESDEBUG( "Done Initializing NC Handlers:" << endl )
    }

and

    void
    NCModule::terminate( const string &modname )
    {
        BESDEBUG( "Removing NC Handlers:" << endl )

        // Your code here

        BESDEBUG( "Done Removing NC Handlers:" << endl )
    }

As you could probably already tell, the initialize method is where you
would add all of your extensions and the terminate method is where you
would remove them and do any cleanup of your module. This is not to be
confused with the initialization and termination callbacks described
later. The callbacks are called whenever a request is made to the BES
where as the methods in the Module class are called when the module is
loaded and when it is unloaded from the BES respectively.

Once you have your new Module class you will need to build the new
module. In order to be dynamically loaded by the BES the module needs to
be built as a shared object module. The following is a snippet from a
Makefile.am from an existing module for netcdf.

    lib_besdir=$(libdir)/bes
    lib_bes_LTLIBRARIES = libnc_module.la

    libnc_module_la_SOURCES = $(MODULE_SOURCES)
    libnc_module_la_CPPFLAGS = $(BES_CPPFLAGS)
    libnc_module_la_LDFLAGS = -avoid-version -module
    libnc_module_la_LIBADD = $(BES_DAP_LIBS)

The important line to take note of is for the LDFLAGS, where we spcify
`-avoid-version -module`. This will build a .so file for your library
that can be loaded into the BES.

## Configuring your Module

Now that you have the shared object module built you need to have it
loaded into the BES. To do this you need to create/edit your
configuration file. If you used the script we've provided to create a
template module, find the \*.conf.in file. And find this line.

    BES.modules+=your_module_name

For example, using the netcdf_handler as an example again, we would have
the following:

    BES.modules+=nc

And then you need to tell it where to find that `nc` module. So you'll
need to add the following line:

    BES.module.nc=/full/path/to/installation/lib/bes/libnc_module.so

And the next time the BES is started it will know to load your module
(in this example) nc. It will know to find the nc module using the full
path to the shared object module libnc_module.so.

If you'd like to add new key/value pairs (parameters) to your
configuration file, you can do so at the end of the file. For example,
in the nc module we have:

    NC.ShowSharedDimensions=true

In your code, you'll look for this parameter using the following code:

    bool found = false ;
    string key = "NC.ShowSharedDimensions" ;
    string value ;
    TheBESKeys::TheKeys()->get_value( key, value, found ) ;
    if( found )
    {
      // Do something here with the value
    }

Parameters:

- key - the parameter name that you are looking for
- value - the value of the parameter if found
- found - whether the parameter was found in any of the configuration
  files

You can also have multiple values for a parameter. Notice in the
BES.modules parameter, we use += to add a value to this parameter. To
get these values you would use the following code:

    bool found = false ;
    string key = "NC.MultiValue.Parameter" ;
    vector<string> values ;
    TheBESKeys::TheKeys()->get_values( key, values, found ) ;
    if( found )
    {
      // Do something here with the values
    }

Parameters:

- key - the parameter name that you are looking for
- values - the vector of values for the parameter if found
- found - whether the parameter was found in any of the configuration
  files

Throughout this document we will tell you how to add the particular
extensions to the Module class' initialization method and how to remove
your extensions in the Module class' termination method.

## New services

A service is a grouping of commands that are provided by a module and
can be handled by other modules, like data handlers. For example,
OPeNDAP provides the `dap` service with the commands
`das, dds, ddx, and dods`. These commands can be handled by the
different data handlers that can serve up OPeNDAP responses for these
commands. The format for each of these commands is `dap2`.

[New Services](New_Services "wikilink")

## New response types.

A response to a BES request is considered here to be a response type.
Whether this response type is a simple informational response, such as a
response to 'show help;' or the response type is a data response such as
a DAS response in the 'get das for definition;' command. New response
types can be added to the BES.

[New Response Types](New_Response_Types "wikilink")

## New request/data handlers

A request handler is a handler that knows how to fill in a particular
response, such as DAS, DDS, DataDDS, help, and version responses, for
example.

[New Request Handlers](New_Request_Handlers "wikilink")

## Containers and how to store them

New ways of storing containers, called container storage. Currently we
have built in volatile storage, which means storing new containers only
during the duration of your connection with the BES. We also have a way
to load containers from a file that can be configured in the
initialization file. You could also create user specific container
storage, storing container information in a MySQL database, for example.

[Containers, and how to store them](BES_Containers "wikilink")

## Definitions and how to store them

New ways of storing definitions, called definition storage. Currently we
have built in volatile storage, which means storing new definitions only
during the duration of your connection with the BES. You could also
create user specific definition storage, saving off those oft used
definitions for later use.

[Definitions, and how to store them](BES_Definitions "wikilink")

## Reporting

A report mechanism. Once a request has been completed, the information
is passed to any reporters that are registered with the server. This way
you can save user information, usage information, data access
information, and more.

[Keeping Track of Requests](BES_Reporters "wikilink")

## Transmitting responses

Different ways of transmitting the data. The default is to return the
data through standard output, which gets redirected back to the client.
But, with the 'get' command you can specify a 'return as' command ( see
above ) that could save the response to a file, transmit it via email,
save it in a new data file like netcdf.

[New Transmitters](New_Transmitters "wikilink")

## Initialization and Terminiation Callbacks

Add in your own initialization and termination routines. If you have
routines that need to be run when the BES starts up, you can add
callback functions for initialization and termination.

[Creating Initialization and Termination
callbacks](BES_Initialization_Termination "wikilink")

## Handling Exceptions

Add in your own exception handling methods. When an exception is thrown
within the BES, the exception is handled by the BES Exception Manager.
It is possible to register a routine to be called before the default
handling is done. This way, if you have a specific way that you want to
handle specific exceptions, you can do that here.

[Handling Exceptions within the BES](BES_Exception_Handling "wikilink")

## Aggregating your data

By default there are no aggregation routines in the BES. But, the BES
provides a mechanism for data to be aggregated.

[Providing Aggregation Routines](BES_Aggregation "wikilink")

## New commands

You've seen all the default commands available in the BES. Now what if
you want to create a new command, or even modify an already existing
command.

[Creating your own commands](New_BES_Commands "wikilink")

## Adding Debugging to your code

The BES has a simple to use debugging tool. When the BES is started up
you can specify -d "

<output>

,<context>" on the command line of the besctl script to enable
debugging. The output option is either cerr, which will dump all debug
statements to standard error, or a file name, which will dump all debug
information to the specified file. The context option is a comma
separated list of components.

[New Debugging](New_Debugging "wikilink")

## Submitting your modules

So you've come up with a killer module that does some pretty cool stuff.
Well ... share it with the rest of us.

[Sharing your BES Modules](Sharing_BES_Modules "wikilink")

[Extending BES](Category:Extending_BES "wikilink") [Extending BES
Module](Category:BES_Modules "wikilink")