Back: [AIS Using NcML](AIS_Using_NcML "wikilink")

<img src="BES_Handler_class_diagram.png"
title="BES_Handler_class_diagram.png" width="400"
alt="BES_Handler_class_diagram.png" />
<img src="BES_Handler_class_diagram_detailed.png"
title="BES_Handler_class_diagram_detailed.png" width="400"
alt="BES_Handler_class_diagram_detailed.png" /> There are several ways
to build a shell BES handler and then add specific functionality to it.
One way is to use the *besCreadeModule* script that is included in the
BES source code. A second way is to copy and modify the *csv handler*
software that's also included in the BES source code. In each case it's
important to know that the BES is an object-oriented framework and as
such, you will be specializing classes that the framework defines and
then adding new classes and/or functions that are specific to your
handler. With that in mind a static class diagram can help show how the
BES's classes are related to each other and to the software you must
write. Shown to the left is a UML class diagram that includes the most
important of the BES classes when writing a new data handler.

- A [high
  resolution](http://docs.opendap.org/images/9/9e/BES_Handler_class_diagram.png)
  version of the class diagram to the right.
- Here's a [link to the detailed
  diagram](http://docs.opendap.org/images/a/a0/BES_Handler_class_diagram_detailed.png).

Also, be sure to look over THE BES documentation files included on this
design's main page and on in the Hyrax documentation. Especially useful
is the BES Handler tutorial that covers building the Hello, World and
CSV handlers. It's so useful I'll repeat it here: [Extending the OLFS:
Writing Custom Dispatch
Handlers](http://www.opendap.org/support/bom_sdw/SDW_2r0_OLFSExtensions.ppt).

## Explanation of the Classes in the Diagram

### Basic operation of the BES Framework

The BES is a daemon that waits for connections and, when one is
detected, reads the commands and executes the appropriate methods in
either the BES framework or one of the handlers that are loaded into the
BES during initialization. In a very general sense, the BES follows this
workflow:

- Parse the command associate with the current request
- Decide which handler should be used to satisfy the request
- Use the handler to build a response
- Return the response

It's possible to define new commands and new ways of transmitting data
for various protocols, but those uses of the BES are beyond what a
simple data handler that works with Hyrax needs. A data handler to be
used with Hyrax that only needs to support the basic BES command (e.g.,
*show version*), the DAP commands and return responses using HTTP, does
not need to specialize the command set, the response types or the
transmitter classes.

### Classes that are Provided

In the diagram, all of the classes whose names start with *BES* are part
of the BES framework. You don't have to write anything to use them and
for several, you don't even have to directly use them at all and instead
just need to know they exist.

For all of the classes listed below, I'll mention which two require
specialization here and in a following section, where the specialization
will be described in detail. Note that *all* of the *BES* classes have
*BESObj* as a parent, so all can passed using a pointer to a BESObj.

BESCommandInterface
Used to pass information from a method you've written in a child of
BESRequestHandler to a response handler

BESResponseHandler
This class builds a BESResponseObject and then transmits it.

BESDapResponseObject and the various children
These holds specific types of responses for the DAP

BESTransmitter
Used by BESResponseHandler to transmit a ResponseObject. For DAP, the
BESBasicHttpTransmitter class is actually used.

BESCatalog
A catalog is a way of getting listings (catalogs) of things

BESCatalogDirecotry
A catalog for a disk directory. This is used to build the Hyrax server's
directory pages.

BESCatalogList
This class is used to for a list that the BES searches when it needs to
find a specific catalog that's mentioned in a command. It's one of the
singleton objects discussed later

BESContainerStorageCatalog
Not really sure...

BESContainerStorageList
A list of ...

### Classes that must be Specialized

There are two classes that must be specialized to build a new DAP data
handler:

BESAbstratModule
This class defined the interface for teh BES handler (aka Module). It
contains two key methods: *initialize()* and *terminate()*

BESRequestHandler
This class is (typically) specialized to include five new methods for a DAP data handler, one for each of the DAP response objects (DAS, DDS, DataDDS, version, and help). The constructor for the specialization (again, typically) uses BESRequestHandler::add_handler(...) to bind each of the five methods to a string so that the BES can figure out which of the methods should be called to handle a request for one of the five DAP responses

### BES Framework Singleton Objects

The BES uses singleton objects to store lists of things that will be
searched to sort out how a given request should be handled. In some ways
these lists are like symbol tables in a compiler or interpreter. Once an
object is defined and bound to a name it can be added to the correct
list and later found and used as commands are parsed and executed. The
singleton classes shown in the UML diagram are:

BESRequestHandlerList
A list of known request handlers

BESCatalogList
A list of the known catalog objects

BESContainerStorageList
A list of the known container objects

The specialization of BESAbstractModule populates these lists, first by
making a specialized instance of BESRequestHandler and then adding that
to the request handler list. once that's done, the container storage and
catalog lists are used to store their respective object types. These
three activities are carried out by the module's *initialize()* method.
By adding the request handler object to the global (singleton) request
handler list, a handler's code to process certain requests is mad
available to the BES's command processing software. Similarly, any
containers or catalog objects defined by a handler are added to the
corresponding global lists and can thus be accessed by the command
processing software.

## Build a null Handler

Use the *besCreatModule* script to build a simple DAP module with now
new commands or responses.

1.  Create a directory, something like ncml_module
2.  from the command line run besCreateModule
3.  Answer the questions as follows:
    1.  ncml
        - This will be the name of the module
    2.  NCML
        - This is the prefix to all of the classes that get created. For
          example, NCMLModule
    3.  <enter>
        - You are not handling any other responses, just the dap2
          responses
    4.  <enter>
        - You are not adding any additional commands to your module

Running besCreateModule will create all the files that you will need for
the NCML module. This includes:

- NCMLModule - this class implements the initialize and terminate
  methods for you, registers the request handler for this module, and
  registers a debug context for the module called "ncml". With this
  debug context you can add debug statements to your code as follows:

`BESDEBUG( "ncml", "some debug info " << somevar << endl )`

The second part of the function would be something that would be sent to
a C++ ostream object like cerr or a fstream.

A terminate method is also created and removes the request handler.
Check out the code. This is where specialized extensions would be added
to the BES Framework.

- NCMLRequestHandler - this class is where the NCML file will be parsed,
  containers retrieved, etc... Please refer back to the main
  documentation of AIS for detailed design.

In addition to the classes being build, a configure.ac, Makefile.am,
sample COPYING, COPYRIGHT, NEWS and README files are created. A sample
.spec file is also created for building the rpm for this module. The
script bes-ncml-data.sh is created, which is used to add this module to
the BES configuration file.

Once all of the files are created, configure is run and the code is
built. It should all build properly.