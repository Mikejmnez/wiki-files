\[Most recent modifications are either underlined or struck out\]

## Characterization of URL Annotations

Requests for data using the DAP4 protocol will require a significant
number of annotations specifying what is to be retrieved, commands to
the server, and commands to the client.

This document is intended to just describe the information with which
URLs need to annotated based on past experience. It also enumerates the
possible URL components that can be used to encode the annotations. I
will consider a specific encoding in a separate document.

Looking at the DAP2 URLs, we see three classes of annotations: protocol,
server commands, client commands, and queries (aka constraints).

### Protocol

For DAP2, the fact that the protocol being used is inferred from
context; in netcdf-C, for example, the fact that the dataset name is a
URL is sufficient to indicate the use of the DAP2 protocol. For some
servers, such as TDS, the protocol is also inferrable from elements of
the URL path. For example, in <http://>.../thredds/dodsC/... the "dodsC"
indicates the use of the DAP2 protocol. TDS also supports a schema
called "dods:" that also indicates the use of DAP2.

### Server Commands

Server commands in DAP2 are appended to the dataset URL to indicate
attributes of the request. For example:

[`http://test.opendap.org/dataset.nc.dds`](http://test.opendap.org/dataset.nc.dds)

The defined kinds of server commands for DAP2 are as follows:

1.  Component requests: ".dds", ".das"
2.  Data requests with format: ".dods", ".asc"
3.  Miscellaneous: ".html", ".ver"

### Client commands

Client commands are interpreted by the client-side library to specify
actions to be performed by the library. The existence of client commands
is important because we want to communicate from the user to the library
without requiring any knowledge by intermediate code layers. For
example, netcdf C tools such as ncdump sends URLs to the underlying
netcdf DAP2 library without having to be cognizant of their structure.

<ins>Currently, there are two primary uses for client commands.</ins>

1.  <ins>Debugging - cause the DAP library to output information about,
    for example, the specific requests it sends to the server.</ins>
2.  <ins>Caching - control the degree of caching and prefetch to be used
    with a given request to the server.</ins>

Currently, client commands are represented as "name=value" pairs or just
"name" enclosed in square braces: "\[nocache\]", for example. These
commands are prefixed to the URL such as this.

`[show=fetch]`[`http://test.opendap.org/dataset.nc`](http://test.opendap.org/dataset.nc)

The legal set of command is library specific.

One notable problem with this form of client command is that it
prevented generic URL parsers from parsing the URL because, of course,
the square bracket notation is non-standard.

<ins>An alternative to using client commands in the URL is to use a
configuration file (often referred to as the *.rc* file such as
*.dodsrc*). This configuration file is assumed to be either in the
caller's home directory or in the current working directory. It contains
the necessary client commands to be applied. It is mildly less
convenient for the user to use a .rc file than to embed a client command
in the URL. </ins>

### Queries

The third class of URL annotations specifies some form of query to
control the information to be extracted from a dataset on the server.
This information is then passed back to the client.

In DAP2, queries consisted of projections and selections specifying a
subset of the data in a dataset.

A projection represents a path through the DDS parse tree annotated with
constraints on the dimensions. This, for example.

`?P1.P2[0:2:10].F[1:3][4:4]`

A selection represented a boolean expression to be applied to the
records of a sequence. Syntactically, a selection could cross sequences,
thus implying a join of the sequences, but in practice this was not
allowed.

DAP2 queries also allowed the use of functions in the projections and
selections to compute, for example, sums or averages. But the semantics
was never very well defined. The set of allowable functions is server
dependent.

### Annotation Mechanisms

DAP4 will need to support at least the three classes of annotations
described above. Whatever annotation mechanisms are chosen, the
following properties seem desirable.

1.  The resulting URL should be parseable by generic URL parsers

    =\>Client commands should be embedded at the end of URLs, not the
    beginning.
2.  Whatever annotation encoding is used, it is desirable if it is as
    uniform as possible.

As mechanisms, we have the following available to us:

1.  The URL schema -- "http:" for example, or the TDS "dods:" schema.
    Using this is somewhat undesirable because it would need to encode
    also an underlying encrypted protocol like https: (versus http:).
2.  URL path elements such as the current use of e.g.
    <http://host/>../dodsC/... by TDS.
3.  URL query -- everything after the first '?' in the URL. URL queries
    technically have a defined form as name=value pairs, but in practice
    are pretty much free form.
4.  URL fragment -- everything after the last '#'. Again these are
    pretty much free form.
5.  Filename extensions -- everything between the data set name in the
    path and the start of the query. The DAP2 ".dds" and ".dods" are
    examples of this.
6.  <ins>Alternate extension formats. Ethan Davis has proposed the use
    of a "+" notation instead of filename extensions: +ddx+ascii, for
    example. This has the advantage of clearly not being confused with
    filename extensions while also making clear the additive nature of
    such annotation.</ins>

I should note that the Ferret server has taken to seriously abusing the
URL format with URLs like this.

[`http://`](http://)`.../thredds/dodsC/hfrnet/agg/6km_expr_{}{let deq1ubar=u[d=1,l=1:24@ave]}`

so we have much to aspire to :-)

*-Dennis Heimbigner*