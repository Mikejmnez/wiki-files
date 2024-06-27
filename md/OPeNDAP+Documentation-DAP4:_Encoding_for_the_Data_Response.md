[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

There are two different approaches to deserializing the data received by
a DAP client: The client may process the data as it is received (i.e.,
eager evaluation) or it may write those data to a store and process them
after the fact (lazy evaluation). A variant of these techniques is to
process the data *and* write it to a store, presumably because the
initial processing steps are useful while having the data stored for
later processing enables still other uses. However, in this document I'm
not going to look at the latter case because experience so far with DAP2
has not provided any indication that would present any performance
benefits. We do have example clients that use both eager and lazy
evaluation.

[HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616.html) defines a
chunked transport scheme. In the past we have spent a fair amount of
time on the notion of chunking as a way to achieve reliable transmission
of errors when those errors are encountered during response formulation,
that won't be addressed in this document. Instead, this document will
assume that the entire response document described here is *chunked* in
a way that enables reliable transmission of errors. The details of that
*[transfer
encoding](http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.6)*
will be described elsewhere.

NB: This proposal covers only the DAP4 data response; it does not apply
to the other responses including, e.g., an ASCII data response.

## References

I found these useful and thought they might be better not lost at the
end of a long document.

1.  [HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
2.  [WikiPedia on Endianness](http://en.wikipedia.org/wiki/Endianness)
3.  [Multipurpose Internet Mail Extensions (MIME) Part One: Format of
    Internet Message Bodies](http://tools.ietf.org/html/rfc2045)
4.  [WebSockets](http://tools.ietf.org/html/rfc6455)
5.  [W3C WebSockets](http://dev.w3.org/html5/websockets/)
6.  [Javascript Worker
    Threads](http://bradkellett.com/p/javascript-worker-threads/) (I
    wish there were a better reference than a blog post)

## Problem addressed

There is a need to move information from the server to a client. The way
this is done should facilitate many different designs for both server
and client components.

Assumptions:

1.  Since DAP is so closely tied to the web and HTTP, its design is
    dominated by that protocol's characteristics.
2.  Processing on either the client or the server is an order of
    magnitude faster than network transmission.
3.  Server memory should be conserved with favor given to a design that
    does not require storage of large parts of a response before it is
    transmitted (but *large* is a relative term).
4.  Clients are hard to write and the existence of a plentiful supply of
    high-quality clients is important (of course, servers are hard to
    write, too, but there are between 5 and 10 times the number of DAP2
    clients as servers).
5.  The response does not explicitly support a real-time stream of data
    (e.g., a temperature sensor which is a data structure of essentially
    infinite size). It may, however, be the case that the response can
    encode that kind of information.

Broad issues:

1.  It should be fast
2.  It should simple
3.  ~~It should be part of the web - meaning that the XML part(s) should
    be identifiable/useable by generic web software even though the
    binary data part will be completely opaque.~~ At the 8/28/12 meeting
    we decided to drop this goal, as part of the move to a simpler
    encoding scheme based solely on chunking the response (and not using
    MIME).

## Proposed solution

The response is the server's answer to a request for data from a client.
Each such request must either include a *Constraint Expression*
enumerating the variables requested or a null CE that is taken to mean
'return the entire dataset.' The response document will have a MIME type
of *application/vnd.opendap.org.dap4.data*. The payload of the response
will be opaque to clients not aware of DAP4. Furthermore, the contents
of the payload will use the chunked encoding scheme described in [DAP4:
Chunked encoding](DAP4:_Chunked_encoding "wikilink") so that it will be
straightforward for clients to detect errors in the response. In
addition, web browsers acting as clients will (typically) save a
response with this MIME type to a disk file and clients can be written
to read those files. A response will consist of two (logical) parts:

1.  A Dataset Metadata Response (DMR) that has no attribute information
    and contains (only) the variables requested; and
2.  A binary part (BLOB) that contains the data for those variables

The overall structure of the response is like any other HTTP response; a
HTTP response code, a set of MIME headers that convey information about
the response for the HTTP software layer, a blank line separating those
headers from the *payload*, and the payload itself. The DataDMR is that
payload and nothing in the HTTP response preamble (the response code or
MIME headers) is needed to deserialize the response. However, it is
useful to make use of the HTTP response document's headers to streamline
integration with the larger web infrastructure. There are two groups of
headers described here: The required headers and headers we have found
to be useful. Note that these headers are not part of the response
payload. They are used by the HTTP transport software, some of which may
be completely separate from either the DAP4 client or server involved in
the data access operation (e.g., a *Squid* cache installed by a
university department).

### Required headers

The headers that are required are:

Date: This header identifies when the response was generated.
Last-Modified: This is the time that the referenced object (the thing in the payload) was last 'modified'. For DAP4, this is the more recent time that any of the values in the response changed. Practically, it is the last modified time of the file or table that holds the data. This is important for caching systems.
Content-Type: This identifies the 'mime type' of the payload. For the DataDMR response this should be *application/vnd.opendap.org.dap4.data*.
X-DAP: The version of the DAP protocol implemented by the software that wrote the payload. Not actually needed since the same information is in the DMR document, this can be useful for clients since most HTTP libraries make *experimental* headers easy to access. Having the protocol of the payload known in advance will simplify parsing the XML component of the response.

### Useful headers

The headers that we have found useful are:

Content-Encoding: this describes an encoding applied to the payload by (typically) a web server such as Tomcat. In almost all cases this will be added to a response as it 'goes out the door' and is not something that the DAP4 server software needs to be concerned with. However, if a server was implemented so that it formed the entire response, it would need to use this header if it wanted to use a transfer encoding like *gzip* and have HTTP client software perform the decoding.
Cache-Control: Tells all caching mechanisms from server to client whether they may cache this object. If a response results in an error and that is detected before the headers are written, it is good practice to suppress caching that response.
Expires: tells a cache how long the response is considered 'fresh'. Both Cache-Control and Expires can improve performance of caching systems. Using this header can eliminate unnecessary conditional GET requests.
Server: this provides a way for the response to indicate information about the server software implementation.

``` html
HTTP/1.1 200 OK
Date: Mon, 23 May 2005 22:38:34 GMT
Last-Modified: Wed, 08 Jan 2003 23:11:55 GMT
Content-Type: application/vnd.opendap.org.dap4.data
Content-Encoding: gzip
X-DAP: <<DAP version>>

<<Blank line>>

<<Hex chunk info - this provides the offset to the data>>

<<DMR here>>

<<Blank line>>

<<Binary part>>
```

### Structure of the DMR Part

The start of the Data Response document follows the blank line
separating the HTTP response MIME headers (i.e., it is the *payload* of
the response document). Here's an example response document in its
entirety. Note that following the DMR (which is a kind of XML document),
a *blank line* is used to indicate the start of the binary part. Since
the DMR must be, at minimum, a well-formed XML document, it is possible
to know where it ends and identify the blank line separating the DMR
from the binary part.

### Structure of the binary part

The binary part of the payload follows a blank line. Essentially this is
the serialization form of DAP2, but with some important changes to the
binary part:

1.  DAP4 will use a *receiver-makes-right* encoding scheme where a
    four-byte preamble will be used to indicate the byte-order of the
    data values
2.  No padding will be used for data values
3.  Each *top-level* variable will be followed by a checksum
4.  Floating point values will use the nearly ubiquitous IEEE 754
    standard.

Differences between the binary encoding scheme of DAP4 and DAP2 include:

1.  DAP2 used XDR while DAP4 does not
2.  DAP2 padded values less than four bytes in size, DAP4 does not
3.  DAP2 dis not include checksums, DAP4 does

#### About serialization of varying-sized variables

There are several kinds of varying data:

1.  Strings
    - String s;
2.  Array variables that vary in size
    - Int32 i\[\*\];
    - Float64 j\[10\]\[\*\];
3.  Structure variables with varying dimensions and Sequence variables
    - Structure { int32 i; int32 j\[10\]; } thing\[\*\];
    - Sequence { int32 i; int32 j\[10\]; } thing2;
4.  Structure variables that have a varying dimension and one or more
    fields that vary
    - Structure { int32 i\[\*\]; int32 j\[10\]\[\*\]; } thing\[\*\];

Note that there is no practical difference between a (character) String
and an integer or floating point array with varying size except that the
type of the elements differ. Thus, the issues associated with encoding
*Int32 i\[\*\]* are really no different than encoding the String type.
This same logic can be extended to a varying array of Structures; it can
be seen as a string of Structures.

### General serialization rules

Narrative form:

1.  Fixed size types: Serialized by writing their (encoded) data.
2.  Strings: Serialized by writing their size as a 64-bit integer, then
    their encoded value
3.  Scalar Structures (which may have String/varying fields): Each field
    is serialized in the order it appears.
4.  Arrays (possibly with varying dimensions): An array is serialized by
    serializing the vector denoted by the leftmost dimension. For a
    fixed size dimension, each element is serialized. For a varying
    dimension, the length of the vector is written (as a 64-bit integer)
    and then each element is serialized.
5.  Sequences are serialized row by row, using a chunked-encoding
    scheme: Chunks are groups of bytes prefixed by a four byte header
    where the first byte indicates the *type* of the chunk and the
    remaining three bytes indicate its *size* (a 24-bit unsigned
    integer). For each row, in the general case, the values of its
    members are encoded as described elsewhere in this document (e.g.,
    Float64 variables as IEEE 754 8-bit floating-point numbers) and then
    broken up into a series of chunks where the last chunk signals the
    end of the row. The last chunk, indicating the end of the row, has a
    special *type* value and a *size* of zero bytes. As an optimization,
    an entire row can be sent in a single chunk, which is indicated
    using a special *chunk type* byte. In this special case, no *end of
    row* marker/chunk-header needs to be sent. The end of a sequence is
    indicated by an *end of sequence* marker that is, like the *end of
    row* marker, a chunk header with a special *type* and a zero-length
    *size*.
6.  Opaque types will be treated like Byte \[\*\] variables (for the
    purpose of serializing their values).
7.  Checksums will be computed for the values of all the variables at
    the top-level of each Group in the response. The checksum value will
    follow the value of the variable. We will use MD5 since it appears
    to be faster than SHA1 and we don't care about cryptographic
    security.
8.  Checksum values will be written as 128-bit values. Both Java and
    C/C++ have many libraries to compute this hash as an array of 16
    bytes; the code to print out a hex/ASCII representation is trivial.
    However, sending the hash as binary uses half the space of the
    hex/ASCII representation.

Assumptions:

1.  String: it is assumed that the server will know (or can determine
    w/o undue cost) the length of the String at the time serialization
    begins.
2.  It is assumed that the size of any variable dimension will be known
    at the time of serialization of that variable's dimension.
3.  For a Sequence, it is assumed that the total size may be
    considerable and *not* known at the time serialization begins.

Notes:

1.  Sequences cannot contain child Sequences, nor can they contain
    Structures (i.e., we are not allowing 'nested sequences') in DAP4
2.  Sequences are chunked so that rows can be read as entities without
    parsing their individual fields. This is the same basic algorithm as
    the chunked transfer encoding, but it's applied explicitly here.

### Non data information written into the binary response

\<<byte order>\>: A two-byte integer with the value *1*. On a big-endian machine the hex for this is *0x 00 01* and on a little-endian machine it will be *0x 01 00*. Thus a server can write data without knowing its own byte order; to write the \<<byte order>\> marker, it simply writes *1* in a 16-bit integer. A server can detect if it needs to swap bytes by looking for *256* or *1* as the value of the 16-bit integer.
\<<checksum>\>: A 128-bit binary value (16 bytes)
length counts: Use 128-bit varying integers, but assume that nothing every has more than a 64-bit integers maximum value as the number of items.
\<<eor>\>: Sequence End of Record markers will be a four byte with the value ??. This will build on the chunking code that is applied to the entire response (so we can economize on the implementation) but this will be explicit in the data stream (and the same goes for the \<<eos>\>).
\<<eos>\>: Sequence End of Sequence markers will be a four byte with the value ??
\<<Blank line>\>: A carriage return and new line pair. Also written <cr><nl>

## Rationale

There are two main differences between this proposed design and
[Proposed DAP4 On-The-Wire
Format](DAP4:_DAP4_On_the_Wire_Format "wikilink"): The data
corresponding to varying-size variables is mixed in with the fixed-size
variables, and this design depends on the DMR (i.e., the metadata in the
first of the two Parts of the response) to provide critical information
regarding the organization of the binary information.

##### Combining the fixed- and varying-size data

The effort required by a server to build that response that supports
random access does not seem justified. To build a response that
separates the fixed- and varying-size data values, the server either
must make two passes at serializing the response or store the
varying-size data after serialization until all of the fixed-size data
have been serialized/transmitted. If DAP were operating over transports
that used parallel I/O, this would not be the case. However, for HTTP
that is not a practical option. A two-pass serialization process is
complex and storing the serialized varying-size data is not acceptable
(either because it demands run-time memory, uses slow secondary storage,
...).

On the other hand, clients can easily read the response described here
and reformat it for random access use. In addition, a client may be able
to take advantage of information unavailable to the server, such as the
intended use of the data, to optimize the storage in ways that the
server cannot. What performance penalties will this place on a client?
If the client uses only a single thread/process, it cannot begin to use
the data until all of the response has been read from the socket.
However, it can reformat the data while they are being read (either by
writing those data to two or more files or to different parts of memory)
and it will have to do that anyway. That is, a single-threaded client is
stuck reading all of the bytes of the response from a socket and storing
them somewhere before it can do anything else. A savvy client would
certainly look at the DDX and note that if it contains only fixed-size
variables, it is already in a form amenable to random access. Either
way, the client can have the data in a form that facilitates random
access just as soon as the response is completely received (based on the
assumption that network I/O is far slower than even spinning disk I/O).
A multi-threaded (or double-buffered) client could do significant
processing while waiting for successive read operations to complete.

[JohnCaron](User:JohnCaron "wikilink")

1\) I agree that the client should be the place to create a randomly
accessible persistent form, if desired.

2\) Network I/O can be faster than local disk, eg at Unidata we have
1Gbit ethernet, but local transfer rate is less than 100Mbit/sec.

##### Removing (most of) the tags from the binary content

In the initial [Proposed DAP4 On-The-Wire
Format](DAP4:_DAP4_On_the_Wire_Format "wikilink") tags for the sizes and
types were included in the binary stream so it was not necessary to also
'walk the DDX' to deserialize the data. This has considerable appeal. It
makes error detection easier and makes the response document less bound
to a particular deserialization scheme. So why leave those out?
Compactness and deserialization speed. This response has the minimal
amount of extra information, which makes it compact but also faster to
deserialize for single-threaded clients (assumed to be most clients). It
is assumed that most clients will read a block from a socket, then store
it somewhere, then read the next block, and so on. Effectively mixing
the parse of the response with the network I/O. The use of prefixed
length information, kept to a minimum, minimizes the number of reads for
a typical client implementation. Of course, a client could read fixed
size blocks from the stream and parse it in-memory, but those that do
will hardly suffer from this response format.

##### Suitability for other protocols

Two candidate protocols for DAP appear to be AMQP and
[WebSockets](http://tools.ietf.org/html/rfc6455). I know of no real draw
to implement DAP over AMQP; WebSockets seems like it could be very
useful for building interactive web-based UIs, but it also seems very
*draft*. I think we should focus our efforts on a response that works
well with HTTP.

## Discussion

[Jimg](User:Jimg "wikilink") 13:07, 11 June 2012 (PDT) My main concern
with this encoding scheme is that a *typical* client be coded so that it
is pretty efficient and a really good client be coded so that it can
read and decode the information is as little extra time as it would take
HTTP to simply transfer the document. I think a typical client will
probably read the BLOB part of this chunk by chunk (see [Data response
and errors](DAP4:_Data_response_and_errors "wikilink")) and dump the
result in a file for later use. A better client would do that plus break
the parts up into sections and store them on disk or in memory. A really
good client would use two or more threads to double-buffer the network
I/O, effectively using the transmission time latency to perform the
decoding and processing operations. A quick look at libcurl's [multi
API](http://curl.haxx.se/libcurl/c/libcurl-multi.html) indicates there's
at least one way to do that without using threads or multiple processes.

## Example responses

In these examples, spaces and newlines have been added to make them
easier to read. The real responses are as compact as they can be. Since
this proposal is just about the form of the response - and it really
focuses on the BLOB part - there is no mention of 'chunking.' For
information on how this BLOB will/could be chunked, see [Data response
and errors](DAP4:_Data_response_and_errors "wikilink").

#### A single scalar

``` c
Dataset {
    Int32 x;
} foo;
```

NB: Some poetic license used in the following and the checksums for
single integer values seems silly, but these are really simple examples.
I used *<cr><nl>* to indicate a carriage return and newline pair which
MIME uses as its separator and which HTTP uses as the terminator for the
MIME headers in its preamble to the payload. Since DAP4 servers will
have to be able to send those, we might as well use them as separators
in the binary part, too.

    ...
    Content-Type: application/vnd.opendap.org.dap4.data
    <CRLF>
        <<Offset to the binary data>>
        <<DMR>>
    <CRLF>
    <<byte order>>
    x
    <<checksum>>

#### A single array

``` c
Dataset {
    Int32 x[2][4];
} foo;
```

    ...
    Content-Type: application/vnd.opendap.org.dap4.data
    <CRLF>
        <<Offset to the binary data>>
        <<DMR>>
    <<CRLF>>
    <<byte order>>
    x00 x01 x02 x03 x10 x11 x12 x13
    <<checksum>>

#### A single structure

``` c
Dataset {
    Structure {
        Int32 x[2][4];
        Float64 y;
    } s;
} foo;
```

Note that there is a single variable at the top-level of the implied
Group */* and that is *s*, so it's *s* that we compute the checksum for.
From now on I will elide the Content-Type header, ..., up to and
including the *\<<byte order>\>* information.

    x00 x01 x02 x03 x10 x11 x12 x13
    y
    <<checksum>>

#### An array of structures

``` c
Dataset {
    Structure {
        Int32 x[2][4];
        Float64 y;
    } s[3];
} foo;
```

    x00 x01 x02 x03 x10 x11 x12 x13
    y
    x00 x01 x02 x03 x10 x11 x12 x13
    y
    x00 x01 x02 x03 x10 x11 x12 x13
    y
    <<checksum>>

#### A single varying array (one varying dimension)

``` c
Dataset {
    String s;
    Int32 a[*];
    Int32 x[2][*];
} foo;
```

Note: The checksum calculation includes only the values of the variable,
not the prefix length bytes.

    16 This is a string
    <<checksum>>

    5 a0 a1 a2 a3 a4
    <<checksum>>

    3 x00 x01 x02 6 x00 x01 x02 x03 x04 x05
    <<checksum>>

NB: varying dimensions are treated 'like strings' and prefixed with a
length count. In the last of the three variables, the array *x* is a 2
by varying array with the example's first 'row' containing 3 elements
and the second 6.

#### A single varying array (two varying dimensions)

``` c
Dataset {
    Int32 x[*][*];
} foo;
```


    3

    3 x00 x01 x02

    6 x10 x11 x12 x3 x14 x15

    1  x20
    <<checksum>>

#### An varying array of structures

``` c
Dataset {
    Structure {
        Int32 x[4][4];
        Float64 y;
    } s[*];
} foo;
```

    2

    x00 x01 x02 x03 x10 x11 x12 x13
    y

    x00 x01 x02 x03 x10 x11 x12 x13
    y
    <<checksum>>

NB: two rows...

[JohnCaron](User:JohnCaron "wikilink")

I would recommend some kind of an <end> tag, rather than having to know
the number of structures that will get returned before you start
writing.

#### An varying array of structures with fields that have varying dimensions

``` c
Dataset {
    Structure {
        Int32 x[2][*];
        Float64 y;
    } s[*];
} foo;
```

    3

    1 x00 4 x10 x11 x12 x13
    y

    3 x00 x01 x02 2 x10 x11
    y

    2 x00 x01 2 x10 x11
    y
    <<checksum>>

#### A Sequence

This sequence uses the optimization where rows are sent in single
chunks, so no *end of row* marker is needed.

``` c
Dataset {
    Sequence {
        Int32 x;
        Float64 y;
    } s;
} foo;
```

    <<chunk header for the row>>
    xy
    <<chunk header for the row>>
    xy
    <<chunk header for the row>>
    xy
    <<chunk header for the row>
    xy
    <<end of sequence>>
    <<checksum>>

#### A Sequence (w/o optimized rows)

``` c
Dataset {
    Sequence {
        Int32 x[1024];
        Float64 y[1024];
    } s;
} foo;
```

Note that in this encoding, the variable *x* has all of its 1024
elements packaged in a singe chunk while *y*'s values are broken up into
two chunks. This is just to illustrate that in the general case, there's
no requirement about how big or small each chunk has to be. In addition,
there is no requirement that there ever be more than one chunk. It's OK
(although not great) to encode a row using only one chunk but not use
the special chunk *type* value and include an *end or row* marker/chunk.

    <<chunk header>>
    x0 x1 x2 ... x1023
    <<chunk header>>
    y0 y1 y2 ... 511
    <<chunk header>>
    y512 y513 ... y1023
    <<chunk header ending row>>
    <<chunk header>>
    x0 x1 x2 ... x1023
    <<chunk header>>
    y0 y1 y2 ... 511
    <<chunk header>>
    y512 y513 ... y1023
    <<chunk header ending row>>
    ...
    <<end of sequence>>
    <<checksum>>

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")