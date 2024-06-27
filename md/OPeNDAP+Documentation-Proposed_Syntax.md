### Proposed Syntax

There are at least two good ideas floating around for a syntax for
building the server-side function URL -- the one used by the F-TDS/GDS
servers and the one used by Ingrid. Because the Ingrid function syntax
as build from scratch it doesn't carry any of the baggage of the
GDS/F-TDS dependencies on GrADS and Ferret. But, since we have a lot of
experience and service build on top of GDS and F-TDS we should retain
those parts that make that parsing easy for those services. Therefore,
we propose we adopt the following hybridization of these two.

1.  The native function syntax will be that of Ingrid.
2.  The function expression will be separated from the data URL with the
    string "_expr_".
3.  A set of {} braces will be used to list other data sources needed
    for the calculation. (This can be empty).
4.  The function specification will in the second set of curly braces
    using the notation from Ingrid.
    - Multiple "lines" of commands will be separated by a ";"
5.  Native script statements can be mixed into the list of statements
    into the list of function command pre-pended with the string
    "native:".

For example:

`http://f-tds.noaa.gov/tds/dodsC/data/sst.jnl_expr_{}{native:let sst_x5=[d=1, sst*5.0];sst_5x/(170W)(120W)RANGE/Y/(5S)(5N)RANGE[X/Y]average/}`

### Alternative proposal

[RobDeAlmeida](User:RobDeAlmeida "wikilink") proposed a different
approach, based on the existing syntax for server-side functions.
According to the latest draft of the DAP 2 spec, functions are called
using the following syntax:

`http://server.example.com/dataset.dods?function1(arg1,...,argn)`

Functions can appear both on the selection or the projection; when
applied to the projection, like in the example above, they should return
a new DAP variable on the dataset root. The proposed alternative is to
allow POSTing scripts to the server, which can later be referenced
through server-side functions. This functionality is better described
below in an hypothetical example.

#### Example

On this scenario, first the client requests the capabilities of the
server. This returns a XML file which described the supported functions,
together with the supported backends for server-side processing. The
advantage of this approach is that a server could support multiple
backends, like Ferret, GrADS and Python. The capabilities response could
look like this:

<capabilities base="http://server.example.com/capabilities">
`  `<backends>
`    `<backend name="ferret" type="text/x-ferret"/>
`    `<backend name="python" type="text/x-python"/>
`  `</backends>
`  ...`
</capabilities>

This server would support both Ferret and Python scripts, eg. The client
proceeds with a POST:

`POST /capabilities`
`Host: server.example.com`
`Content-type: text/x-ferret`

`let sst_5x=[d=1, $1*5.0];`
`let output=sst_5x[x=170w:120w@ave,y=5s:5n@ave]`

And the server responds:

`201 Created`
`Location: `[`http://example.com/capabilities/function1`](http://example.com/capabilities/function1)

`function1`

Here we have the location of the newly created function; by visiting
that URL the client can read the script associated with the function.
The body of the response also includes the name of the newly created
function, which could be encoded in a XML document.

Finally the client does a normal OPeNDAP request, calling the
server-side function:

` `[`http://server.example.com/sst.jnl?function1(sst)`](http://server.example.com/sst.jnl?function1(sst))

This would load the dataset (sst.jnl) and pass the variable sst as the
first parameter to the script, returning the output variable at the end
of the processing.

The benefits of this proposal are:

1.  No additional syntax is necessary, using the already defined syntax
    for server-side functions.
2.  Servers can support multiple backends, exposed through a common API.
3.  Scripts can be as long as the user want, while encoding it in the
    URL is limited to as low as 2k characters, depending on the
    client/server.
4.  Scripts can be easily reused and shared, since they are tied to a
    function name.