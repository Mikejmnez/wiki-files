## Overview

This service provides ISO 19115 metadata for the Dataset, if any can be
found.

## Request

The ISO 19115 service is invoked by appending the suffix
<font size="2">`.iso`</font> to the file part of the dataset's referent
(aka base) URL and then dereferencing the new URL.

The request a query string (constraint expression) is not required, but
if present will obey the rules for constraints as would a [DAP:4 Dataset
Request](DAP4:_Requests#Dataset_Request "wikilink")

## Response

The ISO 19115 response is an XML document containing ISO 19115 metadata
located in the [DAP4: Dataset
Response.](DAP4:_Responses#Dataset_Response "wikilink")

## Errors

[DAP4: Error Response.](DAP4:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")