# Overview of the OPeNDAP Server Architecture

This appendix describes in functional terms the operation of a server.

Note that descriptions of server outputs naturally and unavoidably refer
to certain kinds of inputs and vice versa. This makes it difficult to
create definitions without forward and backward cross-references. Please
be prepared to read this specification with a \htmlonly{virtual} thumb
between the pages as you flip back and forth from the output to the
input sections.

## Outputs

A server sends to a client one of three different sorts of messages:

- HTML;
- ASCII data; or
- Binary data.

### HTML Data

There are three different kinds of HTML data returned by a OPeNDAP
server. Clients that request these data should be prepared to display
the HTML to the user.

Server Information (<font color='green'>usage</font>) : One kind of HTML data is returned in response to a request for server information (using the <font color='green'>.info</font> URL suffix), and contains usage information for the server, including a formatted version of the dataset DAS and DDS. This information is formatted by the <font color='green'>usage</font> service program, invoked by the server.

<!-- -->

WWW Interface : The forms-based OPeNDAP WWW interface returns HTML data to the client. This can either be a form a user can use to create a OPeNDAP contraint expression or a OPeNDAP directory listing, depending on whether the URL indicates a file with the <font color='green'>.html</font> suffix, or a directory containing other files.

<!-- -->

Error messages : A badly formed URL will result in a OPeNDAP error message, which is simply some HTML text describing the supported URL suffixes. (See <font color='green'>OPeNDAP_Dispatch.pm</font>.) Note that though an error message could in theory be returned to any client, whether or not they can display HTML, in practice, only web browser clients are prone to these kinds of errors. Aside from web browser clients (e.g. Netscape), the OPeNDAP clients issue their server requests through the OPeNDAP client core libraries, which format the URL according to the OPeNDAP conventions.

> NOTE: There are two kinds of OPeNDAP error messages. The particular
> one described above is issued in response to a badly formed URL. Other
> kinds of errors are returned as ASCII data within a OPeNDAP data
> document. See ([Section 6.1.3](ProgrammerGuideChapter6 "wikilink")).

### ASCII Data (Text)

These are the different kinds of ASCII data returned by a fully-equipped
OPeNDAP server. ("Fully equipped" implies that the server has, on its
execution path, all the supported OPeNDAP service programs.)

DDS : The OPeNDAP Data Descriptor Structure is a description of the data contained in the dataset. It contains information about the data types and names represented in the dataset. Programmers can think of the DDS as containing the type declarations for the data. The DDS is fully described in \[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The OPeNDAP User Guide</cite>\] . The DDS is created with a service program called <font color='green'>\*_dds</font>, where the <font color='green'>\*</font> is the same two-letter abbreviation used in the server name (<font color='green'>nph-\*</font>).

<!-- -->

DAS : The OPeNDAP Data Attribute Structure contains information *about* the data in the dataset---the metadata. It is a hierarchical list of name-type-value triples, where the names of the containers correspond to entries in the DDS. The DAS is fully described in \[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The OPeNDAP User Guide</cite>\] . The DAS is created with a service program called <font color='green'>\*_das</font>, where the <font color='green'>\*</font> is the same two-letter abbreviation used in the server name (<font color='green'>nph-\*</font>).

<!-- -->

ASCII Data : If a OPeNDAP server is equipped with a service program called <font color='green'>asciival</font>, it can convert binary data to ascii data on the fly, allowing you to use a standard web browser (such as Netscape) to examine data. This feature can also be used to import data into a client that may not be able to process OPeNDAP data encoded in the standard binary format.

### Binary Data

The OPeNDAP data document consists of two parts, a DDS describing the
data returned, and the data itself. It may also consist of ASCII data
describing an error condition.

DDS : The DDS returned with the data differs slightly from the DDS returned by a simple request using the <font color='green'>.dds</font> URL suffix. Whereas the DDS returned in response to that URL is a description of the dataset available, the DDS returned in a data document describes the data being returned. If you ask for an entire dataset, there will be no difference between these two DDS's. However, if you

request only a *part* of a dataset, the DDS included in the data
document will only reflect the part of the dataset returned to you.

: As an example, consider a dataset containing an array with one hundred elements. The DDS for this dataset will contain an array with one hundred elements. However, if you send a server a request for just the first fifty elements of the array, the OPeNDAP data document returned will contain a DDS with only fifty elements. This is illustrated in [figure 6.1.3](:Image:DataDDS.gif "wikilink").

<!-- -->

Data : The data requested from a server is (unsurprisingly) also contained in the OPeNDAP server's response to a data request. The data is encoded using the XDR standard (eXternal Data Representation), and packed into the second part of the response document. For more information on XDR, see \[<http://www.faqs.org/rfcs/rfc1014.html><cite>Internet RFC 1014</cite>\]. For further details, see ([Section 6.1.3](ProgrammerGuideChapter6 "wikilink")), below.

<!-- -->

Error Messages : A OPeNDAP data document may contain an error message. This is a OPeNDAP Error object (containing an ASCII error message and some other data) stuck into the HTTP document where the data would usually go. This is where error conditions on the server are noted, as well as badly formed constraint expressions and file names.

<center>

<figure>
<img src="DataDDS.gif" title="Image:DataDDS.gif" />
<figcaption>Image:DataDDS.gif</figcaption>
</figure>

The OPeNDAP Data Document and the DDS

</center>

The OPeNDAP Data Document and the DDS. For the dataset containing the
vector <font color='green'>temp</font>, with 100 elements, the top
"Simple DDS Request" shows what the DDS might look like for that
dataset. The bottom "OPeNDAP Data Document" shows what might be returned
by a request for all the even-numbered elements of the
<font color='green'>temp</font> array. Note that the DDS has been
altered to allow for the reduced number of elements in the returned data
array.

The HTTP response containing the OPeNDAP data document is formatted like
this:

    HTTP/1.0 200 OK
    XOPeNDAP-Server: <server/dap version string>
    Content-type: application/octet-stream
    Content-Description: dods-data
    Content-Encoding: deflate
    \var{<blank line>}
    \var{<DDS>}
    Data:
    \var{<binary data, each variable given in the order it is listed in the DDS>}
    \var{<EOF>}

Note the following:

- OPeNDAP servers use HTTP 1.0.
- The version string (after the
  <font color='green'>XOPeNDAP-Server:</font> header) must be
  <text>/<version number> where version number is x.y or x.z.y. This
  version number is parsed on the client side to ensure that a client is
  communicating with a compatible server.
- The <font color='green'>Content-Encoding</font> is used only when the
  document is compressed using \`deflate'. (This is the same as the
  compression used by the <font color='green'>gzip</font> program, and
  is implemented in <font color='green'>libz.a</font>.
- <font color='green'>Content-Description</font> is used by the OPeNDAP
  client to figure out if the object is an error instead of a data
  object. If it is, the <font color='green'>Content-Description</font>
  field should read <font color='green'>dods-error</font>.

#### Encoding the DAP Data Types

The OPeNDAP transmission protocol spearates variables into three
classes: scalars, arrays, and sequences. A scalar is included in the
OPeNDAP data document simply by writing its XDR representation. An array
is sent by writing the number of elements (as an XDR
<font color='green'>int</font>) followed by the elements themselves,
also in the XDR format. However, arrays of Byte, Int16, Int32, UInt16,
UInt32, Float32, and Float64 actually have their size sent twice, once
by the DAP software and once by the XDR software. Here's a hex dump of
some Int32 array data that shows this behavior:

    00000000: 4461 7461 7365 7420 7b0a 2020 2020 496e  Dataset {.    In
    00000010: 7433 3220 755b 7469 6d65 5f61 203d 2031  t32 u[time_a = 1
    00000020: 5d5b 6c61 7420 3d20 315d 5b6c 6f6e 203d  ][lat = 1][lon =
    00000030: 2032 315d 3b0a 7d20 666e 6f63 313b 0a44   21];.} fnoc1;.D
    00000040: 6174 613a 0a00 0000 1500 0000 15ff fff9  ata:............
    00000050: 40ff fff6 6fff fff3 e5ff fff1 ffff fff3  @...o...........
    00000060: 4aff fff6 9aff fffb 1c00 0002 9600 0009  J...............
    00000070: b300 000b 5e00 000b 0300 000b 8200 000a  ....^...........
    00000080: b900 000a ae00 000b 7300 000a 2900 0008  ........s...)...
    00000090: 5b00 0007 3500 0006 da00 0007 6900 0007  [...5.......i...
    000000a0: 3e                                       >

The length bytes for the <data> section start at address 0x45 (The
length is 0x00000015.) and is repeated at 0x49. Here's how you can see
this:

    geturl "http://dods.gso.uri.edu/cgi-bin/nph-nc/data/fnoc1.nc?u[0:0][0:0][0:20]"

The DDS for this dataset is (use <font color='green'>geturl -d</font>):

     Dataset {
        Int32 u[time_a = 16][lat = 17][lon = 21];
        Int32 v[time_a = 16][lat = 17][lon = 21];
        Float64 lat[lat = 17];
        Float64 lon[lon = 21];
        Float64 time[time = 16];
    } fnoc1;

Structures and Grids are sent by serializing their components, one by
one, in the order of their declaration in the DDS.

Sequences have a much more complex encoding scheme because one sequence
may contain another sequence *and* because the sequence type provides no
information about the length of a given instance. To send sequences the
DAP uses two different algorithms, one up to OPeNDAP version 2.14
servers and clients and a better (more compact one) after that. OPeNDAP
clients at version 2.15 and later can all read from both servers *but*
they assume that a server that does not announce its version is very old
and will attempt to read Sequences using the old algorithm. (So if
you're writing a new OPeNDAP server that serves Sequences, you must
remember to address the version requirement.)

Here's how the new algorithm encodes a sequence:

1.  Serialize row of the sequence by:
    1.  Writing the start of instance marker (0x5A), and
    2.  Serializing (writing the XDR encoding of) each element in the
        row in the order of appearance, then
2.  Write the end of sequence marker (0x5A).

## Inputs

The input to a OPeNDAP server is contained in an HTTP "GET" request.
Unlike a POST, the information in this kind of request is all in the
URL. Consequently, examining the parts of a OPeNDAP URL will illustrate
all the different sorts of requests a OPeNDAP server can handle.

Figure 6.2 contains a description of the parts of a OPeNDAP URL, not
including the "Constraint Expression." The constraint expression and the
parts of the OPeNDAP URL are described in detail in
\[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The
OPeNDAP User Guide</cite>\]\[constraint\] and
\[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The
OPeNDAP User Guide</cite>\]\[url-parts.html\].

    > http://dods.gso.uri.edu/cgi-bin/nph-nc/data/fnoc1.nc.das

    ^     ^                        ^      ^    ^        ^

    |     |                        |      |    |        |
    Protocol |                        |      |    |        |
    Machine Name                      |      |    |        |
    Server-----------------------------      |    |        |
    Directory---------------------------------    |        |
    Filename---------------------------------------        |
    URL Suffix----------------------------------------------

}

<center>

Parts of a OPeNDAP URL (without a constraint expression)

</center>

### Request types

A OPeNDAP server is equipped to respond to several different request
types. Each request type is signified by a different URL suffix. The
server itself is just a dispatch script, that determines the type of a
request, and dispatches the request to the appropriate service program,
and relays its result back to the client.

In [figure 6.2.1](:Image:arch.gif "wikilink"), a OPeNDAP client makes a
GET request to a OPeNDAP server (which is just an
<font color='green'>httpd</font> daemon equipped with a bunch of CGI
programs). The daemon invokes the OPeNDAP Server, which is a
simple-minded dispatch script which in turn invokes the DAS, DDS, Data,
or other service program.

<center>

<figure>
<img src="arch.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Architecture of a OPeNDAP Data Server

</center>

The request types, their suffixes, and the service programs are listed
in \tableref{server-spec,suffix}.

| **Suffix**                       | **Service Program**                 | **Description**                                                                                                                              |
|----------------------------------|-------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| <font color='green'>.dds</font>  | <font color='green'>\*_dds</font>  | Returns the Data Descriptor Structure for the specified dataset.                                                                             |
| <font color='green'>.das</font>  | <font color='green'>\*_das</font>  | Returns the Data Attribute Structure for the specified dataset.                                                                              |
| <font color='green'>.dods</font> | <font color='green'>\*_dods</font> | Returns binary data in the form of a OPeNDAP data document. See ([<cite> pguide,server,binary</cite>](http://www)).                          |
| <font color='green'>.asc</font>  | <font color='green'>asciival</font> | Converts data requests to ASCII values before sending them back to the client. This service is useful for invoking from simple web browsers. |
| <font color='green'>.info</font> | <font color='green'>usage</font>    | Returns an HTML formatted version of the dataset DDS and DAS, and any other server and dataset information provided in \*.ovr files.         |
| <font color='green'>.html</font> | none                                | Returns a URL constraint expression builder form, based on the dataset DDS and DAS. This is the OPeNDAP WWW interface.                       |
| <font color='green'>.ver</font>  | none                                | Returns the server version information.                                                                                                      |

### Constraint expressions

A OPeNDAP server can accept a "constraint expression" contained in the
URL query string. The OPeNDAP constraint expression describes how a
OPeNDAP server should subsample a OPeNDAP dataset before sending the
data back to the client. The details of the constraint expresssion
syntax are covered in
\[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The
OPeNDAP User Guide</cite>\]\[constraint\]. What's important here is
simply that the constraint expression is a logical expression with two
clauses: projection and selection.

The "projection" clause of a constraint expression specifies the data
variables requested by the client, and the "selection" clause specifies
the condition under which the client wants them. That is, the projection
clause might specify that the client wants to see oceanic temperature
data, and the selection clause would specify that only records from
below 1000 meters should be returned.

### Server functions

Within the context of a constraint expression, a server can implement
functions a client would use to specify data. Since the constraint
expression has two kinds of clauses, there are two kinds of server
functions: projection and selection.

To implement another server-side constraint function, see . Following is
a list of the canonical server-side functions implemented in all OPeNDAP
servers.

#### Selection

yes

#### Projection

yes