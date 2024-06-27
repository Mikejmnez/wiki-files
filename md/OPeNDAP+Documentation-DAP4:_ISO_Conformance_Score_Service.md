## Overview

This service provides a browser renderable document that describes how
well the metadata held in the Dataset conforms to ISO 19115.

## Request

The ISO Conformance Rubric service is invoked by appending the suffix
<font size="2">`.rubric`</font> to the file part of the dataset's
referent (aka base) URL and then dereferencing the new URL.

The request a query string (constraint expression) is not required, but
if present will obey the rules for constraints as would a [DAP:4 Dataset
Request](DAP4:_Requests#Dataset_Request "wikilink")

## Response

This service provides a browser renderable document that describes how
well the metadata held in the [Dataset
Response](DAP4:_Responses#Dataset_Response "wikilink") conforms to ISO
19115.

## Errors

[DAP4: Error Response.](DAP4:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")