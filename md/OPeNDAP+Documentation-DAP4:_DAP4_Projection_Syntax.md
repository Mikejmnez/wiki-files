[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

Currently, the syntax for "projection" constraints in the spec looks
roughly like this:


/group1/.../groupn/struct1\[a1:b1\].field2\[a2:b2\].struct3\[a3:b3\].field4\[a4:b4\]

In looking how John handled this in CDM, I noticed that he used a
different but equivalent notation that strikes me a superior.

## Proposal

Basically, the proposal is to push all of the indexing to the end of the
expression. So we now would have (using the above example):


/group1/.../groupn/struct1.field2.struct3.field4\[a1:b1\]\[a2:b2\]\[a3:b3\]\[a4:b4\]

## Rationale

Assuming one has the rank info for the intermediate structs and fields,
the two forms are equivalent. The advantage of this format is that it
has a simpler syntax and is easier to parse. It is basically an FQN
followed by a sequence of slice specs.

[Dennis](User:dmh "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")