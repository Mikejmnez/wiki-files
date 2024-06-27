[\<\< Back to OPULS Development](OPULS_Development "wikilink")

# Background

At the OPULS meeting in Boulder CO on 1-3 Oct 2012 we discussed how the
concepts of the DAP2 constraint expressions could be extended to DAP4.
We decided that the existing capabilities available in DAP2 should be
included, but with some changes due to the differences in DAP4's data
model and to fix problems with the DAP2 syntax and semantics. DAP4
constraint expressions (CEs) will support *projection* in much the same
way that DAP2's CEs did (but we're going to call it *subsetting*).
Variables are chosen from a dataset by listing them in the CE in a
semicolon separated list. We decided that the language and syntax of the
DAP2 *selection* was flawed and will be replaced by *filters* that are
applied to single clauses in the CE, not to the whole CE as was the case
with DAP2. By dropping the database language of *relations* we are
making a distinction that the CE semantics does not intended to support
the relational calculus, but does support choosing specific values from
different types of variables. Unlike DAP2, these *filters* can be
applied to arrays as well as Sequences. Lastly, we talked at some length
about the relative merits of incorporating something akin to a
functional programming language into the CE syntax and decided to do so.

# Problem Addressed

Remote data access depends on having a simple and powerful query
interface. Users must be able to choose parts of a large and often very
complex dataset. Unlike most 'Big Data,' the datasets that DAP (and,
hence, OPULS) target are highly structured, and the CE semantics must
reflect this. In practice this means that we must be able to choose
parts of a dataset by marking some or all of the variables it contains
as the ones we would like to access. In addition, it is useful to be
able to 'slice' array variables so that a subset, in index space, is
returned. In addition, it is often necessary to subset variables by
value, finding all of the elements of an array or sequence that are
within a certain range.

# Proposed Solution

The DAP4 Constraint Expression (CE) syntax is an extension of the syntax
used by DAP2 that adds some important new features for Arrays and
Coverages (Grids) as well as addressing some ambiguities in the DAP2
syntax. In this design we also introduce some new terminology to make
the explanation of the CE syntax clearer. Additionally, we use a 'curly
brace' notation for datasets to streamline the description of datasets
because the XML documents that DAP4 servers produce is verbose and hard
for humans to read.

A CE lists which variables and what parts of those variables a server
should return to a client. When a client makes a request to a DAP4
server, it always sends a CE where an empty CE is interpreted to mean
that the client wants the entire dataset sent. A CE is made up of a list
of clauses, each of which names a variable in the dataset that the
client would like the server to send to it. Each clause can further be
broken down into two parts: The subset expression and the filter
expression. There are limitations on the CE clauses depending on
variable type. For scalar variables, getting the variable is the only
option available, so the subset expression is limited and no filter
expression is supported. Structure variables can be subset by field but
do not support filter expressions. Sequences can be subset by field and
do support filters. Arrays support index subsets and filters. Lastly,
*coverages* support all of the things Array support but include some
special filter operators that build subsets based on values.

New ideas/features added for DAP4 include:

- Using a grouping operator for Structures and Sequences
- Filters on Arrays
- Masking for arrays (setting elements in one array to No Data based on
  the elements set to No Data in another array)
- Computed subsetting for Coverages
- Sequence filtering expressions explicitly bound to a specific Sequence
  variable

DAP4 type system notes:

- Arrays always have fixed dimensions
- Structures and Sequences can contain any type.
- DAP2's Grid is gone; we use explicit Maps and Dimensions to provide a
  way to denote Coverages, which are more general than DAP2 Grids.
- We provide the data model equivalent of arrays with varying dimensions
  by combining instances of Arrays and Sequences. This results in new
  characteristics for DAP4:
  - Sequences can contain Arrays
  - DAP4 supports Arrays of Sequences
  - DAP4 also supports Structures as fields of Sequences

## Terminology used by this document

selection expression: The entire expression passed to the server that is used to choose specific parts of a dataset.
subset: The act of choosing parts of a dataset based on the *type* of one or more of its variables. We define several types of subsetting operations as follows:
index subsetting: Choosing parts of an array based on the indexes of that array's dimensions. This operation always returns an array of the same rank as the original, although the size of the return array will (likely) be smaller. Index subsetting uses the bracket syntax described later.
field subsetting: Choosing specific variables or fields from the dataset. A dataset in DAP4 is made up of a number of variables and those may be Structures or Sequences that contain fields (and, in effect, the Dataset is itself a Structure and all of its variables are fields - the distinction is more convenience than formal). Field subsetting using the brace syntax described later. One or more fields can be specified using a semicolon (**;**) as the separator.
filter: A filter is a predicate that can be used to choose data elements based on their values. the vertical bar (**\|**) is used as a prefix operator for the filter predicate. Filters can be applied to elements of an Array or fields of a Sequence. A filter predicate consists of one or more filter subexpressions. One or more subexpressions can be specified, using a comma (**,**) as the separator.
filter subexpression: A simple expression that consists of a single variable/field, a relative operator (=, !=, \<, \<=, \>, \>=) for numbers or a comparison operator (=, ~=) for a string (*~=* is a regular expression compare) and new operators for Arrays and Coverages (\<\<, \>\>, @=).
id: The name of a variable. These can be relative or absolute. Absolute names use the FQN proposal (See ).
domain, range: A function is a mapping from a set of *domain* values to a set of *range* values; in a *discrete function*, these sets are finite.
discrete coverage: A *discrete coverage* is a (discrete) function where the indices of the arrays that hold the *domain* and *range* values have a one-to-one and onto mapping, with the important exception that in cases where the dimension of the arrays containing the domain values can be reduced without loosing information, that is done. This is purely an implementation optimization, but where applicable, it is nearly universally used. In DAP4 we call discrete coverages simply *coverages* or *grids*.
coverage: Synonymous with *grid* in this document.

## Subsetting Constraints

The simplest constraint is the null string and it means 'return
everything' from the dataset. Choosing variables in a dataset is
referred to as the *subset*. To choose a subset of the variables in a
dataset, enumerate them in a semicolon-separated list. To choose parts
of a Structure, name those parts explicitly using the syntax *structure
name{field name}* or *structure name.field name*. Each DAP4 dataset
contains one or more Groups; the top-level Group is always present and
is named */* (pronounced 'root'). If the root Group is the only Group in
the dataset, it does not need to be named when listing variables in the
CE. However, if there are other Groups in the dataset, each Group other
than the root Group must be named. In any case, naming the root Group is
optional.

Names are case sensitive.

#### Example: subsetting by variable or field

``` xml
<Dataset name="vol_1_ce_1"
  dapVersion="4.0"
  dmrVersion="1.0"
  xml:base="file:dap4/test_ce_1.xml"
  ns="http://xml.opendap.org/ns/DAP/4.0#"
  xmlns:dap="http://xml.opendap.org/ns/DAP/4.0#">

  <Int32 name="u"/>
  <Int32 name="v"/>
  <Structure name="Point">
    <Int32 name="x"/>
    <Int32 name="y"/>
  </Structure>

</Dataset>
```

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
} vol_1_ce_1;
```

Access just *u*: *u*
Access just *u* and *v*: *u;v*
Access just *x* within *Point*: *Point{x}* This notation is based on the use of brackets in [DAP4: Proposal for Structure Projection](DAP4:_Proposal_for_Structure_Projection "wikilink") and [DAP4: DAP4 Filter Constraints](DAP4:_DAP4_Filter_Constraints "wikilink") with the exception that braces (***{}**'') are likely easier to parse than brackets (***\[\]**'') given that arrays of both Structure and Sequence are possible and thus with arrays of these structures the grammar that defines the constraint expression syntax would become context sensitive.
Equivalent expression to access just *x* within *Point*: *Point.x*
Access *u* and *v* by explicitly naming their Group: */u;/v*. Every dataset in DAP4 has a root Group, written */*. When that is the only Group in a dataset, it is implicit in the CE, but you can still use its name explicitly.

``` xml
<Dataset name="vol_1_ce_2">
  <Int32 name="u"/>
  <Int32 name="v"/>
  <Group name="inst2">
    <Int32 name="u"/>
    <Int32 name="v"/>
    <Structure name="Point">
      <Int32 name="x"/>
      <Int32 name="y"/>
    </Structure>
  </Group>
</Dataset>
```

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
} vol_1_ce_2;
```

Access 'top-level' *u* and *v*: */u;/v* or *u,v*
Access 'top-level' *u* and *v* and *inst2*'s *u* and *v*: */u;/v;/inst2/u;/inst2/v*, or *u;v;/inst2/u;/inst2/v*
Access *inst2*'s *u* and *v*: */inst2/u;/inst2/v*
Access *Point* 's *x*, which is inside the *inst2* Group: */inst2/Point{x}* or */int2/Point.x*.

**Notes**

- Variable names are case-sensitive.
- Using a semicolon is a change from DAP2 where clauses in the *project
  part* of the constraint were separated using a comma (*,*). We used
  semicolon because the comma is used elsewhere and using comma here
  made for a convoluted grammar. We wanted the grammar to be LALR(1) so
  that both table-driven and recursive-descent parsers would be easy to
  write.because it's easy to make both table and recursive descent
  parsers for these.
- Every name in a constraint should be a fully qualified name. As a
  notational simplification, we assume that non-qualified names are
  actually at the top dataset level (i.e., in the root group). For
  example, 'u' is really '/u'.

## Array Subsetting in Index Space

Subsetting fixed-size arrays in their *index space* is accomplished
using square brackets. For an array with *N* dimensions, *N* sets of
brackets are used, even if the array is only subset on some of the
dimensions. The names of array variables are fully qualified names
(FQNs) so it's possible to name arrays in structures and/or Groups.
Array index values are *zero-based* as with a number of programming
languages such as C and Java. Every array has a known starting index
value of zero. Within the square brackets, several subexpressions are
allowed:

\[ *n* \] : return only the value(s) at a single index, where 0 \<= n \< N for a dimension of size *N*. This slicing operator does not reduce the dimensionality of an array, but does return a dimension size of one for the dimension to which this is applied.
\[ \] : return all of elements elements for a particular dimension *or* apply a shared dimension slice (more on this later).
\[ *start* : *step* : *stop* \] : return every *step* value between *start* and *stop*. This is the complete version of the syntax.
\[ *start* : *stop* \] : return the values between*start* and *stop* (*start* and *stop* define a closed interval).
\[ *start* : \] : return the values from *start* to the end of the dimension.
\[ *start* : *step* : \] : return every *step* value from *start* to the end of the dimension.

The subsetting operator can be applied to any array.

#### Example: Subsetting in Index Space

``` xml
<Dataset name="vol_1_ce_3">

  <Int32 name="u">
    <Dim size="256"/>
    <Dim size="256"/>
  </Int32>
  <Int32 name="v">
    <Dim size="256"/>
    <Dim size="256"/>
  </Int32>
  <Structure name="Point">
    <Int32 name="x"/>
    <Int32 name="y"/>
    <Dim size="256"/>
  </Structure>
</Dataset>
```

``` c
Dataset {
    Int32 u[256][256];
    Int32 v[256][256];
    Structure {
        Int32 x;
        Int32 y;
    } Point[256];
} vol_1_ce_3;
```

Access all of *u*: *u*
Access all of *Point* 's *x* field: *Point{x}* or *Point.x*. This returns an array of Structures with a single (Int32) element, not an array of Int32.
Access elements 10 to 20 of array *Point*: *Point\[9:19\]*. DAP4, like DAP2, uses zero-based indexes. This CE will return the 10th to the 20th elements (Structures in this case) of the array
Access every 4th element in the *Point* array: *Point\[0:4:255\]*, or *Point\[0:4:\]*. This is a simple decimation operation; this CE would return 64 Structures corresponding to elements at indexes 0, 3, 7, ..., 255 of the array.
The index-space and field subsetting may be combined in the logical way: *Point\[0:4:\]{x}* will return an array of structures (with 64 elements) named *Point* that contains a single *Int32* field named *x*. Note that ~~*Point\[0:4:\].x*~~ is not accepted
Access parts of *u* and *v*: *u\[4:2:9\];v\[4:2:9\]*

Other possible CEs:

*u\[0:4:\]\[0:4:\]*: every fourth element in both dimensions; this would return 1/16<sup>th</sup> of the array's data.
*u\[\]\[9:19\]*: elements corresponding to every row and columns 10 to 20
*u\[7\]\[9:19\]*: elements corresponding to the 8<sup>th</sup> row and columns 10 to 20
*u\[9:19\]\[9:19\]*: elements corresponding to rows 10 to 20 and columns 10 to 20.
*u\[0:19\]\[0:19\]*: elements corresponding to rows 0 to 20 and columns 0 to 20.
*u\[\]\[\]*: identical to *u*, as are *u\[0:\]\[0:\]* and *u\[0:1:\]\[0:1:\]*.

#### More complex subsetting examples

The data model for DAP4 is very similar to that of a modern structured
programming language where *constructor types* like *Structure* may
contain any allowed type (including other Structures, etc.) as well as
being arrays themselves. The basic syntax for subsetting outlined so far
can be applied to the fields of a Structure using braces to enclose the
subsetting expression that apply to the fields of the Structure. This
can be applied recursively.

``` xml
<Dataset name="vol_1_ce_4">
  <Int32 name="u">
    <Dim size="256"/>
    <Dim size="1024"/>
  </Int32>
  <Structure name="Point">
    <Int32 name="x"/>
    <Int32 name="y">
      <Dim size="256"/>
    </Int32>
    <Int32 name="z">
      <Dim size="1024"/>
    </Int32>
    <Dim size="256"/>
  </Structure>
</Dataset>
```

``` c
Dataset {
    Int32 u[256][1024];
    Structure {
        Int32 x;
        Int32 y[1024];
        Int32 z[256];
    } Points[256];
} vol_1_ce_4;
```

*Points{y\[7:256\]}* or *Points.y\[7:256\]*: Get all of the elements of the Array of Structure *Points* and for each of those elements get the elements 7 to 256 from the array *y*. Do not return the field *x*.
*Points\[0:9\]{y\[0:9\]}* or *Points\[0:9\].y\[0:9\]*: Get the first ten elements of *Points* and, for each of those, only the first ten elements of the array *y*.
*Points\[0:9\]{x;y\[0:9\]}*: Get the first ten elements of *Points* and, for each of those, only *x' and the first ten elements of the array*y''.
*Points\[0:9\]*: Get the first ten elements of *Points* (both fields are included)
*Points* or *Points\[\]* or *Points\[0:\]*: Get all of *Points* with the subtle difference that if *Points* uses a shared dimension, the last of the three CEs will replace that with an anonymous dimension (see the section on shared dimensions, below).

``` xml
<Dataset name="vol_1_ce_5">
  <Int32 name="u">
    <Dim size="256"/>
    <Dim size="1024"/>
  </Int32>
  <Structure name="Points">
    <Int32 name="x"/>
    <Int32 name="y"/>
    <Structure name="sounding">
      <Int32 name="height">
        <Dim size="1024"/>
      </Int32>
      <Int32 name="pressure">
        <Dim size="1024"/>
      </Int32>
    </Structure>

    <Dim size="256"/>
  </Structure>
</Dataset>
```

``` c
Dataset {
    Int32 u[256][1024];
    Structure {
        Int32 x;
        Int32 y;
        Structure {
            Int32 height[1024];
            Int32 pressure[1024];
        } sounding;
    } Points[256];
} vol_1_ce_5;
```

*Points\[0\]{x,y,sounding{height\[0:8:\]}}*: Get only the first element of *Points* and, for that, get the fields *x*, *y* and *sounding* but for *sounding* get only every 8<sup>th</sup> element of the field *height* and elide the field *pressure*. An equivalent way of writing this expression is *Points\[0\]{x,y,sounding.height\[0:8:\]}*. The *{}* syntax provides an easy way to request *x*, *y* and *sounding.height\[0:8:\]* without having to repeat *Points\[0\]* three times. A CE like *Points\[0\].x;Points\[0\].y;Points\[0\].soundings.height\[0:8:\]* is legal, but *Points\[0\]* will only appear once in the result and a CE where *Points* is sliced differently is not legal. That is, ~~*Points\[0\].x;Points\[0:10\].y;Points\[15\].soundings.height\[0:8:\]*~~ is not legal because *Points* can appear only once in the result but has been sliced three different ways in the CE. In any CE, each variable can be constrained only one way.

### How Sequences fit into this syntax

The *Sequence* type is more general data type in DAP4 than in DAP2 where
it was significantly limited. In DAP4 Arrays of Sequences will be
supported as will Sequence elements that are themselves Arrays. A
Sequence variable is conceptually like a table where each field in the
Sequence is a column in the table (or like an array of Structures, where
the size of the single array dimension is a secret). Note that while
there is a big difference between the value held by a Structure and a
Sequence, each has the same subsetting syntax in the CE (although
Sequences may have filters applied while Structures may not).

``` xml
<Dataset name="vol_1_ce_6">
  <Sequence name="s1">
    <Int32 name="x"/>
    <Int32 name="y"/>
  </Sequence>

  <Sequence name="s2">
    <Int32 name="x"/>
    <Int32 name="y"/>
    <Dim size="100"/>
  </Sequence>

  <Sequence name="s3">
    <Int32 name="z"/>
    <Int32 name="x">
      <Dim size="10"/>
    </Int32>
  </Sequence>

  <Sequence name="s4">
    <Int32 name="z"/>
    <Int32 name="x">
      <Dim size="1024"/>
    </Int32>
    <Dim size="100"/>
  </Sequence>

</Dataset>
```

``` c
Dataset {
    Sequence {
        Int32 x;
        Int32 y;
    } s1;

   Sequence {
        Int32 x;
        Int32 y;
    } s2[100];

    Sequence {
        Int32 z;
        Int32 x[10];
    } s3;

     Sequence {
        Int32 z;
        Int32 x[1024];
    } s4[100];
} example;
```

*s1*: All of Sequence *s1*.
*s1{x;y}*: Also all of Sequence *s1*.
*s1{x}* or *s1.x*: every 'row' of Sequence *s1*, but just field *x*.
*s2{x;y}*: All one hundred elements of the Array *s2*. Same as *s2* and *s2\[0:99\]{x,y}* or *s2\[\]{x;y}*.
*s2\[0:9\]{x;y}*: The first ten elements of *s2*. That would be 10 Sequences and for each, both the fields *x* and *y*.
*s3{} \| z \< 10*: Every instance of the Sequence *s3* where z is less than 10. Note that this is the first example of a *filter*, a topic that is discussed in much more detail later on.

### Subsetting and Shared Dimensions

*Shared Dimensions* provide additional information to indicate that a
group of arrays share certain relationships; that specific groups of the
arrays form *coverages* by indicating how dimensions of *Maps* and
*Arrays* are linked. The DAP4 CE syntax provides a way to slice a Shared
Dimension so that slice can be used by all of the *Maps*/*Arrays* that
use it without repeating the slicing operation for each Map or Array.
The syntax can be read 'Assign the shared dimension *X* this slice and
looks like *row=\[10:19\]*. The only difference syntactically between a
shared dimension slice and the slice operator applied to a one
dimensional array is the assignment operator (*=*). All of the
variations of the slice operator possible for an array are accepted for
shared dimension slicing. In any CE, all of the shared dimension slicing
clauses must precede the variable subsetting clauses.

**Note** DAP4 uses XML for it's actual grammar, and because that's wordy
this document includes a mock notation. I will extend that notation used
so far so it includes concepts needed to mimic DAP4's notation for a
coverage:

- The keyword *Dimensions* introduces a list of symbols and their sizes.
  (That is the definition of a Dimension in DAP4; a size bound to an
  identifier.)
- Arrays where every dimension uses a *Dimension* to supply its extent
  are *Maps*. Maps are the arrays that hold the *domain* values for a
  *coverage*.
- Arrays that use parenthesis ***()*** in place of brackets to indicate
  the sizes of dimensions and which *use the names of maps* to do so
  *for at least one dimension* hold the *range* values for the coverage.
  These are the coverage's *data array* (aka *array* as distinct from
  *maps*).
- As with the previous examples, both the official and the mock syntax
  are shown and these datasets are available from the OPeNDAP test
  server.

#### Example of this syntax

``` xml
<Dataset name="vol_1_ce_7">
  <Dimension name="nlat" size="100"/>
  <Dimension name="nlon" size="50"/>

  <Float32 name="lat">
    <Dim name="nlat"/>
  </Float32>
  <Float32 name="lon">
    <Dim name="nlon"/>
  </Float32>

  <Float32 name="temp">
    <Dim name="nlon"/>
    <Dim name="nlat"/>
    <Map name="lat"/>
    <Map name="lon"/>
  </Float32>

  <Float32 name="sal">
    <Dim name="nlon"/>
    <Dim name="nlat"/>
    <Map name="lat"/>
    <Map name="lon"/>
  </Float32>

  <Float32 name="O2">
    <Dim name="nlat"/>
    <Dim name="nlon"/>
    <Map name="lon"/>
    <Map name="lat"/>
  </Float32>

  <Float32 name="CO2">
    <Dim name="nlon"/>
    <Dim name="nlat"/>
    <Dim size="10"/>
    <Map name="lat"/>
    <Map name="lon"/>
  </Float32>

</Dataset>
```

``` c
Dataset {
    Dimensions: nlat=100, nlon=50;
    Float32 lat[nlat];
    Float32 lon[nlon];

    // The maps ''lat'' and ''lon'' are used here and define a coverage
    Float32 temp(lon)(lat);
    Float32 sal(lon)(lat);
    Float32 O2(lat)(lon);
    Float32 CO2(lon)(lat)[10];
} shared_dimensions;
```

#### Examples of the anonymous grouping syntax and coverage subsetting by index

*nlat=\[0:9\];nlon=\[10:19\];lat; lon; temp*: This will return Dimensions nlat=10, nlon = 10, *lat*, *lon* and *temp* such that lat an lon are 10 element vectors and *temp* is a 10 x 10 array.
*nlat=\[0:9\];nlon=\[10:19\];lat; lon; temp; sal*: Same as above, but with both *temp* and *sal* included. This example shows how two or more arrays holding range variables can be accessed along with their Maps without sending multiple copies of the Maps. Similarly, ...
*nlat=\[0:9\];nlon=\[10:19\];lat; lon*: This CE requests just the arrays that hold the domain values, while ...
*nlat=\[0:9\];nlon=\[10:19\];temp; sal*: This CE requests just the arrays that hold the range values. Taken together, the two preceding examples support clients that read the domain values first and then display a map (for example) providing a way for someone to view the data's geographical extent before accessing the values them selves. Also note that there is no restriction that the same shared dimension slices must be used for both requests; like DAP2, each request in DAP4 is *stateless*.
*nlat=\[0:9\];nlon=\[10:19\];temp\[\]\[\]; sal\[\]\[\]*: This CE requests exactly the same data as the previous one, but uses the *\[\]* notation to indicate that the shared dimensions should be used for the subset. An example below shows how this notation can be used to mix local and shared dimension slicing.
*nlat=\[0:4:\];nlon\[0:4:\];CO2*: This CE decimates *CO2* by returning every fourth value in the first two dimensions
*nlat=\[0:4:\];nlon\[0:4:\];CO2\[\]\[\]\[0:4:\]*: This CE introduces the second meaning for *\[\]*. When the empty braces are used for a dimension that corresponds to a shared dimension, it means *use the shared dimension slice*. This is useful because some arrays contain a mixture of shared and anonymous dimensions and it's desirable to slice both, using a shared dimension slice previously defined where applicable and an anonymous slice where that's needed. This expression will decimate *CO2* by four in each of its three dimensions.
*nlat=\[0:4:\];nlon\[0:4:\];CO2\[\]\[1\]\[0:4:\]*: To override the slicing provided by a shared dimension slice, simply replace the *\[\]* with a local dimension slice.

## Constrained DMR Objects

When a DAP4 server receives a request for a Data response, it must build
and return a Data Response Document that contains a text/xml part
containing a DMR, a separator and a binary part that contains the data
values. The organization of the Data Response Document is described in
detail elsewhere in this document. In this section the focus is on the
DMR returned in the first part of the response and how it relates to the
DMR for the original unconstrained dataset. We refer to the original
dataset's DMR as the *DMR* and the DMR associated with the data response
as the *CDMR* (short-hand for Constrained DMR), although a data response
can be generated using a null CE, we consider that a constraint, too.

The DMR contains a number of declarations for the dataset: Enumerations,
Dimensions, Attributes, Groups and Variables. Each DMR and CDMR must
follow the rules for the DMR described in this specification and,
because DAP4 is a stateless protocol, each response from a server must
stand on its own. Since a Constraint Expression alters the data returned
(limiting variables, changing the size of dimensions and so on), it
stands to reason that the contents of the CDMR will vary for any given
dataset based on the CE. Furthermore, a goal of DAP4 is to specify that
the CDMR be 'minimal' containing no unused definitions.

Because filters alter the values of variables, but not whether a
variable is returned, they have no affect on the CDMR. Only the
subsetting operators will be discussed here.

### Enumerations

An enumeration is included in the CDMR only if some variable in the CE
references it. A null CE returns the entire dataset, so it effectively
references every variable. FIXME Make sure that 'variable' and 'field'
are defined correctly in the terms section. jhrg 12/31/13

### Shared Dimensions

Shared Dimension declarations from the DMR are not included in the CDMR
unless the Shared Dimension is used by a variable that has been
projected and that variable does not override that shared dimension
using a local slicing operation. FIXME Refer to the DMR from the
previous section and show some examples. jhrg 12/31/13

### Variables

Each clause in the constraint must specify a variable and that variable
will be declared in the CDMR. The variable must be referenced by a FQN
unless it is declared at the top level of the DMR and the short-hand
notation is used where the leading slash (*/*) is elided.

#### Array Variables

Array variables follow all the rules for *Variables* with the additional
conditions that their dimensions may appear altered depending on the CE.
If the local slicing operations are used, then the sliced dimensions
will have the size given be the slice operator, not the size as shown in
the full dataset's DMR. If a shared dimension is sliced and the Array
uses that slice, then its size will reflect that. Arrays may mix shared
dimension slices and local slices and the result must be correctly
reflected in the specific variable's declaration.

Note that slicing never affects the *rank* of an array.

#### Structure Variables

If the variable is a Structure, then either the entire Structure is
included or a subset of its fields will be included in the variable
declaration where the fields are those specifically mentioned in a
constraint projection. As with all other variables, each variable in the
structure will have the same rank and type as the original declaration
in the DMR.

#### Sequence Variables

If the variable is a Sequence, then for declaration purposes, it is
treated like a Structure (as above). Note that applying a filter to a
Sequence will not change its declaration form because the number of
records in the sequence is not specified in the DMR. Note also that
mentioning a Sequence field in the filter does not necessarily mean it
will be included in the DMR. It will only be included if it is mentioned
in the projection part of the constraint.

### Groups

Each declaration in the CDMR that corresponds to a declaration in the
DMR will cause its containing group (and that group's parents) to be
included in the CDMR. This ensures that the FQN for a declaration in the
CDMR is the same as in the DMR.

### Attributes

Attributes are unaffected by the CE and are simply included in the CDMR,
with the stipulation that attributes for variables that are not included
in the CDMR won't be part of the CDMR. Essentially DAP4 views those
attributes as part of the variables and explicitly excluding the
variable from the CDMR (by providing a CE that does not include it)
excludes its attributes too.

There is one situation that bears mention, however. Many datasets
include attributes that describe domain-specific values for their
values. For example, imagine a atmospheric profile that includes
information about the minimum and maximum temperatures of that profile.
If the values are stored in an array and the array is sliced so that
only a subset of values are returned, the attributes will provide
correct values for the original data *but possibly not the data returned
in the response* because the slicing operation has removed some of the
values of the array. Because DAP4 is a *domain neutral* protocol, it has
no knowledge about how the values of a specific attribute relate to the
values of the variable and cannot adjust the values of the attribute to
match the CE.

## Filters

While *subsetting* provides ways to choose data based on the dataset
structure and the types of the variables, *filters* provide a way to
choose data based on their values. The values to be returned are denoted
using one or more simple predicates. The general syntax for a filter
expression is to follow a subset expression with a pipe (**\|**) and one
or more filter predicates. Multiple predicates are separated by commas
and the value of complete predicate is the logical AND of the
comma-separated subexpressions.

Filter expressions can be applied to Sequence, Array and Coverage
variables. In each case the result of the filter operation returns *the
same type* variable. A Sequence variable is essentially a table of
values and thus can be thought of as containing a number of rows and the
filter expression is applied to *each row* in the order those rows are
provided to the expression evaluator. Every row that satisfies the
predicate will be included in the value returned; those that don't will
not be included in the result. In each case, the filter is applied to
each value of the Array or Sequence and the result returned. Note that
no new values are computed by these operations; no interpolations,
means, etc., are performed. A filter applied to an Array returns an
Array with the same shape (the same rank and the same dimension sizes)
but the elements that fail the predicate will be replaced with the
variable's *No Data* value. The predicate is applied to each element of
the array iteratively. For an Array, the value to use for No Data must
be provided using the special sub-clause *ND = value*.

In the following, the behavior of filtering expressions on Sequences,
Arrays and Coverages with be covered in three separate sections.

### Filters on Sequences and Arrays

#### Example: Filters

``` xml
<Dataset name="vol_1_ce_8">

  <Float32 name="temp">
    <Dim size="10"/>
  </Float32>

  <Float32 name="u">
    <Dim size="256"/>
    <Dim size="256"/>
  </Float32>

  <Float32 name="v">
    <Dim size="256"/>
    <Dim size="256"/>
  </Float32>

  <Sequence name="Points">
    <Int32 name="x"/>
    <Int32 name="y"/>
  </Sequence>

</Dataset>
```

``` c
Dataset {
    Float64 temp[10];
    Float32 u[256][256];
    Float32 v[256][256];
    Sequence {
        Int32 x;
        Int32 y;
    } Points;
} arrays;
```

*temp\|temp\<7,ND=NaN*: Return all ten elements of *temp* but replace those elements that do not satisfy the predicate *temp \< 7* with the *No Data* (**ND**) value.
*temp\|temp\<7,ND=0*: Same as the above, but use zero as the value for No Data.
*u\|u\>10,ND=-1e-37*: Return *u* with any element that is not *\> 10* set to the No Data value of -1 \* 10<sup>-37</sup>.
*u\|u\>10,ND=NaN*: Return *u* with any element that is not *\> 10* set to the No Data value of Nan.
*u\[0:2:99\]\[0:2:99\]\|u\>10,ND=NaN*: This shows how filtering can be combined with subsetting by index using this syntax. The index subsetting is performed first and the resulting 50 X 50 array is filtered. The return value is a 50 x 50 Float32 array with elements \<= 10 replaced with NaN.

### Filters and more complex data types

The basic syntax for filters is that there is a subsetting expression, a
pipe (**\|**) and then one or more filter predicates. This syntax can
appear any place a *selection expression* can appear, so it can be used
inside braces when an Array or Sequence is a field of a Structure or
Sequence. Note that the filter expression prefix operator binds to the
scope of the brace immediately to its left. Some examples follow.

#### Example: Filters on complex types

``` xml
<Dataset name="vol_1_ce_9">
  <Sequence name="Points1">
    <Int32 name="x">
      <Dim size="100"/>
    </Int32>
    <Int32 name="y"/>
  </Sequence>

  <Sequence name="Points2">
    <Int32 name="x"/>
    <Int32 name="y"/>
    <Sequence name="sounding">
      <Int32 name="depth"/>
      <Int32 name="temp"/>
      <Dim size="20"/>
    </Sequence>
  </Sequence>

  <Structure name="Points3">
    <Int32 name="x"/>
    <Int32 name="y"/>
    <Sequence name="raw">
      <Int32 name="depth"/>
      <Int32 name="temps">
        <Dim size="4"/>
      </Int32>
      <Dim size="300"/>
    </Sequence>
  </Structure>

</Dataset>
```

``` c
Dataset {
    Sequence {
        Int32 x[100];
        Int32 y;
    } Points1;

    Sequence {
        Int32 x;
        Int32 y;
        Sequence {
            Int32 depth;
            Int32 temp;
        } sounding;
    } Points2[20];

    Structure {
        Int32 x;
        Int32 y;
        Sequence {
            Int32 depth;
            Int32 temps[4];
        } raw;
    } Points3[100]

} complex_types_example;
```

*Points1{x\[0:9\]\|x\<10,ND=-127;y}\|y\<3*: For the Sequence *Points1*, return the rows of data where *y* is less than 3. In those rows, subset *x* so that only the first ten elements are included and then filter that so that elements \>= 10 are replaced by the No Data value of -127. Note that the filter sub-expression *y\<3* needs no *No Data* (**ND**) value because it is applied to the field of a Sequence and not the elements of a array.
*Points2\[10:19\] { x; y; sounding \| depth \> 10 } \| 20 \< x \< 40, y \<35*: This selection expression first finds the index subset of *Points2* and arranges to return the fields *x*, *y* and *sounding* where *x*and *y* satisfy the predicates *20 \< x \< 40* and *y \<35* and for the field *sounding*, which is a Sequence itself, it will return both fields where *depth \> 10*. This example points out an important aspect to the syntax and to expression evaluation: the order of evaluation of the filter predicates happens after the index and variable and/or field subsetting. The order of evaluation of the complete filter predicates can happen in any order (i.e., the *20 \< x \< 40, y \<35* and *depth \> 10* predicates can happen in any order. The order of evaluation of the filter predicate subexpressions (i.e., *20 \< x \< 40* and *y \<35*) is also unspecified. Another way to write this selection expression is...
*Points2\[10:19\] \| 20 \< x \< 40, y \<35, sounding.depth \> 10*: The same result as above...
*Points3\[3:2:8\] {x; y; raw{temps\[2\] \| temps \> 7,ND=-1}}*: In this expression the *temps* field of the Sequence *raw* is still an Array, it's just an Array with a single element, which illustrates that neither the subsetting nor filtering operations alter the types of the variables.

### The Mask operator may be applied only to an Array

For this predicate, the syntax is *dest \*= source* where any
element<sub>**i,j**</sub> in the *source* array with the value equal to
the given *No Data* value will result in the corresponding element of
the *dest* array being set to that array's *No Data* value. Other values
in the *dest* array are not affected. The intent of this is that
filtering performed on a map array can then be applied to a data array
that uses the map (although there's no actual restriction that this be
only used with coverages; any two isomorphic arrays in an anonymous
group can be used). To use the Mask (*\*=*) predicate, the array on the
Right Hand Side of the sub-clause must have been previously filtered
(and so must have a given ND value).

``` xml
<Dataset name="vol_1_ce_10">
  <Float32 name="lat">
    <Dim size="100"/>
    <Dim size="50"/>
  </Float32>

  <Float32 name="lon">
    <Dim size="100"/>
    <Dim size="50"/>
  </Float32>

  <Float32 name="temp">
    <Dim size="100"/>
    <Dim size="50"/>
  </Float32>
</Dataset>
```

``` c
Dataset {
    Float32 lat[100][50];
    Float32 lon[100][50];
    Float32 temp[100][50];
} vol_1_ce_10;
```

*lat \| lat \< 20, ND=-255; temp \| temp \*= lat, ND=-255*: Filter *lat* so that all values \< 20 are replaced with the No Data value, then use that as a mask, making all of the corresponding elements of 'temp'' also be No Data. Note that this operator forces an order on filter evaluation.
*lat\[0:20\]\[0:20\] \| lat \< 20, ND=-255; lon\[0:20\]\[0:20\] \| -100 \< lon \< -80, ND=-255; temp\[0:20\]\[0:20\] \| temp \*= lon, temp \*= lat, ND=-255*: This will result in *temp* effectively being masked by the logical AND of *lat* and *lon*

#### Filters and Coverages

The same filtering operations that can be applied to simple arrays can
be applied to a coverage by simply using its Maps in the Constraint
expression (i.e., the subset and filter sub-expressions). The array
filtering operation previously described can easily be applied to a
coverage. Below we show two cases; where the maps are vectors and where
they are two-dimensional arrays.

``` xml
<Dataset name="vol_1_ce_11" dapVersion="4.0" dmrVersion="1.0" xml:base="file:dap4/vol_1_ce_11.xml"
  ns="http://xml.opendap.org/ns/DAP/4.0#" xmlns:dap="http://xml.opendap.org/ns/DAP/4.0#">
  <Dimension name="nlat" size="100"/>
  <Dimension name="nlon" size="50"/>

  <Float32 name="lat">
    <Dim name="nlat"/>
  </Float32>
  <Float32 name="lon">
    <Dim name="nlon"/>
  </Float32>

  <Float32 name="temp">
    <Dim name="nlon"/>
    <Dim name="nlat"/>
    <Map name="lon"/>
    <Map name="lat"/>
  </Float32>

</Dataset>
```

``` c
Dataset {
    Dimensions: nlat=100, nlon=50;
    Float32 lat[nlat];
    Float32 lon[nlon];

    Float32 temp(lon)(lat);
} vector_maps;
```

*lat \| lat \< 20,ND=-255; lon \| -100 \< lon \< -80, ND=-255'; temp*: Return all of *lat*, *lon* and *temp* where *lat* and *lon* have ben filtered as per the predicates. The values of *temp* are not altered.
*nlat=\[0:20\]; nlon=\[0:20\]; lat \| lat \< 20, ND=-255; lon \| -100 \< lon \< -80, ND=-255'; temp*: The same as above, but the maps *lat* and *lon* and the array *temp* are subset using the index subsetting expression and the resulting arrays are filtered. Note that because this is a coverage, the Maps' dimensions are tied to *temp*'s dimensions using Shared Dimensions and we can use the Shared Dimension slicing to specify the slicing once and use it for all three of the Maps/Arrays. That is, *lat*, *lon* and *temp* are each subset using *\[0:20\]\[0:20\]*. Contrast this with the corresponding example in the previous section where the slicing subset had to be explicitly specified for each Array.
*nlat=\[0:20\]; nlon=\[0:20\]; lat \| lat \< 20, ND=-255; lon \| -100 \< lon \< -80, ND=-255'; temp \> 7, ND=-255*: Same as above, but now *temp* is filtered too. Note that the filters applied to *lat* and *lon* have no affect on *temp*.

``` xml
<Dataset name="vol_1_ce_12" dapVersion="4.0" dmrVersion="1.0" xml:base="file:dap4/vol_1_ce_12.xml"
  ns="http://xml.opendap.org/ns/DAP/4.0#" xmlns:dap="http://xml.opendap.org/ns/DAP/4.0#">
  <Dimension name="x" size="100"/>
  <Dimension name="y" size="50"/>

  <Float32 name="lat">
    <Dim name="x"/>
    <Dim name="y"/>
  </Float32>
  <Float32 name="lon">
    <Dim name="x"/>
    <Dim name="y"/>
  </Float32>

  <Float32 name="temp">
    <Dim name="x"/>
    <Dim name="y"/>
    <Map name="lon"/>
    <Map name="lat"/>
  </Float32>

</Dataset>
```

``` c
Dataset {
    Dimensions: x=100, y=50;
    Float32 lat[x][y];
    Float32 lon[x][y];

    Float32 temp(lon)(lat);
} two_dim_maps;
```

*lat \| lat \< 20,ND=-255; lon \| -100 \< lon \< -80, ND=-255'; temp*: These examples repeat the above three, but show that the same syntax applies to the case where the maps are N-dimensional (in this case N == 2).
*nlat=\[0:20\]; nlon=\[0:20\]; lat \| lat \< 20, ND=-255; lon \| -100 \< lon \< -80, ND=-255'; temp*:
*nlat=\[0:20\]; nlon=\[0:20\]; lat \| lat \< 20, ND=-255; lon \| -100 \< lon \< -80, ND=-255'; temp \> 7, ND=-255*:

## Grammar for Bison

Shown below is a LALR(1) grammar for the syntax described above.

Implementation note: A *driver* object is passed to the parser (and so
is a *scanner* object) which facilitates not only error reporting but
also the implementation of *parser actions*. Given that a DDS/DDX/DMR
object holds all of the variables for a given dataset, that can be
referenced by the parser's driver object and methods on that driver
object can be used to validate the semantics of the actions during the
parse process. The same driver object can also be used to record
information in that DDS/... object, or the variables it contains, for
use later when building a response object/document (i.e., when
serializing the variables that have been selected and (possibly) subset
and filtered.

### Production rules for *bison*

``` bnf
%start expression;

expression : clauses
| dimensions ";" clauses
;

dimensions : dimension
| dimensions ";" dimension
;

dimension : id "=" index
;

clauses : clause
| clauses ";" clause
;

clause : subset
| subset "|" filter
;

// mark_variable returns a BaseType* or throws Error
subset : id
| id indexes
| id fields
| id indexes fields
;

indexes : index
| index indexes
;

index   : "[" "]"
| "[" WORD "]"
| "[" WORD ":" WORD "]"
| "[" WORD ":" WORD ":" WORD "]"
| "[" WORD ":" "]"
| "[" WORD ":" WORD ":" "]"
;

fields : "{" clauses "}"
;

filter : predicate
| filter "," predicate
;

// Here we use a grammar that is overly general: id op id is not really
// supported by the CE evaluator. However, id op constant, which captures
// the intent of the evaluator design introduces a number of reduce/reduce
// conflicts because any sensible definition of 'constant' will be the
// same as the definition of 'name'. This happens because we must make 'name'
// far more general than ideal (it must include tokens that start with digits
// odd characters that clash with the operators, et cetera). Note that the
// actions here must test for id == "ND" and op == "=", along with a host
// of other checks.

predicate : id op id
          | id op id op id
;

//           | "ND" "=" id

op : "<"
   | ">"
   | "<="
   | ">="
   | "=="
   | "!="
   | "~="

   | "<<"
   | ">>"

   | "@="

   | "="
;

id : path
| "/" path
| group "/" path
;

group : "/" name
| group "/" name
;

path : name
| path "." name
;

// Because some formats/datasets allow 'any' name for a variable, it is possible
// that a variable name will be a number, etc. The grammar also allows STRING
// to support "name"."name with spaces and dots (.)".x
name : WORD
| STRING
;
```

### Scanner definitions and actions used by the production rules above

Here are the definitions and actions for a *flex* scanner that returns
tokens for the above grammar. The quoted strings are very important
because in the grammar, *id*s can be either *WORD*s or *STRING*s (i.e.,
things wrapped in double quotes). This makes it easy to support variable
names that include any character (including ones we attach special value
to, like dot (.)) without ambiguity or needing to escape them. Only the
backslash and double quote need to be escaped. Note that the *WORD*
definition could be made much tighter, eliminating characters like
percent (*%*) but then *id*s that use those would have to be quoted. I
decided to include all the characters in *WORD* I could without
introducing ambiguity into the actions.

(The syntax-dependent display is a bit hosed because of the actions for
quoted strings.)

``` awk
/* This pattern ensures that a word does not start with '#' which
    is the DAP2 comment character. */
WORD    [-+a-zA-Z0-9_%*\\~@!][-+a-zA-Z0-9_%*\\#~@!]*

%%

"["     return token::LBRACKET;
"]"     return token::RBRACKET;
":"     return token::COLON;
"," return token::COMMA;
";" return token::SEMICOLON;
"|"     return token::PIPE;
"{" return token::LBRACE;
"}" return token::RBRACE;
"/"     return token::GROUP_SEP;
"."     return token::PATH_SEP;
"="     return token::ASSIGN;

"=="    return token::EQUAL;
"!="    return token::NOT_EQUAL;
">" return token::GREATER;
">="    return token::GREATER_EQUAL;
"<"     return token::LESS;
"<="    return token::LESS_EQUAL;
"~="    return token::REGEX_MATCH;
"<<"    return token::LESS_BBOX;
">>"    return token::GREATER_BBOX;
"*="    return token::MASK;

[ \t]+  /* ignore these */

[\r\n]+ /* ignore these */

{WORD}  { yylval->build<std::string>(yytext); return token::WORD; }

<INITIAL><<EOF>> return token::END;

["]    { BEGIN(quote); yymore(); }

<quote>[^"\\]*  yymore(); /* Anything that's not a double quote or a backslash */

<quote>[\\]["]  yymore(); /* This matches the escaped double quote (\") */

<quote>[\\]{2}  yymore(); /* This matches an escaped escape (\\) */

<quote>[\\]{1}  {
                    BEGIN(INITIAL);
                    if (yytext) {
                        YY_FATAL_ERROR("Inside a string, backslash (\\) can escape a double quote or must itself be escaped (\\\\).");
                    }
                }

<quote>["]  {
                /* An unescaped double quote in the 'quote' state indicates the end of the string */
                BEGIN(INITIAL);
                yylval->build<std::string>(yytext);
                return token::STRING;
            }

<quote><<EOF>>  {
                  BEGIN(INITIAL);   /* resetting the state is needed for reentrant parsers */
                  YY_FATAL_ERROR("Unterminated quote");
                }

.   {
        BEGIN(INITIAL);
        if (yytext) {
            YY_FATAL_ERROR("Characters found in the input were not recognized.");
        }
    }
```

# Discussion

[Dennis](User:dmh "wikilink") : 9/1/13 **Lexical Forms**

I think that for specification purposes, we need to stick to the
previously defined lexical forms that are defined in section 11 of the
specification. Introducing any alterations in those lexical elements
will just cause confusion.



[Jimg](User:Jimg "wikilink") 09:34, 3 September 2013 (PDT) I agree and
I'll look over that and try to unify the documents

[Dennis](User:dmh "wikilink") 9/2/13: **Curly Braces**

The problem that leads to the use of curly braces is this. What we are
doing it specifying a series of paths from top-level variables of type
Structure or Sequence through any nested Structures or Sequences to a
final field *S1.S2.x*, *S1.S2* etc.

In some (limited?) cases, it would be desirable to unify those paths so
that common prefixes of the paths need not be repeated. I say limited,
because I have no idea how common such repetions would occur in
practice. In any case, we can unify two paths *S1.S2.x* and *S1.S3*,
say, as *S1.{S2.x;S3}* \[Note that I am using ".{" instead of just "{").



[Jimg](User:Jimg "wikilink") 09:51, 3 September 2013 (PDT) Hmmm. OK.

One issue that needs resolution is this. Suppose, we want to unify
S1.S2.x and S1.S2. Writing *S1.S2.{x}* is clearly incorrect. We could
write *S1.{S2;S2.x}*, or perhaps *S1.S2.{;x}*, or disallow it.

In any case, we need to specify some rules, such as, do we require
maximal prefixes or allow less than maximal prefixes?



[Jimg](User:Jimg "wikilink") 09:57, 3 September 2013 (PDT) We agreed
this would result in an error (s1.{s2,s2.x})

I must confess that using curly braces is, IMO, less than optimal
because I would prefer to reserve it in case we need it for use in
filter expressions. The only alternative I can see is *\<...\>*, and
that does not seem attractive.

[Dennis](User:dmh "wikilink") 9/2/13: **Anonymous Grouping**

I am not enamored of this idea. Especially when used with filters. My
interpretation is that we are trying to again avoid repeating
information such as filters or dimension subsets.

I should note that Ferret addresses the same issue using a form of *Let
x = ... in ...* notation and I think for our purposes some variant of
this makes for a cleaner solution.

In line with that I propose that we use a local variable approach, where
we can say things like *D=0:4:12* and F=\|expr. Then these local
variables can be used later in the constraint.



[Jimg](User:Jimg "wikilink") 09:57, 3 September 2013 (PDT) I don't think
we need to introduce local variables and we should avoid that if they
are not absolutely needed.

[Dennis](User:dmh "wikilink") 9/2/13: **Variable Binding in Filters**

Given a filter expression *v\|expr*, the expression part of the filter
will contain (at least?, only?) one variable that must be bound. An
issue to be solved is how to bind all variables in a filter to something
in the underlying dataset, and what restrictions we put on that binding.

One example above:


*Points2\[10:20\] \| 20 \< x \< 40, y \<35, sounding.depth \> 10*

illustrates the binding problem. I would argue this should be written:


''Points2\[10:20\] \| 20 \< Points2.x \< 40, Points2.y \<35,
Points2.sounding.depth \> 10'

One side note: we should not allow any free variables that are not bound
to something on the immediate left side of the filter. This means no
references to data variables not on the left side and no reference to
outer variables when filters are nested.



[Jimg](User:Jimg "wikilink") 09:34, 3 September 2013 (PDT) I agree - no
free variables and FQNs in the filter subexpressions.

[Dennis](User:dmh "wikilink") 9/2/13: **Sequence Filtering**

\[Notation: S refers generally to a Structured object, SQ to a Sequence,
and A to an array of objects\]

For a simple sequence, SQ, and given *SQ\|expression* and assuming we
somehow bind SQ to some variable x in the expression, we need to decide
what things inside SQ (e.g. *SQ.f1*) we are allowed to reference in the
expression. We had this discussion a long time ago, and I proposed that
we restrict it to only be able to reference scalar atomic variables. So
*SQ.S\[0\].x* would be disallowed.

The binding issue is complicated if we want to return some subportion of
a filtered variable. So, we might want to say *SQ.{f1;f2}\|SQ.f3\>5*.
This seems ok.

Perhaps more interesting is this: *S1\[1:2\].SQ.{f1;f2}\| ?.x \> 5.
Should this be written as*S1\[1:2\].SQ.{f1;f2}\| S1.SQ.x \> 5.

[Dennis](User:dmh "wikilink") 9/2/13: **Array Filtering**

When filtering arrays, many of the above issues for sequences also
occur.

Let us assume an array, A and a filter expression such as
*A\|expression*.

Suppose that A is of type Structure. Then presumably we need to decide
what fields inside A (e.g. *.f1*) we are allowed to reference in the
expression. Presumably, we can use the sequence proposal above restrict
it to only be able to reference scalar atomic variables. So *A.S\[0\].x*
would be disallowed.

Question: Above, it was indicated that SQ\[0:10\] is legal, so if A is
Sequence, can we treat it like an array and do Array filtering instead
of tuple filtering?

The issue of returning subfields of selected elements of A also occurs.
So, can we say *A.{f1;f2}\|A.f3\>5*? I think this conflicts with the
no-data idea; perhaps, we need no-data for missing structure fields.

\[\[User:dmh\]Dennis\|User:dmh\]Dennis\]\] 9/2/13: **No Data**

We agreed on the idea of no-data. The issue is specifying it. The *ND=*
idea is good. However, the discussion above seems to be implicitly
assuming we are only return atomic variables. What if what we are
returning is a whole Structure possibly containing nested structures?



[Jimg](User:Jimg "wikilink") 09:43, 3 September 2013 (PDT) Yes, at the
point where I thought about filters on arrays of non-atomic types, I
punted.

[Dennis](User:dmh "wikilink") 9/2/13: **@=**

I need to think about this, but questions concern the situation when the
map is more than 1-dimenensional. I think this could better be handled
using the local variable proposal above.



[Jimg](User:Jimg "wikilink") 09:46, 3 September 2013 (PDT) I think
there's an important point I didn't make in the part about this
operator: I want the return type to be the same as the 'input type'. So
if an Int32 a\[1024\]\[1024\] is filtered using @=, then you get an
Int32 \[1024\]\[1024\] out. If the users wants a subset of the array to
be filtered, they ask for that by subsetting the array and then
filtering the result.

[Dennis](User:dmh "wikilink") 9/2/13: **Restricting the use of filters**

After thinking about it, I believe we should put serious limits (for
now) on where filters can be used. Specifically, I propose the
following.

1.  No nested filters - one filter is allowed for each clause (a clause
    is what is separated by semicolons)
2.  The top level var in the clause is the bound variable of the filter
    and its name is used as the variable inside the filter.



[Jimg](User:Jimg "wikilink") 11:10, 3 September 2013 (PDT) \#1 I agree

[Jimg](User:Jimg "wikilink") 11:10, 3 September 2013 (PDT) \#2 I agree;
explanation: each clause has a top level name and that name is scope of
the filter expression.

IMO, Some consequences:

1.  No anonymous grouping
2.  scratch my named filters idea above (but keep the name range idea).b

The reason I propose this is that our language is getting too
complicated and I want to see how it implements before making it more
complex.

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")