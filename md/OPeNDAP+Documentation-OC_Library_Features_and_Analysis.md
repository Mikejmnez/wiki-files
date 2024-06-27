## Features

*Features likely needed by any general C-language client-side API for
DAP*

### features already implemented in oc

1.  get variables (primitive types)
2.  get array varaibles (of primitive types)
3.  get attributes

### features that can be implemented with little effort

*little means using the current library's structure and some more code*

1.  make print representations for all responses (DAS, DDS, DataDDS)
2.  http authentication support
3.  SSL suport
4.  cookie support
5.  .dodsrc support (read and write)
6.  compression support
7.  testing - write automated tests
8.  nightly builds
9.  automated build (autoconf, make, libtool)
10. more typical API (open, read, close all based on an opaque
    parameter - object - and C's existing data types)

### features that will be hard to implement

*hard means extending the existing library's structure/implementation*

1.  get structures (flat and nested)
2.  get sequences (flat and nested)
3.  get grids
4.  get arrays of Structure
5.  parse XML(DDX, DataDDX\*)
6.  shared dimensions\*
7.  groups\*
8.  allow constraints for the DDS
9.  allow constraints for the DataDDS (e.g., lat or lon or **lat and lon
    togther**)
10. DAP 3.x protocol version negotiation

## Use Cases

### Use Case \#1

#### Description

Write a client for oc that is a command line tool which allows
constraints and prints the desired schema and the data (when
appropriate) in the getdap format.

#### Goal

This use case is designed to identify the essential functions that are
required in order for a command line client from the current Ocapi code
to be replicated in the oc.

#### Summary

The current Ocapi code has undergone a major rewrite which has provided
a new branch of the original code called oc. The oc code branch is
approximately half the size of the Ocapi code branch and the complexity
of the code is reduced dramatically. The Ocapi code has a client that is
a command line tool to allow an end user to access a desired schema,
with or without constraints, and have that schema printed out in a
special format. This use case is designed in principal, to see if the
new oc code will allow for the same functionality as the Ocapi code in
order to achieve the same results that the Ocapi currently provides. If
the oc code is not as capable as the current Ocapi code this use case
will identify the missing criteria and give a general idea of how much
coding it will take to get the new oc code branch on the same
functionality level as the Ocapi code branch. If the oc proves to be a
more efficient means of running the c library then it is plausible that
the oc code will be adopted as the current Ocapi code and any new
development will occur on this code branch and the Ocapi code will no
longer be maintained.

Note: Since it appears that the oc code does not include functions to
print the results of a query, we might take this as an opportunity to
write functions that mimic libdap's output functions/methods and not
those of the current Ocapi library.

#### Actors

Primary: end user- this is any individual that is using the system to
retrieve data utilizing the dap standard for individual use C libraries
Api's Clients

#### Preconditions

The oc code must be properly compiled with no errors. The oc code must
contain all necessary functions in order to achieve the desired outcome.
These include functions that allow the data to be read in and printed in
the appropriate format, allow constraints, proxy support, http
authentication, SSL, cookies, compression, .dodsrc file support, and
password and username provisions.

#### Triggers

An end user downloads the oc code, builds and compiles the code. A
command line is open and the newly created get_oc client is initiated
with a proper url and constraints if desired.

#### Basic Flow

1.  Identify URL
2.  Initialize the oc
    1.  Provide proxy support
    2.  Provide http authentication
    3.  Provide SSL support
3.  Allow for the das, dds, and dods schemas
4.  Read url specified
5.  Retrieve the data from the above url
6.  Print the DDS, DAS or DataDDS

#### Alternate Flow

Provide error handling for all of the specific functions listed above

#### Post Conditions

The user will request information/data using the new client (that has
been copied from, or based on, the current Ocapi and/or libdap code) and
that particular data set requested will be returned and printed to the
screen.

#### Activity Diagram

#### Notes

The oc code has to be able to read and print sequences, arrays,
structures, strings, primitive data types, array of structures, nested
sequences, grids, and nested structures.

#### Resources

|                          |           |                              |                           |                    |
|--------------------------|-----------|------------------------------|---------------------------|--------------------|
| Resource                 | Owner     | Description                  | Availability              | Source System      |
| *oc code and Ocapi code* | *OPeNDAP* | *C library for dap protocal* | *Continuous availability* | *OPeNDAP web site* |

## Definitions

## Background

## Deliverables

## Period of use