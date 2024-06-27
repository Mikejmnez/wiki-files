We work with a few groups that do a fair amount of experimental
compression depending on the type of data they are using. This made me
think about how OPeNDAP handles compression. Here's what I found in the
DAP 4.0 spec:

**Compression**: DAP clients MUST be provided a way indicate to a server
that they are able to process compressed responses. A server MAY
compress a response ONLY if a client has indicated that it can process
the compressed response. A server is NEVER under any obligation to
compress a response. Support for particular compression algorithms is
specific to the transport protocol.

Have you thought at all about allowing the client to indicate the kinds
of compression it can accept and the server specifying what kind of
compression it is using with a given response? That would add a lot of
flexibility. Does the last sentence above mean that how the compression
stuff is handled is defined in the specs for each particular transport
protocol?

I think something about multiple compression algorithms should be said
in the above section so that the various transport protocol specific
specs will all deal with that issue. Maybe something like:

**Compression**: DAP clients MUST be provided a way indicate to a server
whether the client supports any compression algorithms and which ones it
supports. A server MAY compress a response ONLY if a client has
indicated that it can process the compressed response. A server MUST
indicate the compression algorithm used to compress the response. A
server is NEVER under and obligation to compress a response.

I know this complicates things a bit but I really think it will be a
useful feature. What do you think?

*EthanDavis - 17 Mar 2004*

Is there some standardized way to describe compression algorithms?
Without such an accept list of compression type names I think this opens
up a nasty can of worms...

*NathanPotter - 17 Mar 2004*

I agree there needs to be a list of defined compression types but I'm
not sure it has to be a standardized way. I do think it is important
that it be extensible. For example, the folks we work with who do
compression are developing new algorithms that they might want to use.

Perhaps each transport protocol needs to list the names of current
standard compression techniques but allow names not on the list. OGC WCS
must do something similar with return formats; GeoTIFF and HDF are the
suggested formats but others are allowed.

*EthanDavis - 20 Mar 2004*