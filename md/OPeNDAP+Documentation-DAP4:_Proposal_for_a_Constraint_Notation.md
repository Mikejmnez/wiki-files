[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

Assuming that we accept that sequences are intended to be relations (or
at least act like relations), then it is necessary to provide the
appropriate manipulations for sequences, namely the relational operators
select, project, and (optionally) join.

## Proposal

Assume that a constrained request (a constraint) is a comma separated
list of expressions. Each expression computes a variable that will
appear in the DDX sent back to the caller.

I will use this sequence as a running example.

``` xml
<Sequence name="S">
  <Int32 name="f1"/>
  <Float32 name="f2"/>
  <UInt64 name="f3"/>
</Sequence>
```

In general, each expression will have the form *P* or *P\|B* where P is
a projection expression, B is a boolean selection *predicate*, and the
"\|" marker can be read as "where" in the SQL sense. The selection part
is optional and can only occur when the projection part is a sequence.

We will ignore group scoping notations (i.e. /x/y...) for now. I will
use, however, dot notation to indicated choosing a field in a sequence
or structure.

Without being too verbose with syntax, a *predicate* is a typical infix
expression involving the *and* (&) operator, the *or* (\|) operator, the
*not* (~) operator, parenthesis, and fields of a sequence. \[Note that
there is no syntactic ambiguity in using "\|" both or "where" and for
"or"\].

Using the running example, I might have an expression of this form.

    S.f1>23&(S.f1=S.f3|S.f2<5.0)

### Relational Projection

I propose to extend the \[...\] notation from DAP2 to include the
relational projection operator. For example, the SQL statement

    select S.f3 where S.f1 > 5

would be translated to

    S[f3,f1]|S.f1>5

where the column projection, S\[f3,f1\], both picks out columns and
alters their order. Note that \[...\] in this case is defined such that

    S[f3,f2][f2]

is equivalent to this.

    S[f2]

### Joins

John Caron has raised the issue of supporting joins. Joins add
substantial complexity and can be very costly to compute. Since they can
be computed on the client side, they are not essential. The same can be
said for projection and selection, of course, but the computational cost
is less. They are useful and represent an important class of server-side
computation, so I think they ought to be supported.

Again, my rule is that it must be done in such a way as to make it
simple to translate it into a typical SQL expression. Let me add this
sequence to the running example.

``` xml
<Sequence name="T">
  <Int32 name="a"/>
  <Float32 name="f2"/>
</Sequence>
```

A join expression in SQL might look like this.

    select S.f1,T.f2
    where S1.f3 = T.f1

The notion of a natural join also exists where the join is automatically
defined over common column names. In this case, the join would be over
column f2 because it is common to both relations. It would be equivalent
to this SQL expression.

    select S.f1,S.f2,S.f3,T.f2
    where S1.f2 = T.f2

The natural join is more restricted because it uses common naming to
decide what to join. Essentially all relational databases support the
more general form of join so it is the preferred choice for my purposes.

### Join via Cross Product

There is a strong connection between join and the cross-product
operation. The cross product of two relations, S and T say, is a new
relation containing all the columns of both S and T, with renaming as
necessary to achieve uniqueness. Applying a selection and projection to
the cross product can be used to compute any join over those same two
relations.

The first of the two examples above cannot be represented in the
notation as presented so far because it needs to talk about two (or
more) sequences at the same time. To handle this, I use the
cross-product idea using the operator "\*". So, the first example above
is written as follows.

    S*T[f1,f2]|S1.f3 = T.f1

\[Note, I need to verify that a simple algorithm exists to convert this
to the desired SQL statement\].

## Rationale

The key idea here is to provide a notation that maps easily and
unambiguously into typical SQL statements of the form e.g.

    select C1,C2,C3
    where B(R)

where the Ci are projection columns and B is a boolean expression over
the columns of some relation R (or in the case of joins R1...Rn).

One big change compared to DAP2 is using the "\|" notation to place the
selection operation right next to the sequence/relation to which it
applies. This again makes conversion to SQL simpler.

My belief is that the above revised constraint syntax provides a much
better defined and ultimately easier to understand notation than the
existing DAP2 notation.

### Misc. Notes

- Note that the {...} notation from DAP2 is removed because the "or"
  operator obviates the need for {...}.

[Dennis](User:dmh "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")