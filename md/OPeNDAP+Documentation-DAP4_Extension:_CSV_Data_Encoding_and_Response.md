[\<\< Back to OPULS Development](OPULS_Development "wikilink")

This document specifies the DAP4 CSV data encoding, how DAP4 servers can
advertise support for the DAP4 CSV encoding, and how DAP4 clients can
request DAP4 CSV encoded data responses.

The DAP4 CSV data encoding represents DAP4 data as structured
Comma-Separated Values (CSV) in UTF-8 text. Though based on the
*text/csv* media type described in RFC 4180<sup>\[[RFC
4180](#RFC_4180 "wikilink")\]</sup>, the DAP4 CSV is more complex so
that it can fully represent the more complex data structures of the DAP4
data model. Some structure beyond simple CSV is necessary to capture the
DAP4 data structures.

As with requests for data in the DAP4 binary data encoding described in
Volume 1 of the DAP4 specification <sup>\[[DAP4
Vol1](#DAP4_Vol1 "wikilink")\]</sup>, requests for DAP4 CSV encoded data
can be constrained to a subset by using a standard DAP4 Constraint
Expression \[DAP4 CE\]

## DAP4 Extension Details

The Role URI for the DAP4 CSV Data Encoding and Response extension is

**`http://services.opendap.org/dap4/extension/text-csv-data`**

DAP4 servers MUST include this role URI in a Dataset Service Response
\[DSR\] *extension* element to indicate that the DAP4 CSV Data Encoding
and Response extension is supported for the dataset (?represented by
that DSR?). DAP4 clients MAY check for DSR **extension** elements with
this role URI to determine if the DAP4 CSV Data Encoding and Response
extension is supported.

Dataset Service Response \[DSR\] *extension* elements must also contain
a name and a description. The following are recommended text for the
name and description:

Name
DAP4 CSV Data Encoding and Response

<!-- -->

Description
This extension provides an encoding for representing DAP4 data as
structured Comma-Separated Values (CSV) in UTF-8 text.

## Requesting DAP4 CSV Encoded Data Response

> <table>
> <thead>
> <tr class="header">
> <th style="width: 15%"><p>URL Suffix</p></th>
> <th style="width: 55%"><p>Media Type</p></th>
> <th style="width: 30%"><p>URL Example</p></th>
> </tr>
> </thead>
> <tbody>
> <tr class="odd">
> <td><p>"<strong>.dap</strong>" or "<strong>.dap.csv</strong>"</p></td>
> <td><dl>
> <dt>application/vnd.opendap.dap4.data+csv</dt>
> <dd>
> DAP4 CSV encoding
> </dd>
> </dl></td>
> <td><p>http://server/path/dataset.nc<strong>.dap</strong>
> http://server/path/dataset.nc<strong>.dap.csv</strong></p></td>
> </tr>
> <tr class="even">
> <td><p>"<strong>.dap.csv</strong>"</p></td>
> <td><dl>
> <dt>text/csv</dt>
> <dd>
> DAP4 CSV (UTF-8) Data encoding with generic media type
> </dd>
> </dl></td>
> <td><p>http://server/path/dataset.nc<strong>.dap.csv</strong></p></td>
> </tr>
> </tbody>
> </table>

## DAP4 CSV Encoding

## Normative References

<div id="RFC_4180">
</div>

\[RFC 4180\] [Common Format and MIME Type for Comma-Separated Values
(CSV) Files](https://www.ietf.org/rfc/rfc4180.txt)

<div id="DAP4_Vol1">
</div>

\[DAP4 Vol1\] [DAP4 Volume 1: Data Model, Persistent Representations,
and Constraints](DAP4:_Specification_Volume_1 "wikilink").

<div id="DAP4_Extensions">
</div>

\[DAP4 Extensions\] \[DAP4:_Specification_Volume_1#Extensions\|DAP4
Volume 1, Section 9.3 Extensions\]