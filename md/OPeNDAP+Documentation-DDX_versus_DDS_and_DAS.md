In the current set of data handlers, the DDS and DAS objects/responses
are built separately but in the future all of that information should be
extracted at once from the data source and used to build the DDX. How
then should backward compatibility be achieved?

In the current data handlers, the DAS and DDS objects are built when
requested and serialized to generate the 'network representation' of the
object (i.e., the response). Most of the handlers work this way,
although the HDF4 handler builds both objects and caches them, with one
caveat to be discussed later.

There are at least three problems with this approach:

1.  Most clients want the information in both the DDS and DAS at just
    about the same time, so most clients wind up making two requests for
    information that's often to be used in the same part of their code.
2.  Most of the handlers don't actually build DAS objects that correctly
    match the corresponding DDS objects, thus 'cross walking' the DDS
    and DAS for a data source is often a hit or miss operation.
3.  A third issue has arisen now that server-side functions are becoming
    more important - the DDS/DAS combination is used by these functions
    because several (e.g., geogrid) use attributes to determine units,
    constant values, et c., for different variables.

The DDX was designed to address the first two of these problems, but
resources to modify the handlers is so limited that instead of changing
the software to build and return the DDX, we've compromised and adapted
the existing DAS/DDS software so that first the two objects are built
independently and then the DAS is 'merged into' the DDS. This forms an
object in memory that holds all of the information that is required to
form the DDX response.

The problem with this solution is that it fails to address the
underlying issue - that DAS objects are often malformed. Heuristic code
is used to merge the DAS object into a DDS that 'matches' in the sense
that both are from the same data source. The code is good enough to
build the correct object most of the time, but not always.

### A better way to build the DDX

<img src="BuildDASDDS.png" title="BuildDASDDS.png" width="200"
alt="BuildDASDDS.png" /> for the rest of this discussion, it's best to
separate the object (which is a binary object instantiated in a C++
program) from the response, which is a representation of that object
encoded in XML. The current DDS object implemented in libdap can be used
to generate the DDX response.

The current handlers need to be modified to integrate reading the data
type and name information about the variables with reading the attribute
information about those variables. Then the attribute information can be
bundled with the correct variable at the outset. This is currently
supported by the software in libdap.

Because the DDS class in libdap contains methods to print the DDX,
there's no additional work to do to build that response once the above
change is made to the handlers so that they read and store attribute
information for each variable at the time the variable is added to the
DDS object.

### Building the DDS and DAS using XSLT

<img src="BuildonlyDDX.png" title="BuildonlyDDX.png" width="200"
alt="BuildonlyDDX.png" /> If the DDX response has been built, then it
should be possible to use that along with suitable XSLT code to build
the corresponding DDS and DAS responses. This will allow us to
eventually phase-out support for the DAS and DDS responses. Initially we
will be able to remove the functions used to build the ASCII text that
is the response and at some point we might be able to eliminate the
somewhat error-prone parsers used to read the responses. The latter will
only be possible when the DDX is nearly universally supported by
servers.

### Caching the binary object

Some code, like the HDF4 handler, caches the DDS/DAS objects by
generating their 'responses' and writing those to disk. Unfortunately,
this presents a problem when using those cached responses to instantiate
objects that are then used for form a data response: Extra information
needed to build the data response that is part of the binary DDS/DAS
objects but not in the response will be missing. Thus the cached
responses cannot be fully used.

To address this the handlers (or the server?) should use a cache for the
binary C++ objects - some sort of a persistent store - that can
correctly cache the *objects* and not the DAP responses. In this case,
information read from a data source could be used many times for both
the 'metadata' responses and for data responses. Because this would be
implemented outside a given handler's logic, it could be used by all
handlers.