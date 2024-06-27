[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Projections

Let us assume, for now, that the DAP4 constraint language will be
closely related to the DAP2 constraint language. We will start with
projections and address the issue of selections later.

As with DAP2, it can be assumed that a projection walks a path from the
root dataset to some simple variable (i.e. one of a cardinal type);
also, the path may be annotated with dimension ranges (\[a:b:c\]).

Mostly this will work fine in DAP4, except for the problem of shared
dimensions. Suppose we have this case.

    <Dataset name="dataset">
      <Dimensions>
        <Dimension name="lat" size="10"/>
        <Dimension name="lon" size="20"/>
      <Dimensions>
      <Variables>
        <Float32 name="lat">
          <Dimensions>
            <Dimension name="lat"/>
          </Dimensions>
        </Float32>
        <Float32 name="lon">
          <Dimensions>
            <Dimension name="lon"/>
          </Dimensions>
        </Float32>
        <Float32 name="temp">
          <Dimensions>
            <Dimension name="lat"/>
            <Dimension name="lon"/>
          <Dimensions>
          <Maps>
            <Map name="lat"/><Map name="lon"/>
          </Maps>
      </Variables>
    </Dataset>

So we have an array variable (temp) with two coordinate variables (lat
and lon) and shared dimensions lat and lon.

Suppose we request the following projections.

    temp[0:5][2:3],lat[0:3],lon[0]

The key thing to note is that we just broke the shared dimension because
the three variables no longer have the standard coordinate variable
relationship. This would seem like a bad idea. The problem is further
exacerbated when there are two variables such as temp and salinity that
use the same or overlapping sets of coordinate variables.

The only way I can think of to handle this is to modify the projection
syntax so non-anonymous (shared) dimensions ranges are specified in the
projection. For example, we might write something like this.

`lat=[0:5],lon=[2:3],temp[lat,lon],lat[lat],lon[lon]`

or more compactly

`lat=[0:5],lon=[2:3],temp,lat,lon`

The assumption is, then, that the same range is used everywhere for that
dimension.

Other Open Issues:

1.  Should it be possible to stop a projection path at a structure and
    return the whole structure instance?

*-Dennis Heimbigner*

## Discussion

I'm not exactly sure what the proposal here is, but I thought I'd
elaborate a bit more on the problems and present, without any particular
syntax, a solution. That said, I think this proposal and my 'discussion'
are basically in sync, modulo the syntax, which I omit.
[Jimg](User:Jimg "wikilink")

### Regarding Grids and Subsetting

I'm going to focus on the problems/issues associated with Grid
subsetting. I think we need to focus on this because it's such an
important feature of DAP - the ability to subset Grids.

Suppose we have two Grids G<sub>1</sub> and G<sub>2</sub> that share two
maps M<sub>1</sub> and M<sub>2</sub>. If we subsample G<sub>1</sub> and
G<sub>2</sub> using two different intervals over M<sub>1</sub> and
M<sub>2</sub>, then we have a problem because we need one set of
M<sub>1</sub> and M<sub>2</sub> for G<sub>1</sub> and a different set of
M<sub>1</sub> and M<sub>2</sub> for G<sub>2</sub>. This only gets more
complex when *shared dimensions* (aka *Dimensions* from now on) are
added into the mix; however, any solution that solves the problem for
shared coordinate variables (aka Maps) does so for Dimensions if there
is a one-to-one relationship between Maps and Dimensions for any set of
Grids that are subset.

Potential solutions:

##### Add the enclosing Lexical Scope back into the Data Model

We can go back to the old data model where each Grid (G<sub>1</sub> and
G<sub>2</sub>, e.g.) is wrapped in its own scope. Problems with this
solution:

1.  The scope is 'fictitious' in that while it is part of the DAP
    representation for the dataset, it is not present in the original;
2.  The extra scope has semantics associated with it that 'fail' when
    some subsetting operations in DAP2 are applied (e.g., when just the
    'array part' was projected but not the 'maps'), so we'll have to
    address those issues; and
3.  A side effect of this data model is that the storage/transmission
    size increased. I don't feel optimizing for transmission size is as
    important as a flexible data model, but we should obviously not
    transmit excess information.

##### Change the way Grid subsetting works

We can change the way Grid subsetting works. Grids are defined a class
of a relational type - the indices of the Maps and Arrays function like
foreign keys - but that does not mean that we have to return the Maps
with each Grid subset. When a client subsets the two grids
(G<sub>1</sub> and G<sub>2</sub>) it gets back only the Arrays that hold
their data. This introduces some problems/issues:

1.  The 'Grid-ness' of G<sub>1</sub> and G<sub>2</sub> has been lost
2.  Clients must either make a coordinated request for the Maps
    M<sub>1</sub> and M<sub>2</sub> and we must solve the question of
    how to make two or *different* requests for the same variable (cf
    namespace issues) or...
3.  Clients must make two or more requests, one for each Grid and each
    of those must still contain requests for several variables to be
    really useful and ...
4.  How does the client find out about the Dimensions?

##### Restrict the kinds of subsetting allowed

We can limit the ways in which a Grid can be subset. This might be what
Dennis proposes in
[DAP4:_Constraints_and_Shared_Dimensions](DAP4:_Constraints_and_Shared_Dimensions "wikilink")
but I'm not sure. In this scheme, for any given request, a set of Grids
like G<sub>1</sub> and G<sub>2</sub> that share maps could only be
subset using one interval over M<sub>1</sub> and M<sub>2</sub>. If a
client attempts to use two or more intervals, it gets an error. This
introduces some problems, too:

1.  If a client wants to subset of different intervals of the maps, it
    must make several requests
2.  Since clients get back 'consistent' responses, a client might, as
    John says, get back more data than it wants.

### Proposed Solution: Restrict the kinds of subsetting allowed

*I thought a more formal version of this might be useful in ferreting
out any more problems besides the two already mentioned (which are
repeated below)*

Definitions:

Grid: A Grid is an Array variable that has, in addition to it's dimensions which define its rank and extent, a set of maps that provide coordinate space values for the data stored in the Array.
Map: A Map is an Array that uses one or more Dimensions to define a coordinate space and are used by Grids. NB: A plain Array can use Dimensions, but it's not a Map if it is not used by a Grid as such.
Dimension: A Dimension is the binding of a name to a size and is used to define the rank and extent of an Array.

- Grid subsetting can only take place using intervals defined over its
  maps.
- When one or more Grids share maps, any given subsetting request can
  subset one or more of those Grids, but must use the same map interval
  for them all.
- The result of a Grid subsetting operation includes the tuple of
  Dimension(s), Map(s) and Array(s) that make up the subset Grids.
- It is possible to subset the 'Array part' of a Grid and get back just
  the Array.

Problems/issues:

1.  If a client wants to subset of different intervals of the maps, it
    must make several requests
2.  Since clients get back 'consistent' responses, a client might, as
    John says, get back more data than it wants.

[Jimg](User:Jimg "wikilink") 13:57, 6 March 2012 (PST)

## Alternate Proposal

[Dennis](User:dmh "wikilink") 04/12/2012 After further consideration, I
have come to the conclusion that this is non-issue. My belief is that
the result of applying a constraint to a dataset results in a new
dataset and that any constraints on the original dataset need not apply
to the new dataset. This means that if the dimension/map constraints in
the original are violated in the newly derived dataset, then so be it;
the requestor who initiated the derivation is presumed to know what they
are doing.

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")