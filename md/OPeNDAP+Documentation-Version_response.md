The version response is a way for a DAP-compliant server to advertise
the versions of the DAP it supports along with the versions of the
software with which it is built.

There are two ways to request the version response. First, the token
*version* may be appended to the URL which names a server. Second, the
suffix *.ver* may be appended to a DAP URL (i.e., a URL to which a
client could append *.das* or *.ddx* and expect to get the corresponding
DAP response object. Both ways of requesting the version response have
exactly the same affect. The reason two request forms exist is that it's
useful for a person installing a server to be able to get some sort of
response from it before it can serve data (so there maybe no URLs to
which a standard DAP request suffix can be appended). Similarly, for
those on the outside, with one or more URLs to data, it may be hard to
determine which part of the URL corresponds to the server itself.

In the past, the *.ver* suffix was also going to return the version
number of the data referenced by the URL. However, often that
information does not exist. A better solution to this is to generate CRC
codes or digital watermarks on the fly. This does not provide a version
number for the data, but it does provide a way to determine if a given
request is returning the same data and, in the case of a watermark, may
also help determine the origin of a subset.

While we are requiring that 3.2 clients read and understand 3.1, 3.0 and
2.0 (and so on for 3.3, ...) that is not practical for the Version
response since there are some widely different forms and since no
clients actually use the 3.1 response. DAP 3.2 or 3.3 will set this in
stone and then all 3.2 or 3.3 and subsequent clients will need to read
and understand it.

## DAP 3.1

In DAP 3.1, the version response is an XML document. This document
contains version information for:

1.  BES
2.  Handlers (the DAP version is embedded in this part of the document)
3.  OLFS
4.  Hyrax (as a whole)

The entire document is wrapped in a *OPeNDAP-Version* element.

An [example Version response](example_Version_response "wikilink") from
a 3.1-compliant server (Hyrax 1.4.0).

**Note**: See the *discussion* page for an alternative design.

## DAP 3.2

The DAP version document as provided should be changed to make it's
output more in line with something a person would really read and
understand quickly. See [discussion](:Talk:version_response "wikilink")
for more about this.

To provide DAP with a way for clients to discover server features that
are common but not completely universal, the version response may be
extended to include information about server-side functions.

[One possible
design](http://scm.opendap.org/trac/wiki/CEFunctionDiscovery).

[Version response](Category:Development "wikilink") [Version
response](Category:DAP4 "wikilink")