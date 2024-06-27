## Introduction

It seems like the basic problem that we encounter again and again these
days is that the DAP lacks the explicit semantics to link geo-location
information to data values. If we were to address this issue in the DAP
data model (say as part of DAP4, or a GeoDAP extension/profile) I think
a lot of good could come out of it. I imagine that a lot of people would
be enthusiastic about it.

My basic idea is that we would add to the DAP a location version of each
data type. Thus a *GeoFloat32* would contain the explicit information to
associate that value (whatever the value represents, say temperature)
with a location on the planet. The real meat for us would probably lie
in the more complex structural types like *GeoGrid*, in that subsampling
and other geo-functions could then become part of the native API for the
type. Since older clients wouldn't be asking for DAP4 responses, I
imagine the location information would get pushed in to the Attribute
space for them.

I can see this being implemented as a kind of object design. And one
where the higher level 'Geo' objects are returned only for clients that
ask for them. We might make this a DAP4 thing - that is, return the Geo
objects when a client announces it's DAP4 savvy, or we might make it a
separate thing from DAP4. the latter would open up an avenue for other
things besides Geographic specializations. In either case, the idea is
that the more basic classes would hold the same information but using
attributes. Effectively the server could be implemented so as to return
the Geo classes when

1.  It gets the correct Accept: header from a client
2.  The necessary attributes are present for the given type.

This kind of a design is significantly different from the 'profile'
concept because it has a fallback mode so unsavvy clients can still
access all of the data. We're using attributes to add support for new
operations.

Downside: I think there are issues with units lurking here, but if we
can figure that out we will really have something huge - and without a
magic bullet for units, we still will have an important step forward.

We would probably want to work up a design, and then see how it might
impact the various software components in our library. I think that we
could modify the netcdf and hdf4/5 handlers to recognize conventions
(like CF-1.0) so that they would produce GeoDAP data sets whenever
possible. We might even look to the work with Benno and the semantic
ontologies as an intermediate mechanism for promoting DAP data using new
conventions (CF-2.0 or whatever) into GeoDAP prior to the handlers
getting updated.

This could make server side functions like geogrid() very straight
forward. We might also wish to look at the CE syntax and see if these
types might also allow for extensions to the CE to support geo-related
activities. I think WCS work would be greatly simplified, and we might
be able to add more complex server features such as CRS transformations
in the future. The new types could add new operations - since that's
what new types generally do. The advantage of new operations for new
types is that we get away from the problem of sorting out which
functions are present. Now we have a type system to use that determines
which operations are present.

## Types

### Simple

**Simple Types**: GeoByte, GeoInt16, GeoInt32, GeoInt64, GeoFloat32,
GeoFloat64, GeoString, GeoURL

#### API Methods

All of the simple (aka atomic) DAP types would get the following new
methods in the API:

double GetTime()
Returns the time coordinate associated with the value.

<!-- -->

double GetLatitude()
Returns the latitude coordinate associated with the value.

<!-- -->

double GetLogitude()
Returns the longitude coordinate associated with the value.

<!-- -->

double GetElevation()
Returns the elevation associated with the value. How this is correctly
adapted for oceanographic measurements of depth needs to be worked out.
Possibilities include

- Simple state that negative elevations are always "below sea-level".
- Add another method to the API that indicates the "direction" of
  elevation. Maybe something like *boolean elevationIsUp()*
- Defined it in the CRS

We should be careful to make sure that this is clearly defined in a
single simple manner.

<!-- -->

String getCRS()
Returns the Coordinate Reference system associated with the latitude,
longitude, and elevation values. We should probably specify that this
return a standard CRS name via a known convention/vocabulary, such as
the OGC CRS URN (Geographic information ! Spatial referencing by
coordinates \[OGC 05-103\]) like: ***<urn:ogc:def:crs:EPSG>::4326***

#### Persistent Representation

### Complex

GeoStruct

<!-- -->

GeoSequence

<!-- -->

GeoGrid

### Arrays

How should we handle arrays? It seems there are (at least) the following
cases:

#### Arrays of values at a single location (time series)

#### Arrays of values where each value has it's own location (like a ship track)

## Questions

### GeoArray

Is it important to have all the GeoTypes be defined so that they can
'decay' to DAP4 types? That is, be represented w/o information loss
using the existing DAP4 types and some attributes. This implies that we
cannot have a 'GeoArray' type because it's the array that has attributes
and not the elements, so there would be no place to stick the
Geo-Temporal information for each element. OTOH, the GeoInt16, ...,
types *can* decay to DAP4 types.

NB: We could store the G-T information for elements of a GeoArray in a
vector of attribute values, but that will likely break the intent of
attributes, by making some attributes very large.

Another take on this question is that while GeoArray is a non-starter,
we can have GeoTypes that have arrays themselves, like GeoGrid and that
GeoGrid can likely decay to a DAP4 Grid with some additional attributes.
It probably cannot decay to a DAP2 Grid in the general case, although in
common special cases it can (because in general we'd need Grid to
represent curvalinear coordinate systems but most data sets online now
with DAP don't use those kinds of coordinate systems).

### Modifications to the CE syntax

How will we modify the CE syntax to take advantage of these new types?
Overload the existing operators or introduce new ones?