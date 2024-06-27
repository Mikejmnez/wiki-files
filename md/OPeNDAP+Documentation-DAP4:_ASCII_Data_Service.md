## Overview

Return requested data in ASCII format.

## Request

The ASCII Data Service is invoked by appending the suffix
<font size="2">`.ascii`</font> to the file part of the dataset's
referent (aka base) URL and then dereferencing the new URL. The ASCII
Data Service accepts an optional query string (constraint expression),
If present a query string (constraint expression) will be handled as it
would for [Data Service](DAP4:_Data_Service "wikilink") request.

## Response

The ASCII Data response is a repackaging of the data requested from
underlying data resource into an ASCII representation.

## Errors

[DAP4: Error Response.](DAP4:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")