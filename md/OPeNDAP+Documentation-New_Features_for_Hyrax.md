New features for Hyrax

Proposed new features:

- Value-based constraints
- Asynchronous responses
- Checksums

Why are these important?

- Providing value-based constraints is one way to bring support for
  geo-spatial data into the realm of DAP’s Constraint Expression system.
  It would expand on the ‘from/to’ notation of the CE’s indicial
  constraints to ones that use data values. The value-based accesses
  would be supported for Grids, using the Grid maps as the sources for
  the values, and Arrays, using attribute values for the sources of the
  values. In either case, it might be that the value-based constraints
  are not applicable and would then result in an Error response from the
  server.
- Support for asynchronous responses provides a solution to a decade-old
  problem presented by the complexities of many real-world solutions to
  the problem of storing more data than can be stored on a spinning
  disk. We will do this by returning a response that is both human and
  machine readable that provides a URL which can be used to get the data
  at a later date and a hint about when that later date might be. The
  result is guaranteed to be either the data or a similar ‘asynchronous
  response.’
- Checksums are one of the most effective ways to ensure that a given
  data request produced the same response on two separate instances.
  This is a fundamental feature needed by provenance systems

[Category:Google Summer of
Code](Category:Google_Summer_of_Code "wikilink")