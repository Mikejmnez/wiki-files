[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Introduction

An important aspect of query evaluation has to do with the construction
of what may be referred to as a DATADDX. It defines the structure of a
DDX that is returned to the caller as the result of a request to a DAP4
server.

The returned DATADDS for DAP2 was relatively well-defined as long as one
did not use any server-side functions. As far as I know, how the result
of such a function call was to be defined in the returned DATADDS was
never formally (or even informally) defined.

It can be expected that DAP4 will make much more use of complex queries
involving a combination of constraints and server-side functions. So
rules must be defined as formally as possible to define what the result
of any possible query will look like.

A specific problem is that the resulting DATADDX may have only have a
loose relation to any DDX representing the raw dataset. This is because
server-side computations will not have been represented in the original
DDX, but only in the DATADDX.

Initial thoughts based on my "[Possible Notation for Server
Commands](DAP4:_Possible_Notation_for_Server_Commands "wikilink")"
document.

1.  All functions have a defined "return type". Since, unfortunately,
    DAP4 -- like CDM -- has no notion of separately defined type, the
    return type of a function would be defined as a variable possibly
    nested in some set of groups and with some associated attributes.
2.  A function may be defined to have a "void" return type, which means
    it is executed for its side-effects on the server.
3.  Any expression that is not assigned to a variable and does not have
    a void return type will have its return value returned to the caller
    as part of a DATADDX.
4.  As a consequence of \#3, rules must be defined for merging the
    output DATADDX of multiple expressions into a single DATADDX. The
    simplest merge might operate as follows:
    1.  A tree of groups is defined using the groups that are associated
        with the return type of an included expression (see \#1 above).
    2.  The variables, with their attributes, are inserted into the
        group tree. If there are duplicate variables, then an error is
        reported (need a better soln for this case).

*Dennis Heimbigner*

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")