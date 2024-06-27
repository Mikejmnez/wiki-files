## Background

Assume this running example

``` xml
<Structure name="ST">
  <dimension name=z/>
  <Int32 name="f1">
    <dimension value=5/>
  </Int32>
  <Int32 name="f2">
  <Int32 name="f3">
</Structure>
```

In DAP2, a complete and possibly range-projected structure could be
returned using an expression such as this.

    ST[1:7]

\[Note: a range projection is the typical DAP2 projection of a set of
array dimensions such as A\[1:10\]\[31:2:40\] \].

If, however, one wanted to return some non-empty subset of the fields of
that structure, one had to resort to using multiple separate expressions
– one for each field of interest – and rely on the implicit rule that
such separately chosen fields would be placed into the same structure.
So, to get ST with only fields f2 and f3, one had to write something
like this.

    ST[0].f2,ST[0].f3

This introduced the obvious problem of having to figure out which fields
should be grouped together. If I had written this

    ST[0].f2,ST[1].f3

this would have to appear to the client in the resulting DDX as two
separate structures using some rename algorithm.

## Proposal

I want to propose that we extend the \[...\] notation to allow the
specification of fields (as I have done for [relational
projections](DAP4:_Proposal_for_a_Constraint_Notation "wikilink")). So
the above example would be written like this.

    ST[0][f2,f3]

Additionally, nesting would be allowed. This would allow one to write
things like this.

    ST[0][f1[5],f3]

[Dennis](User:dmh "wikilink")