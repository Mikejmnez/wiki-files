[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

One persistent (ahem) problem with DAP2 was the inability of client
applications to recognize when a data transmission failed. If an error
happened before the initial set of headers for the response were sent,
then DAP2's error reporting scheme worked just fine. However, if the
server encountered an error once the serialization of data started, for
example, it was essentially impossible for a client to detect the error
(*essentially* being the operative word; the Ocapi library did detect
these errors, but it is the only client I know that did so).

This proposal suggests that DAP4 use a simple variation on the HTTP/1.1
chunked transmission scheme to serialize the data Part of the response
document so that errors are simple to detect. Furthermore, this scheme
is independent of the form or content of that part of the response, so
the same scheme can be used with different response forms or dropped
when/if DAP is used with protocols that support out-of-band error
signaling, simplifying our ongoing refinement of the protocol.

## References

1.  [HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616.html)

## Problem Addressed

The DAP needs to format its data responses so that a server that
interleaves transmission with data reads and serialization can signal
errors to a receiver reliably.

## Proposed Solution

##### Overview

The data part of a response document (from now on I will just say
'response document') will be 'chunked' in a fashion similar to that
outlined in HTTP/1.1. However, in addition to a prefix indicating the
size of the chunk, DAP4 will include a *chunk-type* code. This will
provide a way for the receiver to know if the next chunk is part of the
data response or if it contains an error response. In the latter case,
the client should assume that the data response ended, even though the
correct closing information was not provided.

##### More detail

Each chunk will be prefixed by a *chunk header* consisting of a *chunk
type* and *byte count*, where the chunk type is a single byte and the
byte count is an unsigned integer using three bytes, with the entire
header in the server's byte order. Immediately following the chunk
header will be *byte count* bytes followed by another (four byte) chunk
header. By writing the chunk header in the server's byte order, it
should be simple for the server to write these headers and easy for the
clients to read them.

Three *chunk-type* types are defined in this proposal:

data: This chunk header prefixes the next chunk in the current data response
error: This chunk header prefixes an error message; the current data response has ended
end: This chunk header is the last one for the current data response

##### Grammar

<font size="2">

    response = chunk *chunk

    chunk = chunk-header chunk-data

    chunk-header = chunk-type chunk-size

    chunk-type = b0 (OCTET)
                          ; 0 = data, 1 = error, 2 = end

    chunk-size = b1 ... b3 3(OCTET)
                          ; 3 bytes, interpreted as an unsigned integer, in the server's byte order

    chunk-data = chunk-size(OCTET)
                          ; exactly chunk-size bytes

</font>

## Rationale

The chunk headers use a simple prefix with a binary count because it
will be fast and easy to build without cumbersome binary to ascii
conversions (e.g., HTTP uses ASCII...). It will be simple to en/decode
the chunk-type and count. Having space for 256 chunk types is overkill,
but enables clients to use byte masks which might be easier in some
programming environments.

## Discussion

[Jimg](User:Jimg "wikilink") 14:58, 28 August 2012 (PDT) We decided to
drop the multipart MIME in favor of chunking the entire response. Dennis
made the point that using both MIME and chunking was redundant; I agree
and there seems to be little real benefit to using MIME since there's
little way a non-DAP client will be able to do anything useful with the
data part. Chunking the entire response is a fine way to transmit the
data and ensure reliable error transmission. Also note that there's no
limit on the size of a chunk, so any response can be made up of just one
chunk.

[JohnCaron](User:JohnCaron "wikilink")

1\) Perhaps "message" is a better name than "chunk' ?
[Jimg](User:Jimg "wikilink") 14:51, 28 August 2012 (PDT) Since these are
parts of a single message I think *chunk* is right word.

2\) I would not limit the size of the chunk to 2^24. In fact, I would
use a base-128 variable length encoding to let the size be as large as
needed, without wasting space. [Jimg](User:Jimg "wikilink") 15:31, 4
September 2012 (PDT) Originally, I thought this was OK, but Ethan and
Dennis convinced me that 2^24 (~16.7\*10^6) was plenty big enough for a
chunk.

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")