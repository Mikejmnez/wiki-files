## Overview

Return requested data in an XML document.

## Request

The XML Data Service is invoked by appending the suffix
<font size="2">`.xdap`</font> to the file part of the dataset's referent
(aka base) URL and then dereferencing the new URL. The XML Data Service
accepts an optional query string (constraint expression), If present a
query string (constraint expression) will be handled as it would for
[Data Service](DAP4:_Data_Service "wikilink") request.

## Response

This service provides data responses in XML format. When invoked the
constrained Dataset response document (DDX) will be marked up with the
data values of the request and returned. Large requests may be denied.

## Errors

[DAP4: Error Response.](DAP4:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")