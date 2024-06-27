## Proposed Capabilities

We propose the following set of capabilities for servers that want to
expose server-side functions to OPeNDAP clients. Clearly, some servers
will go well beyond this and some of these requirements are identified
as optional. One important requirement will be to implement the
"getCapabilities" service so that clients can query the server about
what it can do.

### Capabilities Service

Servers will be able to respond with [listing of
functions](#Standard_Functions "wikilink") and its [capabilities as a
client](#Server_as_Client "wikilink"), and other information to be
determined.

1.  How do we do this via OPeNDAP?
2.  Is is adequate for this to be a separate service with an XML
    document as a response?

### Server as Client

Optionally servers will be able to act as an OPeNDAP client. This
capability means that the server can read from other OPeNDAP data
sources and use them as part of the server-side function calculation.
This capability is implemented as part of
[F-TDS](Current_Implementations#The_Ferret-Thredds_Data_Server_.28F-TDS.29 "wikilink")
and
[GDS](Current_Implementations#The_GrADS_Data_Server_.28GDS.29 "wikilink")
via a list of data sets in the first set of curly braces.

### Standard Functions

The Ingrid server currently has
\[<http://iridl.ldeo.columbia.edu/dochelp/Documentation/funcmenu.html>\|
166 functions\] available. Presumably, F-TDS and GDS duplicate many of
these functions as part of the standard analysis capability of the
underlying implementations (Ferret and GrADS). I think the task for this
section should be to winnow the Ingrid list into a manageable collection
of functions for our first cut.

### Native "scripts"

For those servers which are implemented on top of script-able
command-line analysis programs or interpreted languages it would be
extremely useful for client to pass in a script to perform a function
defined by the client. The current implementations of
[F-TDS](Current_Implementations#The_Ferret-Thredds_Data_Server_.28F-TDS.29 "wikilink")
and
[GDS](Current_Implementations#The_GrADS_Data_Server_.28GDS.29 "wikilink")
only have this capability (that is all server-side function requests
must be expressed in the native command language of the underlying
analysis program).

### Security

Because command-line analysis programs and interpreted languages have
commands to "shell-out" to the underlying shell implementations should
"clean" their input to disallow these commands, or run in a mode that
disables the ability to "shell-out" or both.

### Variable Naming

When a server-side function is applied to an OPeNDAP request often the
net result is to create a "virtual" variable in the referenced data set.
Implementations should allow the client to specify the name of the new
variable as part of the request, but in the absence of a name the
variable will named following a convention of existing variable names
separated by "_", function name, affected axis and axis ranges all
separated by "_". For example: SST_AVERAGE_X_170W_120W_Y_5S_5N.