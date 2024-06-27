## Normative References

MIME
RFC 1521

<nowiki>Multipurpose Internet Mail Extensions

`                           (MIME) Part One:`
`                  Format of Internet Message Bodies`</nowiki>


RFC 2045

Multipart/Related MIME
RFC 2387

SOAP with Attachments
<http://www.w3.org/TR/SOAP-attachments>

RFC2234 - Augmented BNF for Syntax Specifications: ABNF
RFC 2234

## Division of Labor

<figure>
<img src="DAP4_DataDDX_Design_2.png" title="DAP4_DataDDX_Design_2.png"
width="400" />
<figcaption>DAP4_DataDDX_Design_2.png</figcaption>
</figure>

<figure>
<img src="DAP4_DataDDX_Design_1.png"
title="DAP4_DataDDX_Design_1.png" />
<figcaption>DAP4_DataDDX_Design_1.png</figcaption>
</figure>

The BES and OLFS work together to support DAP, however, it is the job of
the OLFS to correctly implement the DAP. The BES understands certain
aspects of the DAP, but it does not actually build correct responses
from DAP requests. Instead the OLFS reads a request and asks the BES to
build parts of the correct response that it then assembles to make the
complete response document.

To build a DataDDX response, the OLFS and BES will work together as
shown in Figure 1. When the OLFS receives and processes a DAP4 DataDDX
request, it will first parse from that request the information the BES
needs and send that along with both the Multipart MIME (MPM) *start* and
*boundary* values. The BES will use a handler to build a C++ DDS object
and return the MPM document that forms the payload of the DataDDX
response, with a few wrinkles, such as why the start and boundary values
need to be passed by the OLFS to the BES, to be described shortly.

When the OLFS begins to receive the MPM payload for the DataDDX
response, it will first write out the MIME headers for the response.
Following that, it will stream the response from the BES, and following
that, it will append (send to the stream) the closing section of the
document.

> **Note:** In Figures 1 and 2 the sequence of interactions between the
> OLFS, the BES and a generic Handler are shown using a UML sequence
> diagram. That type of diagram is intended to be used for objects and
> their methods, but I thought it was a useful diagram even though the
> idea of the BES, OLFS or a handler as an object is a bit of a stretch.

### Why this design?

This design describes the implementation of the DataDDX response with as
little modification to the libdap and BES software as possible (and
minimal changes to the OLFS, too). However, we want this design to be
usable as a building block for two things besides DAP4: A SOAP interface
for DAP servers, where several DataDDX responses can be held in a single
SOAP envelope; and implementations of DAP4 that use transport protocols
other than HTTP.

Like most designs intended for several uses, this one is a compromise.
Figure 2 shows a design that would be better at addressing the issues
associated with *support for other transport protocols* and which might
offer advantages to SOAP as well. In fact, it also has a cleaner
separation of duties because the OLFS takes over complete responsibility
for building the headers of the MPM document. But the cost is that the
BES must build two DDS objects - an expensive operation, especially for
large datasets. So the compromise reached is that some effort to build
MIME headers, those that are part of the payload of the response, is
moved into the BES/Handler/libdap software. However, because support for
a SOAP interface means that we should be able to stuff several of these
MPM documents into one SOAP envelope, the trailing MPM separator
information is *not* written by the BES/Handler/libdap. Instead, the
trailing separator that closes the document needs to be written by the
OLFS.

With the design presented, the BES/... software can behave as
efficiently as the current DataDDS code but each response can be used as
a building block in a SOAP response - one response concatenated after
the other - that is closed by the OLFS so the BES/Handler software does
need to know about the way the responses are being packaged. At the same
time the response format is likely usable by other transport protocols
because either that implementation can use MPM as its payload or take
apart the response (since MIME is designed to make that operation fairly
easy).

### What is not Covered Here?

HTTP and MIME are both 'header and payload' protocols in the sense that
control information is put in a set of headers and then the information
to be sent is held in a second part called the payload. In the case of
multipart MIME over HTTP, there are two payloads. First, the HTTP
response has a set of headers and a payload and that payload is, in this
design, a multipart MIME document, which itself has sections with
headers and payloads. See the Figure.

We will want to make some changes to the 'data part' or 'data payload'
of the multipart MIME document, but *those are not described here.*
Those changes will be described in the DAP 4.0 design specification. If
they are complex enough to warrant their own implementation document,
then we will either add a section here or start a new document. For now
(8/21/09), the data payload transported by the DataDDX is the same as
the data payload in the DataDDS response of DAP2.

### Handler/libdap

The Handler running inside the BES uses libdap's DDS class and its
methods to make the DDX and XDR-encoded data blob. The DDS::print_xml()
method will take the *Content-Id* to be used with the data blob section
as a parameter. A new method will be added to build the data blob.

A new set of functions will be added to libdap's collection of
MIME-header writers to build the MPM boundary sections. Two functions
will be needed: One to write the boundary headers for the DDX and one to
write the boundary headers for the data blob.

The libdap DODSFilter class will get a new method that will package all
of these calls in a way that's similar to the existing
DODSFilter::send_das(), ..., send_data() methods

### BES

The BES will need to be modified to respond to a *get dataddx* request
and will need to accept the *start* and *boundary* values for the MPM
document from the OLFS. Each handler will likely need some modification
to support the new response type.

The XML request document will look like the following:

<?xml version="1.0" encoding="UTF-8"?>

`  `<request reqID ="####" >
`       `<get type="dataddx" definition="def_name" returnAs="name">
`           `<contentStartId>`SOME_ID_STRING`</ContentStartId >
`           `<mimeBoundary>`MIME_BOUNDARY_STRING`</MimeBoundary >
`       `</get>
`  `</request>

And the bescmdln command string will be:

`   get dataddx for def_name contentStartId SOME_ID_STRING mimeBoundary MIME_BOUNDARY_STRING;`

### OLFS

The OLFS will need to be modified to recognize the new request, generate
the correct boundary and start values, and complete the response by
building the correct response headers and closing 'separator' headers.

## Example Response Document

         Content-Type: Multipart/Related; boundary=example-2;
                 start="<1@opendap.org>"  <-------------------
                 type="Text/x-Okie"                           |
                                                              |
         --example-2                                          |
         Content-Type: Text/xml; charset=iso-8859-1;          |
                 declaration="<3@opendap.org>"                |
         Content-ID: <1@opendap.org>      <-------------------
         Content-Description: dap4-ddx

         [DDX with <blob href="cid:2@opendap.org"/>]  <----
         --example-2                                       |
         Content-Type: application/octet-stream            |
         Content-ID: <2@opendap.org>      <----------------
         Content-Transfer-Encoding: gzip
         Content-Description: dap4-data
         Content-Disposition: ATTACHMENT

         [XDR Encoded data]
         --example-2--

Here are the parts, broken down into which parts of Hyrax are
responsible for what:

         Content-Type: Multipart/Related; boundary=example-2;
                 start="<1@opendap.org>"
                 type="Text/x-Okie"

The response headers are built by the OLFS. It must figure out the value
of *boundary* and *start* and both pass those to the BES and use them to
build the *Content-Type* header.

         --example-2
         Content-Type: Text/xml; charset=iso-8859-1;
                 declaration="<3@opendap.org>"
         Content-ID: <1@opendap.org>
         Content-Description: dap4-ddx

         [DDX with <blob href="cid:2@opendap.org"/>]

The part boundary, headers and DDX. This will be built by the BES
(likely in BESDapTransmit) using a method in DODSFilter that will
build/send the part headers and then call a method in DDS to build/send
the DDX.

         --example-2
         Content-Type: application/octet-stream
         Content-ID: <2@opendap.org>
         Content-Transfer-Encoding: gzip
         Content-Description: dap4-data
         Content-Disposition: ATTACHMENT

         [XDR Encoded data]

Teh data blob will be written also using a method in DODSFilter that
will build/send the part headers and then the data blob. As with the
previous part, this DODSFilter method will be called by the BES.

         --example-2--

The closing part boundary will be written by the OLFS.

### Notes about the example

- "Usage of "cid:", as in this example, may be useful for a variety of
  compound objects. It is not, however, a part of the Multipart/Related
  specification." (Multipart/RElated MIME, p5.)
- "User Agents that recognize Multipart/Related will ignore the
  Content-Disposition header's disposition type. Other User Agents will
  process the Multipart/Related as Multipart/Mixed and may make use of
  that header's information." (Multipart/RElated MIME, p6.)
- We probably need to use "Content-Transfer-Encoding: binary" on things
  without compression. " "Binary" means that not only may non-ASCII
  characters be present, but also that the lines are not necessarily
  short enough for SMTP transport." (MIME, p14.)
- The boundary markers are all prefixed by "--" and the line is
  terminated by a CRLF pair **except** for the closing boundary which is
  ended by "--" and a CRLF pair.
- The boundary text (not including the "--" and CRLF) is limited to 69
  characters (RFC 1521, p68).
- The grammar for boundary is:

<!-- -->

     boundary := 0*69<bchars> bcharsnospace

     bchars := bcharsnospace / " "

     bcharsnospace :=    DIGIT / ALPHA / "'" / "(" / ")" / "+"  / "_"
                      / "," / "-" / "." / "/" / ":" / "=" / "?"

where '0\*69<bchars>' means zero to sixty nine '<bchars>'; <bchars> are
either a 'bcharnospace' or a space; and a 'bcharnospace' is any one of
DIGIT, ALPHA, et cetera.

- Content-Id headers use the Message-Id header syntax and must be
  world-unique (RFC 2045, p25). See RFC 822 for information about
  Message-Id.
- A brief summary of the Message-Id header value as described by RFC
  822: The value is *word \*(. word) "@" domain* where *\*(. word)*
  means zero or more occurrences of a dot and a word; "@" is the literal
  at sign; and *domain* is the internet domain of the host building the
  message.
- Use *getdomainname()* on Unix to get *domain*.
- Get a UUID for the first part of the Message-Id using uuid_generate()
  (#include \<uuid/uuid.h\> in C/C++). This may be overkill, but who
  cares?