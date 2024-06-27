## Overview

The RDF service provides an RDF representation of the Dataset document
(DDX).

## Request

The RDF Service is invoked by appending the suffix
<font size="2">`.rdf`</font> to the file part of the dataset's referent
(aka base) URL and then dereferencing the new URL. The RDF service does
not accept a query string (constraint expression). If present a query
string (constraint expression) will be ignored.

## Response

The RDF response is an XML document containing an RDF version of the
[DAP4: Dataset Response.](DAP4:_Responses#Dataset_Response "wikilink")

## Errors

[DAP4: Error Response.](DAP4:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")