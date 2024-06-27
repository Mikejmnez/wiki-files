# Abstract

[Jimg](User:Jimg "wikilink") 14:31, 22 May 2012 (PDT) Notes from an open
discussion between OPeNDAP and Unidata developers.

Constraints must support subsetting and sampling on the basic data
types. These operations are keyed to the types without any
domain-specific features.

The constraint language will use server-side functions to support
domain-specific subsetting operations. This will enable multiple sets of
these kinds of operations.

This project will define a set of subsetting functions that encapsulate
the operations provided by the Common Data Model (CDM).

The functions (or named sets of functions) applicable will be named in
the 'get capabilities' response for each data set, so that clients at
least have a shot at knowing what will work. Given that these get
capabilities responses can be rendered in HTML, people with a browser
can see this information too.