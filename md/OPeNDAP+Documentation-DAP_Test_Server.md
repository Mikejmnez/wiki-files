## Background

When I finished Jake Hamby's work on the Java-DAP I wrote the DODS Test
Server. This server utilized the Java-DAP to build synthetic datasets.
This was accomplished by:

1.  Subclasssing all of the datatypes to provide a specialization of the
    type that would generate synthetic data using algorithms such as
    trigonometric functions, Fibonacci series, etc.
2.  These test classes where packaged in class factory.
3.  The parsers were passed an instance of this class factory and a one
    (or more) DAP2 metadata documents (DDX, DDS, DAS) from which the
    parser would build a DDS object in memory.
4.  Calling the serialize() method of the DDS object would cause it to
    generate a DAP2 Data object conforming to the ingested metadata
    document(s) and carrying the synthetically generated data payload.
    - Sequences all had the same length, controlled by a configuration
      option.

## Problem addressed

Both libdap and Hyrax suffer from the absence of similar code written
for the BES and Hyrax using C++.

The DTS would provide an excellent mechanism with which to:

- Test fringe cases in the data model for which the data model should
  work but no regular dataset modeling the structure is readily
  available.
- Allow us to test the code with more complete code coverage.
- Allows client developers to easily test their clients against a
  variety of data representations.
- Because the data are generated algorithmically, test verification
  becomes easier.

## Proposed solution

Implement a DTS in C++ using the BES and libdap.

## Rationale for the solution

## Discussion