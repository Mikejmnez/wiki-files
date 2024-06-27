## Overview

The Data Service provides DAP4 data access to a dataset.

## Request

The Data Service is invoked by appending the suffix
<font size="2">`.dap`</font> to the file part of the dataset's referent
(aka base) URL and then dereferencing the new URL. The Data service
accepts a query string (constraint expression).

[DAP4: Data Request](DAP4:_Requests#Data_Request "wikilink")

## Response

The Data response is a multipart MIME document that contains a N+1 parts
for a response with N variables.

[DAP4: Data Response](DAP4:_Responses#Data_Response "wikilink")

## Errors

[DAP4: Error Response](DAP4:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")