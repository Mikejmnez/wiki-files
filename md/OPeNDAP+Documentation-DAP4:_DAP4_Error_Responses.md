[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

It must be expected that any DAP4 server will encounter unrecoverable
errors when processing a DAP4 request. Thus the DAP4 protocol must be
prepared to communicate such errors from the server to the requesting
client.

## Proposal

The [multipart mime
proposal](DAP4:_DAP4_Multipart_Mime_Format "wikilink") is to be extended
so that at any point, the sequence of parts can be terminated with an
error part describing the error and ending the request.

The proposed format for an error part is as follows.

    --<<boundary>>
       Content-Type: text/xml; charset=UTF-8
       Content-Transfer-Encoding: binary
       Content-Description: error
           <<Error Body>>

The error body contains information using a T.B.D. xml document
structure. It is proposed that the error body contain at least the
following information.

1.  An error code (an integer) – provides a short characterization of
    the error. The set of error codes will be defined and extended as
    needed.
2.  An error message – provides a detailed characterization of the
    error.
3.  Positional information – where appropriate, this provides a
    "pointer" into some text document that shows where the error was
    detected. Possible documents include the DDX and the constraint
    expression. The positional information will include, as appropriate,
    a document reference, (optionally) a line number, and a character
    number either within the line or within the document as a whole.
4.  Context information – provide additional information that will help
    to isolate the error and its cause. A java stack trace would be an
    example of this.
5.  Other information – Any other arbitrary text information thought
    useful by the error detector.

The error code and error message are the only required elements. The
structure of the "other information" is undefined, but should look like
a legal, if unvalidated, xml document.

[Dennis Heimbigner](User:dmh "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")