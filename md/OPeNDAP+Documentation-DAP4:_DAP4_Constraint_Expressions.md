[\<\< Back to OPULS Development](OPULS_Development "wikilink")

# Background

At the OPULS meeting in Boulder CO on 1-3 Oct 2012 we discussed how the
concepts of the DAP2 constraint expressions could be extended to DAP4.
We decided that the existing capabilities available in DAP2 should be
included, but with some changes due to the differences in DAP4's data
model and to fix problems with the DAP2 syntax and semantics. DAP4
constraint expressions (CEs) will support *projection* in much the same
way that DAP2's CEs did. Variables are chosen from a dataset by listing
them in the CE in a comma separate list. We decided that the language
and syntax of the *selection* was flawed and will be replaced by
*filters* that are applied to single clauses in the CE, not to the whole
CE as was the case with DAP2. By dropping the database language of
*relations* we are making a distinction that the CE semantics does not
intended to support the relational calculus, but does support choosing
specific values from different types of variables. Unlike DAP2, these
*filters* can be applied to arrays as well as 'sequences' (which we have
technically dropped from the data model in favor of structures with
varying dimensions). Lastly, we talked as some length about the relative
merits of incorporating a functional programming language into the CE
syntax and decided to do so.

# Problem Addressed

Remote data access depends on having a simple and powerful query
interface. Users must be able to choose parts of a large and often very
complex dataset. Unlike most 'Big Data,' the datasets that DAP (and,
hence, OPULS) targets are highly structured, and the CE semantics must
reflect this. In practice this means that we must be able to choose
parts of a dataset by marking some or all of the variables it contains
as the ones we would like to access. In addition, it is useful to be
able to 'slice' array variables so that a subset, in index space, is
returned. In addition, it is often necessary to subset variables by
value, accessing all of the elements of an array or structure that are
within a certain range.

# Proposed Solution

The proposed solution is based on the existing DAP2 constraint
expression syntax, with two main modifications that result from
differences in the DAP4 data model as well as fixes for ambiguities in
the DAP2 CE syntax and semantics. The ambiguities arose from applying a
*selection* to the entire CE, which didn't always make sense (so the
user or evaluator had to make some arbitrary choices about what a
particular selection subexpression meant) and in calling functions on
the dataset or in the selection-part of the CE. the latter was less of
an ambiguity than an annoyance for users because important metadata
about the potential return from a function was not available in the DAP2
CE.

## Simple Constraints

The simplest constraint is the null string an it means 'return
everything' from the dataset. Choosing variables in a daaset is referred
to as the *projection* of the CE. To choose a subset of the variables in
a dataset, enumerate them in a comma-separated list. To choose parts of
a Structure, name those parts explicitly using the syntax *structure
name*.*field name*. Each DAP4 dataset contains one or more Groups; the
top-level Group is always present and is named */* (pronounced 'root').
If the root Group is the only Group in the dataset, it does not need to
be named when listing variables in the CE. However, if there are other
Groups in the dataset, each Group other than the root Group must be
named. In any case, naming the root Group is optional.

Names are case sensitive.

#### Example: Projections

**Note**: The syntax used for the examples is (hopefully) easier to read
than the DAP4 DMR which uses XML; Curly braces indicate hierarchy.

``` c
Dataset {
    Int32 u;
    Int32 v;
    Structure {
        Int32 x;
        Int32 y;
    } Point;
} projections;
```

Access just *u*: *u*
Access just *u* and *v*: *u,v*
Access just *x* within *Point*: *Point.x*. Note that variable names are case-sensitive.
Access *u* and *v* by explicitly naming their Group: */u,/v*. Every dataset in DAP4 has a root Group, written */*. When that is the only Group in a dataset, it is implicit in the CE, but you can still use its name explicitly.

``` c
Dataset {
    Int32 u;
    Int32 v;
    Group {
        Int32 u;
        Int32 v;
    Structure {
        Int32 x;
        Int32 y;
    } Point;
   } inst2;
} Explicit_Group;
```

Access 'top-level' *u* and *v*: */u,/v* or *u,v*
Access 'top-level' *u* and *v* and *inst2*'s *u* and *v*: */u,/v,/inst2/u,/inst2/v*, or *u,v,/inst2/u,/inst2/v*
Access *inst2*'s *u* and *v*: */inst2/u,/inst2/v*
Access *Point* 's *x*, which is inside the *inst2* Group: */inst2/Point.x*

## Array Subsetting in Index Space

Subsetting fixed-size arrays in their *index space* is accomplished
using square brackets. For an array with *N* dimensions, *N* sets of
brackets are used, even if the array is only subset on some of the
dimensions. The names of array variables are fully qualified names
(FQNs) so it's possible to name arrays in structures and/or Groups.
Within the square brackets, several subexpressions are allowed:

\[ *n* \] : return only the value(s) at a single index, where 0 \<= n \< N for a dimension of size *N*.
\[ \] : return all of elements elements for a particular dimension
\[ *start* : *step* : *stop* \] : return every *step* value between *start* and *stop*. This is the complete version of the syntax.
\[ *start* : *stop* \] : return the values between *start* and *stop* (*start* and *stop* define a closed interval).
\[ : *stop* \] : return the values from zero to *stop*.
\[ *start* : \] : return the values from *start* to the end of the dimension.
\[ : *step*: *stop* \] : return every *step*' value from zero to *stop*.
\[ *start* : *step* : \] : return every *step*' value from *start* to the end of the dimension.
\[ : *step* : \] : return every *step* value for the dimension.

The subsetting operator can be applied to one or more arrays in the CE.

#### Example: Subsetting in Index Space

``` c
Dataset {
    Int32 u[256][256];
    Int32 v[256][256];
    Structure {
        Int32 x;
        Int32 y;
    } Point[256];
} arrays;
```

Access all of *u*: *u*
Access all of *Point* 's *x* field: *Point.x*. This returns an array of Structures with a single (Int32) element, not and array of Int32.
Access elements 10 to 20 of array *Point*: *Point\[9:19\]*. DAP4, like DAP2, uses zero-based indexes. This CE will return the 10th to the 20th elements (Structures in this case) of the array
Access every 4th element in the *Point* array: *Point\[0:4:255\]*, or *Point\[:4:\]*. This is a simple decimation operation; this CE would return 64 Structures corresponding to elements at indexes 0, 3, 7, ..., 255 of the array.
Access parts of *u* and *v*: *u\[4:2:9\],v\[4:2:9\]*

Other possible CEs:

*u\[:4:\]\[:4:\]*: every fourth element in both dimentsions; this would return 1/16^th of the array's data.
*u\[\]\[9:19\]*: elements corresponding to every row and columns 10 to 20
*u\[7\]\[9:19\]*: elements corresponding to the 8^th row and columns 10 to 20
*u\[9:19\]\[9:19\]*: elements corresponding to rows 10 to 20 and colums 10 to 20.
*u\[:19\]\[:19\]*: elements corresponding to rows 0 to 20 and columns 0 to 20.
*u\[\]\[\]*: identical to *u*.

### Subsetting and Shared Dimensions

Shared dimensions provide a way to indicate that two or more variables
are share similar extents. To support applying the same subsetting
operation on several variables, we will introduce the idea of an
*iterator* in the projection subclause of the CE. Iterators are a way of
describing the range over which operator are applied. By defining the
iterators once and using them with several variables, it is possible to
subset each variable in a related way. This feature also introduces a
new syntax element to the CE: projection subexpressions can contain
several parts grouped together using curly braces.

**Notes**:

1.  Do the iterators need to use the same names as the dimensions? That
    can create name conflicts when the iterators are to be returned as
    part of the result set.
2.  Is this subsetting two or more arrays in one projection
    subexpression limited to things with shared dimensions?
3.  Following from the above, if it is necessarily limited to shared
    dimensions, what about cases where the variables don't share all of
    their dimensions? Just those that are shared?

NB: I think that the limitation regarding *Shared Dimensions* might be
because we confused shared dimensions with *Maps*.

#### Example: Subsetting with Shared Dimenstions

``` c
Dataset {
    Dimensions: lat=10, lon=5; // the sizes of the dimensions

    Float32 temp[lat][lon];      // they are shared by these variables
    Float32 lat[lat];
    Float32 lon[lon];
} shared_dimensions;
```

{ temp\[ lat=0:5 \]\[ lon=0:3 \], lat\[lat\] } : The iterators *lat* and *lon* correspond to the shared dimensions of the same name in the dataset. They well be applied to each of the variables listed resulting in *temp\[0:5\]\[0:3\]* and *lat\[0:5\]* as return values. Note that this syntax is one of two that can be used to define iterators. When the iterators are defined inside a variable, they are not returned as part of the result. However, as with the following example, when the iterators are assigned values on their own, they *are* returned as part of the result set. Also note that a subset/projection request that includes variables that use *shared dimensions* must include those shared dimensions in the result. The above CE returns:

``` c
Dataset {
    Dimensions: lat=6, lon=4;   // the new sizes of the dimensions

    Float32 temp[lat][lon];         // they are shared by these variables
    Float32 lat[lat];
} shared_dimensions;
```

{lat=\[0:5\], lon=\[0:3\], temp\[lat\]\[lon\], lat\[lat\] } : This CE returns the same parts of *temp* and *lat* as the first example. However, it also returns the values of the iterators *lat* and *lon*. Note that this example illustrates a problem with restricting iterator names to be those of the shared dimension(s) to which they correspond.

``` c
Dataset {
    Dimensions: lat=6, lon=5;   // the new sizes of the dimensions

    Int32 lat[6];                   // the iterators hmmm...
    Int32 lon[4];

    Float32 temp[lat][lon];         // they are shared by these variables
    Float32 lat[lat];
} shared_dimensions;
```

## Filters

While *projections* and *subsetting* provide ways to choose data based
on the structure of a dataset, *filters* provide a way to choose data
based on their values. The values to be returned are denoted using
simple predicates. When an array is *subset by value* using a filter,
*iterators* are used to describe which parts of the array are to be
returned. The general syntax for a filter expression is to list one or
more iterators with a start and stop range followed by one variable
followed by a filtering expression. Several filter subexpressions can
appear in a single CE but only one variable (and *N* iteratrors given
that the variable has rank *N*) can be included in a single filter
subexpression (but see the next section for variables with shared
dimensions). Note that when arrays are subset by value, either an array
of varying dimension is returned or an array of structure with varying
dimension is returned with the iterator and array as scalar members. In
the latter case, the i<sup>th</sup> value of the structure contains the
i<sup>th</sup> iterator and the matching value of the array.

**Note** Should we require that the iterators must be used when filters
are used to subset and they must be explicitly declared, with the side
effect that they will be returned along with the array values that
satisfy the filter predicate(s)?

#### Example: Filters

``` c
Dataset {
    Float64 temp[10];
    Float32 u[256][256];
    Float32 v[256][256];
    Structure {
        Int32 x;
        Int32 y;
    } Point[256];
} arrays;
```

{ j=\[0:5\], temp\[j\] \| temp\[j\]\>7 } : returns a single-dimension (with varying size) Structure that contains both *j* and the corresponding values of *temp*. The iterator *j* is used to limit the operation to the first 6 elements of *temp*. Note that *j* is and not part of the dataset even though it is returned as part of the result. The name of the Structure is the sam as the name of the array (*temp* in this example).

``` c
Structure {
    Int32 j;
    Float64 temp;
} temp[*];
```

{ temp \[j=0: 5\] \|temp\[j\]\>7 } : This chooses the same elements of *temp* (all those with a value greater than 7) but returns only those values and not the associated iterator values (because the iterator was not explicitly listed in the projection). The return is an array of Float64 with a single dimension that is variying in size.

``` c
Float64 temp[*]
```

{ i=\[0:5\] ,j=\[0\], u\[i\]\[j\] \| u\[i\]\[j\] \> 7} : This syntax extends to *N* dimensional arrays. This returns a Structure named *u* with a single varying dimension.

``` c
Structure {
    Int32 i;
    Int32 j;
    Float32 u;
} u[*];
```

{ i=\[\], temp\[i\] \| temp\[i\]\>7 }, { temp\[ i=\[\] \] \| temp\[i\]\>7 } : return all values of *temp* greater than 7. In the first case, return the corresponding iterator values along with the values of *temp* and in the second omit the iterator values.

#### Example of subsetting (with a filter) a 'projected' lat/lon array

This is a common case: An array holds the range values of some sampled
function and two one-dimensional arrays hold the latitude and longitude
values. The data are (often) in some standard map projection like
Mercator.

**Note**:

1.  In the notes, the name of the returned structure is usually the name
    of the first non-iterator variable in the
    projection/subsetting/filtering clause. What about allowing these
    'made up' Structures to be anonymous?
2.  In this first example, there's quite a bit of redundancy - suppose
    lat and lon are each 1024. In that case the total size of the arrays
    *lat*, *lon*, and *temp* is 1024<sup>2</sup>) + 2(1024) but the size
    of the return value with a filter is, worst case
    3(1024<sup>2</sup>) - essentially it triples the response size in
    the worst case. A better response would be to return three
    structures. see below

``` c
Dataset {
    Dimensions: lat = 10, lon = 5;

    Float64 lat[lat];
    Float64 lon[lon];

    Float64 temp[lat][lon];
} temp_data;
```

{ i=\[\],j=\[\], temp\[i\]\[j\], lat\[i\], lon\[j\] \| lon\[j\]\>90 & lon\[j\]\<128 & lat\[i\]\>40 & lat\[i\]\<60 }: Returns:

``` c
struct {
   int i;
   int j;
   Float64 lat;
   Float64 lon;
   Float64 temp;
} [*];
```

As mentioned in the notes, this can increase the amount of data
returned. A more efficient return form would be:

``` c
struct {
   int i;
   Float64 lat;
} lat[*];
```

``` c
struct {
   int j;
    Float64 lon;
} lon[*];
```

``` c
struct {
   int i;
   int j;
   Float64 temp;
} temp[*];
```

### Filters applied to 'Swath' data

Satellite Swath data illustrate an important special case for filters
because it is a generalization of the syntax to a broader collection of
sampled functions (what the OGC calls 'discrete coverages').

``` c
Dataset {
    Dimensions:
    x = 1024;
    y = 1024;

    Float64 lat[x][y];
    Float64 lon[x][y];

    Float32 u[x = lon][y = lat];
    Float32 v[x = lon[y = lat];
} arrays;
```

In the above example, the syntax *Float32 u\[x = lon\]\[y = lat\];*
indicates that *u* is a two-dimensional variable in *x* and *y* but that
it holds the range values for *u* for a function with the domain given
by the maps *lat* and *lon*. Values in *lat*, *lon*, and *u* are
associated with each other by using the same values for the shared
dimensions *x* and *y*.

{ i = \[\], j = \[\], lat\[i\]\[j\], lon\[i\]\[j\], u\[i\]\[j\], v\[i\]\[j\] \| lat\[i\]\[j\] \< 45 & lat\[i\]\[j\] \> 5 & lon\[i\]\[k\] \> -80 & lon\[i\]\[k\] \< -30 } :

``` c
struct {
   int i;
   int j;
   Float64 lat;
   Float64 lon;
   Float64 u;
   Float64 v;
} [*];
```

## Applying Filters to Arrays with Varying Dimensions

## Filters and Structures with Varying Dimensions

## Functions

## Represenation of Return Values

## Grammar

    <!--
    Dennis' original notes
    ---------------------------------
    float temp[10]
    {j=[0:5],temp[j]|temp[j]>7}

    struct {
     int j;
     float temp;
    } temp[*];

    --------------------
    float temp[10]
    {temp[j=0:5]|temp[j]>7}

    float temp[*]
    <note that j is not returned because it is not at top
    level in the expression>
    ---------------------------------
    float temp[10][5]
    {i=[0:5],j=[0],temp[i][j]|temp[i][j]>7}

    struct {
     int i
     int j;
     float temp;
    } temp[*];

    ---------------------------------
    float temp[*]
    {i=*,temp[i]|temp[i]>7}

    float temp[*]

    as opposed to returning point structures?

    --------------------
    dims: lat=10, lon=5
    float temp[lat][lon]
    float lat[lat]
    float lon[lon]

    CE=lat=[0:5],lon=[0:3]{temp[lat][lon]}{lat[lat]}{lon[lon]}

    returns:
    dim: lat=6 lon=4
    float temp[lat][lon]
    float lat[lat]
    float lon[lon]
    -->

    --------------------
    dims: lat=10, lon=5
    float temp[lat][lon]
    float lat[lat]
    float lon[lon]

    CE: i=[0..n],j=[0..m],
       {temp[i][lon],lat[i],lon[j]
        | lon[j]>90&lon[j]<128
         & lat[i]>40&lat[i]<60}

    returns:

    struct {
       int i
       int j
       float lat
       float lon
       temp
    } <name>[*]

    versus

    struct {
       int i
       float lat
    } lat[*]

    struct {
       int j
       float lon
    } lon[*]

    struct {
       int i
       int j
       float temp
    } temp[*]

    --------------------------------------------------
    Shared dim "grid" case:

    dims: lat=10, lon=5
    float temp[lat][lon] maps: lat, lon
    float lat[lat]
    float lon[lon]

    CE: lat=[*],lon=[*],
       {temp[lat][lon],lat[lat],lon[lon]
        | lon[lon]>90&lon[lon]<128
         & lat[lat]>40&lat[lat]<60}

    result:?
    dim: lat=m lon=n
    float temp[lat][lon] maps:lat,lon
    float lat[lat]
    float lon[lon]

    --------------------
    Shared dim "swathe" case:
    dims: x=10, y=5
    float temp[x][y] maps: lat, lon
    float lat[x][y]
    float lon[x][y]

    CE: x=[*],y=[*],
       {temp[x][y],lat[x][y],lon[x][y]
        | lon[x][y]>90&lon[x][y]<128
         & lat[x][y]>40&lat[x][y]<60}

    returns:

    Note that [x,y] is ragged, not rectangular.

    struct {
       int x
       int y
       lat
       lon
       temp
    } <name>[*]

    versus
    struct {int x; int y; float lat;} lat[*]
    struct {int x; int y; float lon;} lon[*]
    struct {int x; int y; float temp;} temp[*]

    --------------------

    Using function:
     f(temp,lat,lon,90,128,40,60)

    return:
     dims: lat=n, lon=m //m,n computed by looking at lat[] and lon[]
     float temp[lat][lon]
     float lat[lat]
     float lon[lon]

    or
    struct {
     float lat;
     float lon;
     float temp;
    } X[*];


    --------------------
    Consider the following

    i=[0..n],temp[i]|temp[i]==temp[i+1]


    i=[0..n],j=[0..m],temp[i][j]|temp[i][j]==temp[j][i]

    ==================================================

    {i=[0:5],j=*,S1[i]|S2[j].depth=100.0}

    Structure {
     int i;
     Structure {
       float lat;
       float lon;
       Structure {
         float depth;
         float temp;
       } S2[*];
      } S1;
    } S1[*];

    ---------------------------------



    =>float temp[*]




    float lat[lat]

    lat=[0:99]
    ...
    lat|(lat[lat] > 7 & lat[lat] < 10)


    i=[0:99],lat|(lat[i] > 7 & lat[i] < 10)

    i[*1],lat[*2] *1=*2

    structure {
      int i[*]
      float lat[*]
    } lat;

    structure {
      int i
      float lat
    } lat[*];


    x=10; y=20
    float lat[x][y]

    lat[i=0:9][j=1:3]|lat[i][j] > 7 & lat[i][j] < 10)

    structure {
      int i
      int j
      float lat
    } lat[*];

    float temp[10][20]
    float lat[19]
    float lon[18]

    DIMS=i=[0:5],j=[1:2]
    CE=temp[i][j],lat[i],lon[j]

    temp[0:5][2],lat[0:5],lon[2]

    lat1=0..5
    lon1=2
    temp[lon1][lat1],lat[lat1],lon[lon1]

    dim: lat1, lon1
    temp[lat1][lon1]
    lat[lat1]
    lon[lon1]


    iterator variable
    defined new shared dimensions => iterator

    CE=<shared dim iterators>{<local iterator>expr>}{...}

    similar to ferret, except they use let x=...


    relational calculus:
    {seq1.c1,seq1.c2,seq2.c3,seq3.c4 where seq1.c1 = seq2.c3}

    semi-join:
    {seq1.c1,seq1.c2, where seq1.c1 = seq2.c3}

    seq1,seq2|seq1.key1=seq2.key2

    Structure {
     float lat;
     float lon;
     float depth;
     float temp;
    } S1[*];

    relational selection:
    CE: S1|S1.depth >100
    returns: struct {lat;lon;depth;temp} S1[*}

    relational projection:

    CE: S1.(lat,depth)
    returns: Structure { lat; depth} S1[*]

    S1.(lat)
    struct {lat} S1[*]

    S1.lat
    lat[*]

    --------------------
    Structure {
     float lat;
     float lon;
     Structure {
       float depth;
       float temp;
     } S2[*];
    } S1[*];

    If S1[*]
    CE: j=*,S1[j] | S1.lat>5
    CE: S1[j=*] | S1.lat>5

    if S1[5]
    CE: j=*,S1[j] | S1.lat>5

    return:
    Note user is responsible for name conflict with the iterator name.
    Structure {
     int j;
     float lat;
     float lon;
     Structure {
       float depth;
       float temp;
     } S2[*];
    } S1[*];

    --------------------
    Structure {
     float lat;
     float lon;
     Structure {
       float depth;
       float temp;
     } S2[*];
    } S1[*];

    CE: i=*,j=*,S1[i] | S1[i].S2[j].temp > 10
    CE: S1[i=*] <where does j go?>| S1[i].S2[j].temp > 10
       should be: S1 | S1.S2.temp > 10

    return:
    Structure {
     int i
     float lat;
     float lon;
     Structure {
       int j
       float depth;
       float temp;
     } S2[*];
    } S1[m];

    --------------------
    Structure {
     float lat;
     float lon;
     Structure {
       float depth;
       float temp;
     } S2[5];
    } S1[10];

    CE: S1 | S1.S2.temp > 10

    return:
    Structure {
     float lat;
     float lon;
     Structure {
       float depth;
       float temp;
     } S2[*];
    } S1[m];
    --------------------
    Structure {
     int x;
     Structure {
       int y;
     } S2[*];
     Structure {
       int z;
     } S3[*]
    } S1[m];

    ?what about:
    CE: S1 | S1.S2.y = S1.S3.z

# Discussion

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")