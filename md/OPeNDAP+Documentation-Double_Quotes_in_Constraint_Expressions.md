## DAP 3.2

Double quotes (") can be used to quote any identifier in a Constrain
Expression.

## Rationale

Many data sets us identifier names which contain characters other than
those that qualify as an identifier in a programming language like C.
For example, using an ampersand (&) in a variable name will cause a
conflict with the meaning of an ampersand as a prefix AND operator in
the CE's selection clause. Enclosing the identifier name in double
quotes resolves the ambiguity. The same can be said for other DAP/CE
meta-characters like the dot (.) which serves as a separator for the
name of a Structure, et c., and its fields.

### examples

scale factor
"scale factor" or scale%20factor or "scale%20factor"

<!-- -->

level_2 data.scale factor
"level_2 data"."scale factor" and the %20 variants. Note that each
component of the full field name is quoted separately so the dot (.)
will be read by the parser

<!-- -->

level.2.data&errors
"level.2"."data&errors" In this example, the first dot is part of the
variable name and the second is a separator between a field and its
container

## Implementation Note

In libdap++ we use a pair of functions to encode and decode strings so
they meet the criteria set forth for characters in a URL as described by
RFC 2396. We also store strings (identifiers) internally in the library
so that they have spaces 'escaped' using *%20* because this makes it
easier to parse names with spaces (since most scanners group lexemes
into tokens based on whitespace). We assume that all information in a
URL will have been encoded using the *%<hex-digit><hex-digit>* notation
as per RFC 2396 and decode the identifiers when they are read from the
URL, with the exception that we allow some characters (e.g., spaces) to
remain encoded because it simplifies the implementation of our scanners
and parsers. There's no need to do this. However, we are mentioning it
here because it seems like a useful trick when dealing with the
constraint expressions.