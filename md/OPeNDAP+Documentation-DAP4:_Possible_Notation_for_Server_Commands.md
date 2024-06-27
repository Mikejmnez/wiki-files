[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

Looking to the future, it is clear that eventually our query language,
or more generically our [server
commands](DAP4:_URL_Annotations "wikilink") must encompass three classes
of computations.

1.  Queries in the DAP2 sense,
2.  Commands to control the processing of requests on the server (i.e.
    thing like caching),
3.  Server-side processing.

I want to propose a notation for everything in the URL after the "?". I
think this notation has the ability to represent a wide variety of
features without, I hope, being too generic.

## Proposal

The notation is basically nested functions combined with single
assignment variables. A semantically nonsensical, but grammatical
example would look something like this.

`?svc("cmd");$x=f("string17",g(h(12))),f2($x,[0:3:10])`

Everything past the "?" is in the form of a semi-colon separated list of
expression lists. An expression list is a comma separated list of nested
function invocations, possibly assigned to a variable. Anything that
begins with a dollar sign is considered a local, temporary, variable,
anything that does not look like a function call (i.e. name followed
immediately by left paren) is assume to be what I will call a non-quoted
string constant; standard quoted string constants are also allowed. Each
function has an arbitrary number of argument expressions separated by
commas.

BTW, the term "single assignment" means that a variable may only be
assigned to once, but may be referenced as many times as desired after
that.

One important issue involves providing namespaces for function names.
That is, there will be standard pre-defined functions, server-specific
functions, and even dynamically defined functions. In order to define a
namespace, it is necessary to provide some kind of marker in function
names. I have chosen the Java fully qualified name model in which the
marker is the dot character.

The idea is that any function name has a fully qualified name specified
using dot separators. Names that have no dots (modulo import below) are
assumed to be in the pre-defined standard namespace.

The management of that namespace tree must be determined, with some
mechanisms for allowing others to assign functions in the namespace.
Also, some form of import mechanism ala Java would be desirable as a
separate server command.

## Syntactic and Lexical Structure

I have defined a preliminary [syntax
document](http://dl.dropbox.com/u/53929684/query.y) and [lexical
document](http://dl.dropbox.com/u/53929684/query.lex) for the ideas
presented here.

## Discussion

The purpose of providing multiple expression lists separated by
semi-colons is to support the a visible notation for specififying server
commands separate from constraint evaluation.

My hypothesis is that this notation should also be able to handle most
kinds of server side processing by defining and composing functions.

The standard projection+selection constraints of DAP2 can be represented
using a special constraint() function whose argument is the standard
DAP2 constraint. Alternatively, one could define a collection of nested
functions to do the same thing.

An important issue involves the construction of a DDX from a constraint.
I have begun this discussion
[here](DAP4:_Constructing_a_DDX_from_a_Query "wikilink")

I hypothesize that Ferret notations coud be represented in my proposed
function notation without having to clutter up the URL format. Consider
this Ferret expression.


http://.../thredds/dodsC/hfrnet/agg/6km_expr_{}{let
deq1ubar=u\[d=1,l=1:24@ave\]}

A possible equivalent (assuming I understand the Ferret expression)
might look like this.


?avg(u\[1,1:24\])

*-Dennis Heimbigner*

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")