This succinct suggestion for adding support to asynchronous responses
came from Roberto De Almeida (roberto at dealmeida.net):

> My suggestion would be to return a 202 Accepted
> (http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3)
> response with a "Location:" header pointing to a unique URL. Accessing
> the new URL should return either a 404 Not found if the response is
> not ready; 200 OK if it is; and 410 Gone if it has been generated and
> deleted after some time.

**Comment**: [jimg](User:Jimg "wikilink") 18:47, 22 January 2009 (PST)
This effectively factors the issue out of DAP and says that how
asynchrony will be handled is dependent on the transport protocol. I
think that's essentially correct. For example, SOAP has an asynchronous
mode and DAP over SOAP should use that, not some DAP-specific hack.

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")