[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

John Caron has argued that supporting nested sequences is desirable
because it provides a natural representation for certain datasets such
as trajectories of trajectories. This is an important insight, and it
should be supported in some form.

The key point is that we have a sequence with one or more fields that
are themselves sequences. Consider this example. <font size="2">

``` xml
<Sequence name="SQ1">
  <Int32 name="f1"/>
  <Float32 name="fx"/>
  <Sequence name="SQ2">
    <Float32 name="fy"/>
    ...
  </Sequence>
</Sequence>
```

</font>

There is an implicit join column that connects an instance of SQ1 with
an instance of SQ2. Asking for field e.g. SQ1.SQ2.fy in effect joins SQ1
and SQ2 on some unnamed, untyped field/column (or columns) common to
both and then projects out column SQ2.fy. Without some kind of syntactic
addition, there is actually no way to explicitly get the join column(s)
of SQ1 or SQ2.

One question is: if we request the server for SQ1.SQ2.fy, what is
returned by the server? To be consistent with, for example, the request
for SQ1.f1, what must be returned is a sequence of records from SQ1 with
a field, SQ2, that is itself a sequence of records from SQ2. This tends
to be a fairly complex data structure.

## Proposal

I propose to support nested sequences using Sequence/relation flattening
combined with concepts of keys and foreign keys. The trade-off becomes a
complex data structure for nested relations versus a somewhat more
complex query plus additional work on the client side.

Consider this example. <font size="2">

``` xml
<Sequence name="SQ1">
  <Key name="f1" type="Int32"/>
  <Float32 name="fx"/>
  ...
</Sequence>
<Sequence name="SQ2">
  <ForeignKey key="/SQ1.f1"/>
  <Float32 name="fy"/>
  ...
</Sequence>
```

</font> This approach is a direct representation of the idea of a
foreign key as defined in traditional relational database theory. It
specifically indicates how two Sequences can be combined (effectively
using join) based on the ForeignKey element in one Sequence pointing to
a Key element in another Sequence.

The <Key> element in the example in SQ1 is equivalent to the following
field. <Int32 name="f1"/> as far as its role as a field in the Sequence.

We might also consider this alternative form. <font size="2">

``` xml
<Int32 name="f1" key="true"/>
```

</font> Here the "keyness" is indicated by an XML attriute versus a
<Key> element.

In either case, other Sequences can refer to that key using a
<ForeignKey> element.

As far as fields go, defining a foreign key implicitly includes the key
being referenced (f1 in our example) as a field in the Sequence.

So, the above example is equivalent to the following. <font size="2">

``` xml
<Sequence name="SQ1">
  <Int32 name="f1"/>
  <Float32 name="fx"/>
  ...
</Sequence>
<Sequence name="SQ2">
  <Int32 name="f1"/>
  <Float32 name="fy"/>
  ...
</Sequence>
```

</font>

Once we have keys and foreign keys, it is easy to represent two
Sequences as if they were a nested Sequence. So, our example above could
be presented to a user as the following equivalent nested Sequence.
<font size="2">

``` xml
<Sequence name="SQ1">
  <Int32 name="f1"/>
  <Sequence name="SQ2">
    <Int32 name="fy"/>
  </Sequence>
</Sequence>
</Sequence>
```

</font> Note that the common key field (f1) only appears in the outer
Sequence because its existence with the same value is implicit in the
nesting.

## Rationale

This proposal keeps the data model simple, while still allowing client
code to present the appearance of a nested sequence to the user.

## Discussion

[Dennis](User:dmh "wikilink") I am having second thoughts about how to
support nested relations. The problem is not with the key idea, but
rather how to generate a query to simulate a query against nested
relations. If one actually supported nested relations in the model, then
a query could be done with a single query. With the relations flattened,
it requires two queries: one against each relation. This is both
confusing and more complex than is desirable.

[Dennis](User:dmh "wikilink")

Yes, i was wondering the same. Can you give examples of what the
quesries look like?

[JohnCaron](User:JohnCaron "wikilink") 15:04, 30 May 2012 (PDT)

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")