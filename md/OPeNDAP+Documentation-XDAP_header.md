A DAP server should use the XDAP header to announce the lowest version
of the needed to understand the current response. Each time a server
builds/returns a response, it should try to set the protocol version in
the XDAP to the lowest needed to read the response.

Higher versions of DAP should be compatible with the features in older
versions of the protocol. For example, this means that a 3.3 client must
be able to read a response that conforms to 2.0, 3.0, ..., 3.3.

A client that receives a response that is at or below the highest
version it implements MUST process that response. A client SHOULD try to
process responses with higher protocol numbers since servers might have
mis-labeled a response with a higher version. For example, determining
the exact lowest version needed to decode a the data responses might be
complicated. Or, a server might start off building a Data response only
to realize that the client needs to be able to accept 3.2 responses when
it can actually only understand 2.0 - in this case, the server would
return an error, which a 2.0 client can understand, even though it might
have already written the XDAP: header. Of course, it's best to label the
responses accurately, but it might be very hard to ensure that is always
the case.

## DAP 3.1 Behavior

A server conforming to DAP 3.1 or greater MUST always return the XDAP:
header and it MUST always contain the version number of the DAP such
that a client implementing that version can understand the response. The
server SHOULD return the lowest protocol version number needed to read
the response, but MAY return a higher number if appropriate. For
example, suppose a server is building a response that would require DAP
3.3 to read but encounters an error and instead will return an Error
response. An Error response requires only DAP 2.0 so the server should
return XDAP: 2.0 but it may instead return XDAP: 3.3.

A client MUST equate a server that sends no XDAP header as the same as a
server which returns XDAP: 2.0.

## DAP 3.2 Behavior

DAP 3.2 will introduce the notion of protocol negotiation, similar to
HTTP's response type or encoding negotiation. The client MAY send an
XDAP-Accept: header to tell the server the highest version of the
protocol that the client can understand. The value of this header MUST
be a single value of the form x.y (DIGIT '.' DIGIT); other values or
multiple values can be ignored at the server's discretion. The server
MUST then respond using only responses at or below that version number
of the protocol. Note that an Error response (e.g., "The response you
requested cannot be returned using the protocol version you understand")
can be understood by any client. If a client does not send the
XDAP-Accept: header, then the server MUST assume a DAP 2.0/3.1 client.
That is, a client that either understands DAP 2.0 version responses or
the XDAP header with it's value equal to 3.1. This is because the only
functional difference between DAP 2.0, 3.0 and 3.1 is form/content of
the version information returned in the HTTP response header.

By allowing the server to respond with a lower version the protocol can
support old servers (since that's how they will respond) and enable
newer clients to treat the lower version responses as errors (because
the newer servers can be expected to discriminate between different
server versions).

New servers SHOULD always return a response that conforms to the version
sent from the client in the XDAP-Accept header.

In addition to returning the DAP protocol version using the XDAP header
(which is a mechanism specific to HTTP), the protocol version will also
be returned in the DDX *Dataset* element using the XML attribute
*dap-version*. See [DDX](DDX "wikilink").

[XDAP Header](Category:Development "wikilink") [XDAP
Header](Category:DAP4 "wikilink")