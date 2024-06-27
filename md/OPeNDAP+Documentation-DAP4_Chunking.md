## Overview

### Chunking Scheme

           chunked-body     =  *chunked-section last-chunk

           chunked-section  = chunk | chunk-extension

           chunk-extension  = chunk-size "x"  1*(chunk-ext-name [ "=" chunk-ext-val ] ";")

           chunk            = chunk-size "d" chunk-data

           chunk-size       = 7HEXDIG
           last-chunk       = 7("0") "d"

           chunk-ext-name   = token
                              ; sequence of 7-bit ASCII printable chars

           chunk-ext-val    = token | quoted-string
                              ; quoted-string is DQUOTE token DQUOTE

           chunk-data       = chunk-size(OCTET)
                              ; exactly chunk-size bytes

### Graphical representation of chunking scheme

<figure>
<img src="BES_chunking_7_1.jpg" title="Image:BES chunking 7 1.jpg" />
<figcaption>Image:BES chunking 7 1.jpg</figcaption>
</figure>

size - Stored in the first seven bytes are the size of the chunk (in
bytes). The size does not include the first 8 bytes which are the chunk
size and the chunk type (x or d). The size is a 16 bit integer encoded
in 7 bytes as 7 ASCII characters that represent the size as a 7
hexadecimal (base 16) digits. If the size is 0, this signifies the end
of the transmission, no more chunks follow.

type - The eighth byte is the type of chunk that follows. The type can
be one of

- x - extension, one or more name=value; pairs
- d - data, actual data

data - The data part of the chunk, meaning either extensions or data,
not both

Extensions are a name/value pair and can represent information needed by
the underlying communication layer. For example: status=error; would
mean that an error has occurred. This chunk would not contain the
error/exception information itself. Following chunks would hold that
information and would be of type d (for data).

It is possible that the chunk may not come across in one read call to
the underlying socket layer. The first 8 bytes represent information
about the chunk, the first seven bytes being the size of the chunk and
the eighth byte being the type of data the chunk contains. Read should
be called until the entire chunk has been received.

Read/receive should be called until the last chunk in the transmission
is received. The last chunk is represented by chunk size of 0.

### Chunking State Diagram

<figure>
<img src="ChunkedStreamReaderStateDiagram.png"
title="ChunkedStreamReaderStateDiagram.png" width="1000" />
<figcaption>ChunkedStreamReaderStateDiagram.png</figcaption>
</figure>

### Error response

If an error is encountered during the processing of a request (either
with the request itself, the processing of the response to the request,
or sending of the response) then the server will send an extension chunk
to the client with the name/value pair "status=error;", and, following
that chunk, data chunks will contain the error information followed by
the last chunk. The extension chunk with the error status could come
following a series of data chunks containing the partial response.