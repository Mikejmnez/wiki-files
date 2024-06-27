[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

Assuming one believes that the multipart-mime boundary is indeed a
unique string in the response, it makes the existing specification for
chunking of the data part of Data DMR redundant.

## Proposal

I propose that we get rid of the chunking and instead modify the
multipart-mime representation to be three parts instead of the current
two. The additional, third part would indicate the success or failure of
the preceding parts. Because the multipart-mime boundary is (by
assumption) unique, it is always possible to unambiguously locate the
final success/failure part. This satisfies the original reason for using
chunking, which was to allow for the insertion of an error message into
the output stream at any point.

This proposal has a number of advantages.

1.  It simplifies the client and server processing by eliminating the
    extra processing required by chunking.
2.  It removes the need to test the chunking layer.
3.  It no longer duplicates the existing HTTP chunked transfer encoding.
4.  It works for any data format: binary, json, protobuf, utf. They all
    are treated the same.

~~The cost is in searching the incoming stream of bytes for the
boundary. Using, for example, the Boyer-Moore \[1\] fast string search I
believe that cost is low, especially since the boundary string is
long.~~

Note that every response will end with the following.

`(1) --`<boundary>
`(2) success info or error info`
`(3) --`<boundary>

So this trailer can be easily located by searching backward from the end
of th response to locate the first boundary (line 1). This only requires
searching at most a few hundred bytes.

[Dennis Heimbigner](User:dmh "wikilink")

~~\[1\] [Boyer-Moore
Search](http://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_string_search_algorithm).
Note that this page also contains both C and Java implementation code.~~

### Possible Extension

It is possible to extend this proposal to do proper semantic chunking by
extending the multipart-mime format from 2+1 parts to 2+N parts. Each of
the N middle parts would contain the data for a single variable.

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")