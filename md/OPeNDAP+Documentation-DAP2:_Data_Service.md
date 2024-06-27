## Overview

The DAP2 data service provides DAP2 access to the data resource.

## Request

The DAP2 Data Service is invoked by appending the suffix
<font size="2">`.dods`</font> to the file part of the dataset's referent
(aka base) URL and then dereferencing the new URL. The DAP2 Data service
accepts a query string (constraint expression).

**[DAP2 Specification
Document](https://cdn.earthdata.nasa.gov/conduit/upload/512/ESE-RFC-004v1.1.pdf)**

## Response

The DAP2 Data response self describing data document that contains a
DAP2 DDS followed by the XDR encoded binary object whose structure is
described by the DDS.

**[DAP2 Specification
Document](https://cdn.earthdata.nasa.gov/conduit/upload/512/ESE-RFC-004v1.1.pdf)**

## Errors

[DAP2: Error Response](DAP2:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")