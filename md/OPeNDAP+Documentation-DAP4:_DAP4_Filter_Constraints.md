[\<\<back to OPULS Development](OPULS_Development "wikilink")

Revised: 9/21/2012 [Dennis](User:dmh "wikilink")

Authors: [Dennis Heimbigner](User:dmh "wikilink"), John Caron, Ethan
Davis

## Background

One of the unaddressed issues for sequences is the degree to which they
should support relational operators, and specifically joins and
equivalent operations such as selections involving multiple sequences.

Some time ago, in one of our telecons, Nathan proposed the idea of
forcing all complex relational queries to be pre-built on the server.
The idea was that this would allow the server to limit relational
queries to those that could be performed in a computationally efficient
and secure fashion.

I am making a proposal for an alternative to complex client-specified
queries under the assumption that we adopt the idea of pre-defined
server-side queries.

## Proposal

First, I assume that all complex queries are pre-built on the server in
the form of server-side functions. The term "complex" here effectively
means any query that involves joins (explicit or implicit) or unions or
intersections.

Under this assumption, I would then propose that we replace the DAP2
selection constraint with a combination of projection plus "filter"
constraint. I deliberately use the term "filter" instead of "selection"
to indicate its more limited semantics.

The filter is a predicate over the fields of a sequence record. In
effect it specifies a subset of sequence records to transmit to the
client. When the predicate applied to a record evaluates to true, the
record is tranmitted, otherwise it is suppressed.

Projection (in the SQL sense) is supported by allowing a reference to a
Sequence to select only certain fields to be transmitted.

The proposed syntax for filters is as follows:

    <sequence-projection> | <filter-predicate>

The '\|' can be read as "such that".

The syntax of the filter predicate would be a standard expression where
the atomic (leaf) elements of the predicate would be either fields of
the sequence record or constants. The allowed operators would be boolean
(&& \|\| !), comparison (= != \> \< \>= \<=) and arithmetic (+ - \* /
%). I do not think there is any syntactic ambiguity here, but to be
sure, we could require the filter predicate to be enclosed in
parentheses.

The "sequence-projection" has the following syntax.

    <sequence-name>'[' record-variable-name (',' record-variable-name)* ']'

The list of record names indicates which part of each sequence are to be
transmitted. Note that filtering is applied before sequence projection.

## Examples

Suppose we have this sequence variable.

    <Sequence name="S">
      <Int32 name="field1"/>
      <Float32 name="field2"/>
    </Sequence>

Example filters might be as follows:

1.  S\|(field1 \> 5 && field2 \<= 100.0)
    This would transmit all records in S that met the specified
    predicate.
2.  S\[field2\]\|(field1 == field2)
    Transmit projected records of S matching the specified predicate.
    The projected record would contain only the field "field1".

Consider this more complicated example.

    <Sequence name="S">
      <Int32 name="field1"/>
      <Structure name="field2">
           <Int64 name="v1"/>
           <Int64 name="v2"/>
         <Dimension size="15"/>
         <Dimension size="*"/>
      </Structure>
      <Float32 name="field3"/>
    </Sequence>

Ideally, one might specify the following filter.

1.  S\|(field2\[7\]\[5\].v2 == 37.0)

I say "ideally" because it is not obvious to me that references to
specific instances of dimensioned object is of any use to anyone. I
would need a use case to be convinced.

\[Note: even more strongly, I am not sure I like being able to do this.
I would prefer restricting the filter variables to refer to only atomic
typed top-level fields of the Sequence (see next section).\]

## Implementation

Implementation of this concept is not quite as simple as one might
think. The key problem has to do with searching a record instance to
locate the specified fields.

Consider our previous example. In order to locate, say, "field3" one
might have to traverse all of the dimensioned structure "field2". The
efficiency of this is highly dependent on how the sequence records are
represented on the server.

## Discussion

The advantage of the filter approach is that it should be easier (but
not necessarily easy) to implement than more complex queries; it
certainly would be easier to implement than the DAP2 select constraints.
Further, it is not dependent on the sequence being represented in a
relational database.

I hypothesize that the filter mechanism would be capable of representing
most of the real-world examples that have been encountered in DAP2.

Note that limiting complex queries to be encapsulated in server side
functions is without loss of generality since someone could be so
foolish as to define a server-side function that took a full SQL query
as an argument and executed it on the server.

[ndp](User:Ndp "wikilink") 07:10, 21 September 2012 (PDT) I like this
idea, and I want to put forward this corollary: What if we were to say
that we would allow the use of this type of constraint on arrays (i.e.
subsetting by value), and as a result the request would return a
Sequence of the matching values? In other words, whenever you use a
"filter" constrain the returned object is ALWAYS a Sequence.

[Dennis](User:dmh "wikilink") 3PM 9/21/2012 With respect to relational
databases, I note that it may be the case that the Sequence S is
actually being stored in a relational database on the server. In this
situation, it is possible to turn the filter into an equivalent SQL
expression except for references to structure fields e.g. field2. It is
likely that such fields would be represented as BLOBs in the database
and hence not easy to apply a predicate to them. This is one reason to
consider limited the types of fields that can appear in a filter
predicate.

[Dennis](User:dmh "wikilink") 3:30PM 9/21/2012 I also should note that
the form S\[field1\] is ambiguous with respect to the range syntax
proposed in the DAP4 spec. field1 could be interpreted as being a name
of a defined range (e.g. using field1=\[1:3:5\]). This can only be
disambiguated by determining (in a context sensitive manner) that S is a
sequence.

[Jimg](User:Jimg "wikilink") 08:54, 24 September 2012 (PDT) I think this
is a good way of looking at Sequences and ways to extract elements from
them 'by value,' which is very important. I agree with all of Dennis'
stipulations and, I believe, all of his conjectures. Also, I like
Nathan's corollary, as well. I like less the idea that we represent
these filter operations as server functions because of exactly the same
reason Dennis gave in an email - that doing so provides too much 'wiggle
room' for servers. I think thse operations on this datatype are not
~~operational~~ optional in the same way that hyperslabbed access to an
array is not optional.

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")