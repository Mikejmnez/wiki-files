[\<\< Back to OPULS Development](OPULS_Development "wikilink")

Note: This document is a revision of material from [DAP4
Design](http://docs.opendap.org/index.php/DAP_4.0_Design).

## Introduction

A DataDDX response is the way DAP4 returns data to a client. Each
DataDDX response is returned over the wire as a multipart MIME document
where the first part contains the DDX describing the data requested and
the second and later parts contains a binary encoding of the requested
data or error information.

See the [DAP4 DDX Grammar](DAP4:_DDX_Grammar "wikilink") document and
the [DAP4 Lexical Elements](DAP4:_DDX_Lexical_Elements "wikilink")
document for the DDX syntax and lexical structure respectively. See the
[on-the-wire format](DAP4:_DAP4_On_the_Wire_Format "wikilink") document
for the format of transmitted data.

For references to the Multipart MIME specification, see [The MIME
Multipart/Related Content-type (rfc
2387)](http://www.ietf.org/rfc/rfc2387.txt) and [MIME part
one](http://www.ietf.or/rfc/rfc1521.txt).

#### Organization of the multipart MIME document

Here's what the shell of the document looks like:

``` xml
Content-Type: multipart/related; type="text/xml"; start="<<start id>>";  boundary="<<boundary>>"

--<<boundary>>
Content-Type: text/xml; charset=UTF-8
Content-Transfer-Encoding: binary
Content-Description: ddx
Content-Id: <<start-id>>
    <<DDX here>>
--<<boundary>>
Content-Type: application/x-dap-little-endian
Content-Transfer-Encoding: binary
Content-Description: data
Content-Id: <<unique id for this piece of binary data>>
Content-Length: <<-1 or the size in bytes of the binary data>>
    <<XDR encoded binary data, part 1>>
--<<boundary>>
Content-Type: application/x-dap-little-endian
Content-Transfer-Encoding: binary
Content-Description: data
Content-Id: <<unique id for this piece of binary data>>
Content-Length: <<-1 or the size in bytes of the binary data>>
   <<XDR encoded binary data, part 2>>
...
--<<boundary>>
Content-Type: application/x-dap-little-endian
Content-Transfer-Encoding: binary
Content-Description: data
Content-Id: <<unique id for this piece of binary data>>
Content-Length: <<-1 or the size in bytes of the binary data>>
    <<XDR encoded binary data, part n>>
--<<boundary>>
```

The example shows multiple sets of MIME headers separated by
*--\<<boundary>\>* lines; The final boundary line terminates the
document. The first group of headers (in a real response, there would be
other headers here like Date, XDAP, and others) provide information need
to recognize the boundary separators. The payload of that first data
part contains references to the related parts using the values of their
Content-Id headers (See
[here](DAP4:_DAP4_On_the_Wire_Format "wikilink")).

#### Choosing values for the DataDDX Content-Ids and Boundaries

We would like the software that builds these DataDDX responses to be
compatible with as many different transport protocols as possible, so
long as the cost to the implementation for which we know we must support
is low. One thing that some transport protocols may do is combine
several DataDDX responses into a single document and, while the
specifics of that will vary between protocols, one choice we can make
now that will facilitate that is to ensure that the values of the
Content-Ids and \<<boundary>\>s are unique within and across systems.
This will free software that combines DataDDX responses from having to
process the DDX and Content-Id header to ensure that no name collisions
are present. While using UUIDs, for example, makes the result values
'ugly', it adds virtually nothing to the time needed to build or process
the responses. Other schemes, that combine a URI with some
system-generated token could also be employed. The important point is to
ensure that these symbols are unique not only within a system, but
across systems.

[ndp](User:Ndp "wikilink") 12:42, 30 March 2010 (PDT) [Dennis
Heimbigner](User:dmh "wikilink") Modified 5/7/2012.

##### Regarding Content-Type

For the data-part of the response, the value of the Content-Type header
will be x-dap-\<big\|little\>-endian. This will make it easy to
determine the byte-order of the data BLOB that follows without actually
reading any of that BLOB. Note that the BLOB will have the byte-order
encoded in in as well, making one or the other redundant, but that will
add essentially no cost to the server's and simplify clients (because
they will be able to use either to determine the response byte-order).

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")