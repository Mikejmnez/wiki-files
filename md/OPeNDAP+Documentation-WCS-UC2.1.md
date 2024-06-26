## Butte Meeting Notes

Use Case 2.1 - Describe Coverage - Generated Response

Preconditions

- HF Radar Data in multiple files
- All of the files are spat. colocated
- Each file is one time slice, no gaps, uniform delta
- The files are NetCDF-CF in WGS84
- There exists an Id that represents the union of these files (as a
  single coverage)

Trigger

- Hyrax receives request

Sequence of Events

- same as 2.0 except generate the coverage object (???)
  - Read .agg file to find out about the DAP aggregation
  - Get information about coverage from catalog object

[Category:WCS](Category:WCS "wikilink")