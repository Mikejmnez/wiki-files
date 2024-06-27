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

NB: The current Data Model page in the straw man design already does
this. Unfortunately, I used XML for the examples (which muddies the idea
of an abstract information model with one particular representation of
that model) but I think what is presented there is the same as this
proposal.[Jimg](User:Jimg "wikilink")

Rather than making grids be scope containers, grids need to be simple
relationship instances between an array and its coordinate variables.
This would be done by associating the coordinate variables with an array
variable. For example, the first case above (D1,D2,D3) might be
represented as: <font size="2">

    <variable name="D1"...>
       <map coordinate="x"/>
       <map coordinates="y"/>
     </variable>
    ...

</font>

The case of point data would be represented as follows: <font size="2">

    <variable name="temp"...>
       <map coordinate="lat"/>
       <map coordinates="lon"/>
       <map coordinates="depth"/>
    </variable>

</font> Note that the dimensions can be inferred from the specified
coordinate variables.

*-Dennis Heimbigner*

## Discussion

[Jimg](User:Jimg "wikilink") 16:05, 27 March 2012 (PDT) I moved the
existing *Discussion* section to *Old Discussion*; I think our group has
converged on the following, with one topic to be resolved:

1.  The Grid data type in DAP4 should shed the enclosing lexical scope
2.  Grid is a relational data type that binds one or more coordinate
    variables (aka maps) to one Array.
3.  In a Grid, we can define the following terms/concepts:
    - The maps specify the *Domain*
    - The array specifies the *Range*
    - The Grid itself is a *Coverage*
    - The Domain and Range are sampled functions
    - We are leaning heavily on the OGC abstract coverages specification
      when we use these terms...
4.  If the Grid array has rank *N*, each of its *M* maps can have at
    most rank *N* (but may have less).
5.  All maps have at least one dimension *(we didn't talk about this,
    but I think it's implied by all of our discussions so far)*
6.  That maps match the array in size, for the dimensions they share.
    *(There is certainly a better way of saying this...)*
7.  There can be any non-zero number of maps. That is, *M* can be 1 \<=
    *M* \< *infinity*. *This point is contentious; discussed below*

### Proposed Additional Restrictions

John, Ethan, and I (Heimbigner) had a discussion about this and propose
the following additional set of semantic restrictions. I will discuss
them in the context of this example (using, a non-xml syntax, that I
hope is clear).

Assume we have an array of the form

    float32 A[w,x,y,z,d1...,dn]

and associated maps

    float32 var1[w,x];
    float32 var2[y,z];

Assume the following definitions:

- Let {A} be the set of dimensions of A, namely {w,x,y,z,d1,...dn}.
- Let {D} be the set of dimensions mentioned in any of the map
  variables, so {D} = {var1} union {var2}.
- Let \|A\| be the rank of A (n+4 in this case).
- Let \|{...}\| be the number of elements in a set.
- Let {vari} be the set of maps (= {var1,var2} in this case).

James' set of restrictions are represented as follows.

1.  \|vari\| \<= \|A\|: each map var has a dimension no more than that
    of the grid array.
2.  {vari}\| has no fixed upper bound (i.e. there can be as many maps as
    desired.

We disagree with the following of James' restrictions

1.  John has made a cogent argument for allowing scalar map variables,
    which violates James' restriction that \|vari\| \> 0. (John, can you
    provide the rationale?).

In addition, we propose the following additional restrictions.

1.  {D} = {A} or {D} is a subset of {A}: i.e. every named dimension
    mentioned in the map variables must appear in the set of dimensions
    of A.
2.  \|A\| = \|{A}\|: that is the dimensions of A may not contain
    duplicates so A\[x,x\] is disallowed. This is necessary to be able
    to unambiguously map a subset of dimensions of A to the proper map
    variable.
3.  {vari} is in fact a set, which means that any duplicates are ignored
    and the order is irrelevant. So maps=v1,v1,v2 = v1,v2 = v2,v1.

\[Note: I am removing most of the following until such time as we have a
use case that decides the issue one way or another\].

~~The following restriction needs discussion. 1. {A} must consist of
only named dimensions. 2. {D} must consist of only named dimensions. 3.
For each map variable vari, /vari/ must constitute a contiguous
subsequence of /A/. That is the following example is illegal because x,z
is not a contiguous subsequence of x,y,z.
float32 A\[x,y,z\];
with map variables
float32 var1\[x,y\];
float32 var3\[x,z\]; 4. For each pair of map variables, vari and varj,
either {vari} = {varj} or {vari} intersect {varj} is the empty set. That
is map variables may have exactly the same dimension set as another map
variable or it shares no dimensions in common. This restriction comes
from the question of how to interpret this example.
float32 A\[x,y,z\];
with map variables
float32 var1\[x,y\];
float32 var2\[y,z\];~~

### Coordinate Systems XML Element

John has noted that in CDM, he introduced a notion of Coordinate System
which is a set of maps defined independently of any variable. In xml,
this would be something like this.

    <CoordinateSystem>
    <map variable="var1"/>
    ...
    <map variable="varn"/>
    </CoordinateSystems>

The question to discuss is: should be add such a concept in DAP4. The
argument for it is that sets of maps are often going to be used in
identical form in a number of variables. Further, it allows one to add
attributes to the coordinate system element to provide extra information
(John, do you care to elaborate on this?).

#### There can be any non-zero number of maps

This is the part that is **Under Review**

The essential issue seems to be how 'point data' are represented. There
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

##### Some ideas we talked about at the meeting

1.  We just go with it
2.  We limit *grid* to being a coverage where the number of maps *M* is
    \<= the rank of the grid array and We admit a new type - *point* -
    to hold point data that does not have this limitation. Both are
    *Coverages*
3.  We force point data to be encoded using Sequence and ensure that
    clients which expect CDM variable organization will see that.

## Old Discussion

The four points listed above were/are addressed on the [Data
Model](DAP4:_Data_Model "wikilink") but in doing so we created a new set
of issues with constraints (and there were already some issues left over
from DAP2). John's comment \#2 immediately below seems to address one of
these new problems. I think that restricting the way grids can be subset
along with differentiating between subsetting Grids and parts of Grids
addresses the problems. [Jimg](User:Jimg "wikilink") 14:59, 6 March 2012
(PST)

### John's comments ...

1\) The CDM uses this object model for coordinate systems:

<font size="2">

` `[`CDM CoordSys`](http://www.unidata.ucar.edu/software/netcdf-java/CDM/index.html#CoordSys)

</font>

When translating things like GRIB into CDM, we usually also add the CF
attributes, which simplifies things since now the coordsys info is
encoded at the data access layer. This is very simple, in CDL:

<font size="2">

`float Temp(z,y,x);`
` :coordinates = "lat, lon, depth";`

</font>

2\) It appears that a Variable that contains map elements is a "grid",
and that when you make a data request for a grid, you get back the
corresponding values of the maps. Correct?

One problem with that is when you have 2D coordinates, as in:

<font size="2">

`float lon(y,x);`
`float lat(y,x);`
`float Temp(z,y,x);`
` :coordinates = "lat, lon, depth";`

</font> then you get back 3X more data, which you may not want.

--[JohnCaron](User:JohnCaron "wikilink") 15:29, 2 March 2012 (PST)

From above: "...when you make a data request for a grid, you get back
the corresponding values of the maps. Correct?" Ans: Maybe. Slightly
longer answer: If a client requests that a Grid be subset, then it
receives all of the information that a Grid requires in the response. If
a client asks for a subset of one of a Grid's components, then it will
receive just that component (which will no longer be a Grid). I'm
defining a Grid to be a set with three things: The Grid (a
specialization of an Array), one or more Maps (each also a
specialization of an Array) and one or more Dimensions). In your
example, the set of *lon*, *lat* and *Temp* is a Grid. A client can ask
for the Grid to be subset and it will receive parts of all three of
those pieces, which would also be a Grid. If it asks for just part of
*Temp*, it will receive just that, which is not a Grid.
[Jimg](User:Jimg "wikilink") 13:57, 6 March 2012 (PST)

### Basic features of Grids in DAP4

We're still discussing just how constraining "grids" works. I think that
the model we choose needs to support:

- N-dimensional coordinate variables (aka maps)
- Shared dimensions
- subsetting that returns a valid grid
- subsetting that returns parts that make up the grid

I think optimizing transfers should be secondary to proper semantics.

NB: This is already present in the [DAP4: Data
Model](DAP4:_Data_Model "wikilink")

[Jimg](User:Jimg "wikilink") 17:11, 2 March 2012 (PST); Updated:
[Jimg](User:Jimg "wikilink")

### Counter Proposal

I think I misunderstood the original proposal... I thought it was about
subsetting. I've removed my comments about that topic from this page.

I think the gist of this proposal is correct: "...grids need to be
simple relationship instances between an array and its coordinate
variables." However, the proposal also includes: "Note that the
dimensions can be inferred from the specified coordinate variables."
which is not correct. In the past we have talked this over and it's come
to light that some grids do not have coordinate variables for all of
their axis (this was news to me, but an example, I believe, is that a
model run might have maps/coordinates for lat and lon and a third
dimension for run number where various parameters are varied for
different runs).

I propose that a grid be an array with explicit maps/coordinate
variables for some or all of its dimensions. I also propose that an
array must have explicit dimensions, which I know is obvious, but I wan
to draw attention to the idea that a grid has *both* dimensions and
maps/coordinates.

[Jimg](User:Jimg "wikilink") 11:30, 7 March 2012 (PST)

### Coordinate Systems non est Grid

So heres another aspect of this. In the above, you are (I think)
describing CDM Coordinate Systems. A CDM Grid is a different animal
altogether. Here is the classic example thats not a CDM grid:

` float data(sample);`
`   `[`data:coordinates="lat`](data:coordinates=%22lat)` lon time";`
` float lat(sample);`
` float lon(sample);`
` float time(sample);`

So my first thought is maybe to use a different name than Grid. Its
really an "Array with CoordSys". But CDM CoordSys is more general than
an Array, it happens with, say a sequence:

` float lat;`
` float lon`
` Sequence {`
`   data1;`
`   data2;`
`   time;`
` } buoyData;`
`  buoyData:coordinates = "lat lon time";`

this data has a coordinate system with a constant lat lon, and a time
coordinate in each sequence.

So, are you intending to capture full CDM CoordSys semantics, or maybe
just partial?

[JohnCaron](User:JohnCaron "wikilink") 11:55, 7 March 2012 (PST)

I am not trying to capture the CDM coordinate system semantics. But I am
trying to capture the semantics of DAP2 Grids, and then extend that to
include three ideas:

1.  more than one dependent variable for a given set of independent
    variables (also can be read: more than one array for a single set of
    maps)
2.  maps that are no longer limited to vectors
3.  dependent variables (arrays) where not every dimension has an
    associated independent variable (map) (because that seems to be a
    reality in many cases - the array structure is used to store
    metadata - unless I'm mistaken on that point).

We have many requests for these features.

Is it that case that the coordinate systems in CDM have domain-specific
semantics bound to them (e.g., Latitude, Mercator projection, etc.)? If
so, that would be a clear difference between this idea - Grid as a
mapping - and CDM coordinate systems.

[Jimg](User:Jimg "wikilink") 12:39, 7 March 2012 (PST)

### Coordinate Systems est divida in tres partes

CDM coordinate systems are totally general, and very simple, and include
your 3 desired features. They describe both grids and points, as well as
swaths, radial data, and a whole lot more. So its a shame not to use
them.

1\) Essentially one associates with each dependent variable (aka data
variable) a list of independent variables (aka coordinate variables).
The only restriction is that the set of dimensions (always shared) used
by any coordinate variable is a subset of the dimensions used by the
data variable. Then, for each data point, you get a set of coordinate
values, one from each coordinate variable; eg at data(i,j,k) you get
{lat(i), lon(j), alt(k)} \[thats the grid case\]. the point case looks
like: eg at data(i) you get {lat(i), lon(i), alt(i)}.

Thats it, thats the general case: an association of independent and
dependent variables with a restriction on the shared dimensions used.

There are two more parts, each a bit more specific to earth referenced
data:

2\) one can classify the coordinate variables, which is domain specific,
and needed for a lot of practical things. The classification in CDM is
Lat, Lon, Time, etc.

3\) one can associate transforms that convert between coordinate systems
(aka CRS). These are in practice a controlled vocabulary which we (CF/
CDM) has defined for horizontal CRS and vertical CRS. This needs 2) to
work.

This part could be skipped, but you need it if you want to map into OGC
protocols.

None of this is needed by DAP \<--\> CDM, because we already do it with
attributes. So one could just leave it out and/or implement it above the
"transport data model". IMO adding it explicitly to the protocol would
really add a lot of semantics at very little complexity. But you have to
understand what these things mean or it wont be used correctly.

--[JohnCaron](User:JohnCaron "wikilink") 18:11, 27 March 2012 (PDT)

John, I'm inclined to agree with you, that we should support this in
DAP4.

My only concern, and it's a point to keep in mind, is that most pont
data will be in relational databases so it'll look differently to
clients. In DAP2 we made the statement that the transport provided
mechanism, not policy. That worked out OK, but we are working on its
follow on...

My suggestion is that we accept that a *Grid* in DAP4 have the property
that the number of maps is not limited to/by the dimensionality of the
array. That we mark this as such here and say this is 'Accepted'.

[Jimg](User:Jimg "wikilink") 16:05, 30 March 2012 (PDT)

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")