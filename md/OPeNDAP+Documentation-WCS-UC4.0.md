## Butte Meeting Notes

Use Case 4 - Catalog configuration for the WCS Server

Preconditions

- Same as 2.1
- The DAP has been defined in a .agg file

Trigger

- Start to configure the server for the data

Sequence of Events

- Generate Coverage Description for the data
  - Get General Information
  - Get Information from Data Source DDX
  - Read WCS/Catalog config file

What's need in a catalog (for each coverage)

- all the information needed for the describe coverage
- DAP access information (how to use the BES to get the data)
- This catalog will (???) be maintained by the BES and queried by the
  OLFS

[Category:WCS](Category:WCS "wikilink")