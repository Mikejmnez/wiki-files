[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

The grid construct as originally established in the DAP2 protocol has
been a source of problems from its inception. The evolution of the
notion of coordinate variables makes its use in its current form (or
even closely similar forms) untenable.

## Problems Addressed

### Grid as scoping/lexical container

This means that properly sharing coordinate variables is not possible
without duplication, which is highly undesirable.

Consider the following situation. <font size="2">

        Arrays: D1(x,y), D2(y,z), D3(x,z).
        coord vars: x(x), y(y), z(z)

</font> No grid, as currently defined can represent this because the
three coordinate variables x(x), y(y), and z(z), cannot be properly
distributed across needed three grids without duplication. The only way
this can work is if all the Arrays and all the coordinate variables
reside in a single grid; not, I maintain, a useful solution. Further,
the Grid must change if new arrays are defined that use any of the
coordinate variables, D4(x,w), for example.

### Grid projections

When a projection is applied to a grid, the result cannot be a grid.
This has been an ongoing source of problems in DAP2 where projecting the
array component of a grid results in a structure. From the point of view
of semantics, this is a really bad idea.

### Multi-dimensional coordinate variables

When representing point data, it is desirable to have coordinate
variables distinguished using more than a single dimension. Consider the
following: <font size="2">

        array: temp(x,y,z)
        coordinate vars: lat(x,y,z), lon(x,y,z), and depth(x,y,z).

</font>

Here we are trying to represent point data where each point is defined
by three dimensions: lat, lon, and depth. Grids are not capable of
properly representing this case. I should note that neither is, for
example, netcdf-3 or netcdf-4. CDM can do it, by only by encoding the
proper relationships as attributes with complex internal structure.

### Coordinate Variable Duplication

In examining a large number of DAP2 DDS's, I note that coordinate
variables inside grids are almost always duplicated outside the grid. My
hypothesis has been that this a result of the fact of problem (1) above.
In any case, this proposal below would obviate the need for duplication.

## Proposal: Grid as mapping

Rather than making grids be scope containers, grids need to be simple
relationship instances between an array and its coordinate variables.
This would be done by associating the coordinate variables with an array
variable.

Specifically:

1.  The Grid data type in DAP4 should shed the enclosing lexical scope
2.  Grid is a relation that binds one or more coordinate variables (aka
    maps) to one Array.

Using OGC coverage terminology, we have this.

1.  The maps specify the *Domain*
2.  The array specifies the *Range*
3.  The Grid itself is a *Coverage*
4.  The Domain and Range are sampled functions

There are a number of constraints on the form of maps and their
relationship to the Array.

Assume we have a (grid) array of the form

    float32 A[d1...,dn]

and associated maps

    float32 M1[d1,d2];
    float32 M2[d3,d4];

Assume the following definitions:

- Let {A} be the set of dimensions of A, namely {d1,...dn}.
- Let {D} be the set of dimensions mentioned in any of the map
  variables, so in our case above {D} = {M1} union {M2}.
- Let \|A\| be the rank of A (n in this case).
- Let \|{...}\| be the number of elements in a set.
- Let {Mi} be the set of maps (= {M1,M2} in this case).

Using these notations, the contraints are as follows.

1.  \|Mi\| \<= \|A\| : i.e. each map var has a dimension no more than
    that of the grid array.
2.  {Mi} has no fixed upper bound : i.e. there can be as many maps as
    desired.
3.  {D} = {A} or {D} is a subset of {A} : i.e. every named dimension
    mentioned in the map variables must appear in the set of dimensions
    of A.
4.  \|A\| = \|{A}\| : i.e. the dimensions of A may not contain
    duplicates so A\[x,x\] is disallowed.
5.  {Mi} is in fact a set, which means that any duplicates are ignored
    and the order is irrelevant. So {Mi} = {v1,v1,v2} is the same as
    {m1,m2} is the same as {m2,m1}.

## Discussion

The remaining issue seems to be how 'point data' are represented. There
are two candidate representations for point data:

- CDM/CF-1.6: where a grid/coverage is used where the array has one
  dimension and there are several maps (example: temperature data - one
  array with one dimension - has two maps, one for lat and one for lon -
  each map is one dimension).
- Using Sequence: The same data can be represented using a sequence with
  three columns (one for temperature and one each for lat and lon).

There's no debate about the suitability of each of the above to
represent 'point data'.

However, there is debate about how best to *transport* and/or
*represent* this information. That is, given that many systems will
store point data in a relational database while many others will adopt
CF-1.6 and use arrays to store the data, does adopting this mean that
servers (in the aggregate) will provide two different representations
for the same kind of data? My ([Jimg](User:Jimg "wikilink")) prediction,
based on the past, is that servers will have to be modified to provide
the kind of responses different clients expect (rather than the case
where clients are written to process each of the representations). This
is a function of users/clients tending to cluster around different
application areas. It may not matter for within-domain access, but it
will hinder cross-domain access.

We have moved this discussion to the proposal about [Sequences and
VLens](DAP4:_VLens_(and_Sequences) "wikilink").

[Dennis Heimbigner](User:dmh "wikilink")(5/17/2012) The above proposal
allows for map variables that are fields of a dimensioned structure.
This means that map names have to be prepared to deal with names like
this.

    /g/S[0].f

I think this is undesirable and I propose that we do two things.

1.  Distinguish variables from fields; a variable is a top-level decl in
    a group, a field is a decl within a structure or sequence.
2.  Require that all map variables and all variables with maps be
    variables and not be allowed to be fields.

[Jimg](User:Jimg "wikilink") 13:34, 17 May 2012 (PDT) I agree. Allowing
Maps and 'Variables with Maps' to be fields is a needless mess. If there
is some dataset that is really like that, the DAP layer, which is
necessarily an abstraction, can hide those details. My preference in
this case is for a bit more complexity on the server side to simplify
the logic of clients.

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")