## Overview

Return requested data in a NetCDF 3 file format.

## Request

The NetCDF File-out Service is invoked by appending the suffix
<font size="2">`.nc`</font> to the file part of the dataset's referent
(aka base) URL and then dereferencing the new URL. The NetCDF File-out
Service accepts an optional query string (constraint expression), If
present a query string (constraint expression) will be handled as it
would for [Data Service](DAP4:_Data_Service "wikilink") request.

## Response

The NetCDF File-out response is a repackaging of the data requested from
underlying data resource as a NetCDF 3 file.

## Errors

[DAP4: Error Response.](DAP4:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")