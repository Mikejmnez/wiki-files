## Overview

The Dataset Service provides a metadata description for a dataset.

## Request

The Dataset Service is invoked by appending the suffix
<font size="2">`.xml`</font> to the file part of the dataset's referent
(aka base) URL and then dereferencing the new URL. The dataset service
accepts a query string (constraint expression).

[DAP4: Dataset Request](DAP4:_Requests#Dataset_Request "wikilink")

## Response

The Dataset response is an XML document that contains both the
'syntactic' (structural) and 'semantic' metadata for the dataset.

[DAP4: Dataset Response](DAP4:_Responses#Dataset_Response "wikilink")

## Errors

[DAP4: Error Response](DAP4:_Responses#Error_Response "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")