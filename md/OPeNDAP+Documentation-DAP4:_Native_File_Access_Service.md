## Overview

This service provides direct access to the data source file (or whatever
else) that is creating the DAP dataset resource.

## Request

The Native File Access service is invoked by appending the suffix
<font size="2">`.file`</font> to the file part of the dataset's referent
(aka base) URL and then dereferencing the new URL.

The request a query string (constraint expression) is not required, and
if present it will be ignored.

## Response

Returns to the requesting client a copy of the data source file (or
whatever else) that is creating the DAP dataset resource.

## Errors

[DAP4: Error Response.](DAP4:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")