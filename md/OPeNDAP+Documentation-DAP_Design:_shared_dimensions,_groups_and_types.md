Back: [DAP3/4#NC-DAP](DAP3/4#NC-DAP "wikilink")

## Definitions

Type definition
In the DDX all items are declarations which describe things actually
defined (assigned or otherwise associated with values) elsewhere. The
term *Type Definitions* refers to the representation of something in a
data set which defines a data type; the types are defined in the data
set and the DDX merely holds a representation of that definition - a
declaration.

<!-- -->

Dimension
A name bound to a size, e.g., "lon" has a size of 1024

<!-- -->

Coordinate variable
A name bound to both a dimension and a datatype, e.g.,"height" is a
vector of dimension "height" 32-bit floating point numbers, or
"latitude" is an array of dimension "x" by dimension "y" 32-bit floating
point numbers.

<!-- -->

Grid
One or more N-dimensional array of values bound to 1 to N coordinate
variables.

## DDX Document Organization

DAP and the DDX will be extended to include Groups, Shared dimensions
and user-defined types. Groups will be added as a kind of
constructor-type with properties similar to Structure and to Java or C++
namespaces. Unlike Structure, Groups cannot be dimensioned.

A rough syntax which describes how these additions will fit into the DAP
and the existing DDX Notation is (<font color="red">Replace with XML
schema</font>):

    Dataset :== Groups
    Groups :== null | Group Groups
    Group :== Types Dimensions Attributes Variables Groups
    Types :== null | Type Types
    Dimensions :== null | Dimension Dimensions
    Attributes :== null | Attribute Attributes
    Variables :== null | Variable Variables

This pseudo-grammar does not capture what can be produced for a *Group*,
et cetera. Instead it shows how these sections of the DDX must be
organized. It also does not show that a valid Dataset can have only
Types (user-define types) and does not need to have variables, but it
must have one or the other or both.

## Group

The DDX will be modified so that it contains one or more Groups. If only
one Group is present (which describes the case for DAP 3.2 and earlier)
then the declaration can be left out, but if there are two or more
groups, the declarations must be present.

Group characteristics:

- Any configuration of Groups other than one (anonymous) Group which
  holds all the variables in a data set must be declared.
- If declared, Groups must be named.
- A Group can contain any object, including a Group
- Variables and Attributes are named using */ <group name> / ... /
  <variable name>* to reflect their hierarchy.
- Each Group declares a new lexical scope for values.
- A Group cannot be an Array or a Grid (although the distinction between
  those two might become blurred or non-existent; Group is fundamentally
  a scalar container-type).
- This definition does not completely subsume the HDF5 Group type but is
  equivalent to the netCDF 4 version of it.

### Examples:

*This data set contains one Group - the root group - which has by
convention the name '/'*

    <Dataset ... >
        ...
    </Dataset>

*This data set contains two Groups, one after the other.*

    <Dataset ... >
    <group name="primary">
        ...
    </group>
    <group name="secondary">
        ...
    </group>
    </Dataset>

*This data set contains more Groups, and shows they can be nested.*

    <Dataset ... >
    <group name="primary">
        ...
        <group name="in_situ">
            ...
        </group>

    </group>
    <group name="secondary">
        ...
    </group>
    </Dataset>

### Discussion

In the past we have often talked about *Dataset* as a kind of
*Structure* but implicitly it's not exactly the same since there cannot
be an Array of datasets; The *Group* type captures this semantic
distinction.

In HDF5, the *Group* object is modeled after a general graph but here
it's uses a strict hierarchy, which simplifies both servers and clients
while retaining most of the utility of the HDF5 data type.

## Shared Dimensions

### Background on shared dimensions and coordinate variables

From an email exchange, John Caron wrote:

James:

> Is it that an dimension is a formal declaration of an independent
> parameter?

John:

> I know that some people prefer that interpretation. My own opinion is
> that's it more complicated. Abstractly, I think its reasonable to say
> that the number of dimensions of a variable indicates its
> dimensionality in the topological sense. I think its necessary to
> allow "independent variables" to have topological dimensionality \> 1.
> eg lat(x,y), lon(x,y). lat and lon can still be considered independent
> variables, but they are not orthogonal. Neither is associated
> exclusively with one dimension.
>
> Concretely, dimensions are used for all sorts of reasons, and are not
> just about topological dimensionality. For instance, they control the
> grouping of data and the layout of files. So in real files, you see
> this mixture of uses.
>
> That's why the explicit assignment of coord variables is needed, which
> makes your Grid attractive, because that's a way of explicitly saying
> what the independent variables are. One needs shared dimensions
> between data and coordinate variables, so that one can unambiguously
> assign coordinate values to a data value.
>
> The downsides of using Grid for this purpose:
>
> - the name "Grid" connotes gridded data, eg model data, and this
>   shared dimension thing is needed for other types of data, eg point
>   data.
> - If Grid scopes the dimension, then all variables sharing a dimension
>   have to be contained in the grid. So its impossible to have some
>   dimensions globally shared, and others locally shared.
>
> So my preference would be to use Groups to scope shared dimensions,
> rather than Grids. But still use Grids (or some evolution of Grids) to
> assign coordinate variables to data variables.

### Dimensions

Shared dimensions will be added to DAP in the *dimensions* section of
the *Dataset* or *Group* objects. Each dimension will consist of a name
and a size.


    <dimension name="lat" size="1024"/>
    <dimension name="lon" size="1024"/>

Characteristics of dimensions:

- Dimensions are not associated with a data type.
- Dimensions do not have attributes.
- Dimensions bound to a type define coordinate variables.
- Shared dimensions may be used by both Grids and Arrays.

## Coordinate variables and Grids

While dimensions are scoped at the Dataset or Group level, coordinate
variables are defined at the level of a Grid object. Grid objects in
DAP4 are different from those in DAP2 in three ways beyond using
(shared) dimensions:

1.  Each Grid object may hold more than one *Array* (what is often a
    dependent variable);
2.  Maps (often independent variables) may have more than one dimension;
    and
3.  Each Array within a Grid is not constrained to use all of the Grid's
    coordinate variables.

N.B: *Coordinate variables* in a Grid object are called *Maps* to
conform to the old nomenclature and to avoid (re)using the word
*dimension*.

Features of the DAP4 and DAP2 Grid object:

1.  Each Grid object defines a lexical scope.
2.  There is an explicit relation between the Grid object's maps
    (coordinate variables) and the indicial extents of the array.

### A very simple Grid object

    <grid>
        <map name="lon" dim="lon" type="Float32"/>
        <map name="lat" dim="lat" type="Float32"/>

        <array name="SST">
            <Byte/>
            <map name="lon">
            <map name="lat">
        </array>
    </grid>

Notes:

1.  The *map* object may have the same name as a *dimension* object.
2.  Map objects may have attributes, even though they are not shown in
    the example.
3.  In an Grid's *array* object, *\<map...\>* elements are used to
    specify the array's dimensions; the word *dimension* is avoided to
    cut down on confusion.

### A more complex Grid object

    <dataset>
        <dimension name="pt" size="4096">

        <grid>
            <map name="longitude" dim="pt" type="Float32"/>
            <map name="latitude" dim="pt" type="Float32"/>
            <map name="altitude" dim="pt" type="Float32"/>
            <map name="time" dim="pt" type="Float32">
                << attributes >> <!-- The syntax for attributes is in flux -->
            </map>

            <array name="Radioactivity">
                << attributes >> <!-- for example, scale_factor and add_offset -->
                <Byte/>
                <map name="longitude"/>
                <map name="latitude"/>
                <map name="altitude"/>
                <map name="time"/>
            </array>

            <array name="surface_temp">
                 << attributes >>
                 <float64/>
                 <map name="longitude"/>
                 <map name="latitude"/>
                 <map name="time"/>
            </array>
        </grid>
    </dataset>

### An example Grid with Maps that are not vectors

    <dataset>
        <dimension name="x" size="4096">
        <dimension name="y" size="4096">

        <grid name="SST_Swath">
            <!-- We could list multiple dims in a space-separated list
                 but purists will gag. I'm experimenting with different
                 syntaxes -->
            <map name="longitude" type="Float32"/>
                <dim name="x"/>
                <dim name="y"/>
            </map>
            <map name="latitude" type="Float32"/>
                <dim name="x"/>
                <dim name="y"/>
            </map>

            <!-- This grid has two maps, each of which are two-dimensional
                 arrays. It can be used to store satellite 'swath' data. -->
            <array name="SST">
                << attributes >> <!-- for example, scale_factor and add_offset -->
                <Byte/>
                <map name="longitude"/>
                <map name="latitude"/>
            </array>
        </grid>
    </dataset>

Note:

1.  The highest dimension of the Grid's Maps cannot exceed the
    dimensionality of the Grid's Array.
2.  When using the *\[\]* operator on a Grid in a DAP Constraint
    expression, the arguments enclosed in the square brackets correspond
    to the *dimensions* declared in the Map and not the Maps themselves.
    Thus a CE like *SST_Swath\[10:20\]\[40:50\]* means that the array
    *SST_Swath.SST* and the maps *SST_Swath.longitude* and
    *SST_Swath.latitude* will all be returned sub-sampled to elements 10
    to 20 in their first dimension and 40 to 50 in their second. **In a
    DAP2 grid where all of the maps are vectors, there is a one-to-one
    correspondence between the *\[\]* operators and Maps, but in a DAP4
    Grid there is a one-to-one correspondence between the *\[\]*
    operators and *dimensions*.**

## Types

Add a section for type definitions (technically, these are *type
equivalents* in the sense of C's *typedef*) and allow those type
equivalents to be used interchangeably within the existing DDX notation.
This would provide a way for a dataset stored using HDF5 to be presented
without loosing any type definitions it uses. At the same time this
would allow the existing notation which does not require types be
defined in cases where none were (e.g., a HDF 4 data file cannot contain
type definitions).

The syntax for a type definition

    <typedef name="point">
        << attributes >>
        <structure name="point">
            <int32 name="x"/>
            <int32 name="y"/>
        </structure>
    </typedef>

Notes:

1.  The contents of the *<typedef>* element are a DDX type
2.  The enclosed type and the *name* become synonymous.
3.  The enclosed type can include attributes.
4.  The type definition itself can include (optional) attributes.

Use of a type definition, assuming the typedef above

    <variable name="points" type="point">
        <dim size="100>
    </variable>

or

    <variable name="points">
        <type name="point"/>
        <dim size="100"/>
    </variable>

Notes:

1.  The XML elements *\<variable ...\>* and/or *\<type ..\>* are used
    because the typedef name can be any legal XML attribute value (a
    requirement because names in HDF5 are essentially not restricted to
    a particular character set) and thus cannot be an XML element name.
2.  This example uses the new (proposed) syntax for array declarations.
3.  Right now instead of a *\<variable ...\>* element in the DDX we have
    a collection of things like *\<Grid...\>*, *\<Array...\>* but we
    cannot reliably introduce arbitrary element names because of the
    character restrictions XML places on them. So I'm suggesting that we
    change the DDX to use the *\<variable...\>* element everywhere. And
    adopt the notion that A *variable* is scalar or vector/array
    depending on whether the element includes one or more *\<dim ...\>*
    elements.