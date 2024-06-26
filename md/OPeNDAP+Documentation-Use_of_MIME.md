I think that we should specify that MIME documents are used to hold the
objects/responses.

James Gallagher - 26 Sep 2003

Ok, do you envision doing away with the separate blob response?

Nathan Potter - 26 Sep 2003

Nope. We need the BLOB to be separate because quasi-transports like SOAP
require the entire response be complete before transmission starts. We
want to support streaming, so we need to make the data available w/o
involving SOAP, for example. Since MIME does not _have_ to include
=Content-Length= we can stream a MIME document.

Along these same lines, using the MIME/multipart for the BLOB (see
BlobFormatting) we can support stuff like HTTP keep-alive (because it's
possible to tell when the stream ends since multipart specifies a
trailing =boundary= marker). This is a big win for efficiency - we want
to be able to support the more advanced features of HTTP even though we
don't want to tie the DAP4 to HTTP.

James Gallagher - 26 Sep 2003

Well, this is sort of related (perhaps change this topic header to
"on-the-wire specification"?)

I think we need a rigorous specification of what the "on-the-wire"
protocol is. As part of that we need to know what is a legal request and
response. Right now we have the situation that the HDF server can put
out a DDS that breaks the Java parser. Which side has the bug?

John Caron - 22 Oct 2003