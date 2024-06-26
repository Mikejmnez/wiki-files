## General Questions and Assumptions

- What version of netCDF will this support?

[jimg](User:Jimg "wikilink") 11:39, 21 December 2008 (PST) Initially we
should support netCDF version 3

- Should I traverse the data structure to see if there are any
  sequences?

[jimg](User:Jimg "wikilink") 17:53, 18 December 2008 (PST) Yes. An
initial version should note their presence and add an attribute noting
that they have been elided.

## How to flatten hierarchical types

For a structure such as

`Structure {`
`    Int x;`
`    Int y;`
`} Point;`

represent that as

`Point.x`
`Point.y`

Explicitly including the dot seems ugly and like a kludge and so on, but
it means that the new variable name can be feed back into the server to
get the data. That is, a client can look at the name of the variable and
figure out how to ask for it again without knowing anything about the
translation process.

Because this is hardly a lossless scheme (a variable might have a dot in
its name...), we should also add an attribute that contains the original
real name of the variable - information that this is the result of a
flattening operation, that the parent variable was a Structure, Sequence
or Grid and its name was *xyz*. Given that, it should be easy to sort
out how to make a future request for the data in the translated
variable.

This in some way obviates the need for the dot, but I think we should
use that regardless.

### Attributes of flattened types/variables

If the structure *Point* has attributes, those should be copied to both
the new variables (*Point.x* and *Point.y*). It's redundant but this way
the recipient of the file gets all of the information held in the
original data source. [jimg](User:Jimg "wikilink") 10:14, 6 January 2009
(PST) Added based on email from Patrick.

The name of the attributes should be Point.name for any attributes of
the structure Point, and just the name of the attribute for the
variables x and y. So, if x has attributes a1 and a2 and Point has
attributes a1 and a3 then the new variable Point.x will have attributes
a1, a2, Point,a1 and Point.a3.

## Extra data to be included

For a file format like netCDF it is possible to include data about the
source data using it's original data model as expressed using DAP. We
could then describe where each variable in the file came from. This
would be a good thing if we can do it in a light-weight way. I think it
would also be a good thing to add an attribute to each variable that
names where in the original data it came from so that client apps &
users don't have to work too hard to sort out what has been changed to
make the file.

## Information About Specific Types

### Strings

- Add dimension representing the max length of the string with name
  varname_len.
- For scalar there will be a dimension for the length and the value
  written using nc_put_vara_text with type NC_CHAR
- For arrays add an additional dimension for the max length and the
  value written using nc_put_vara_text with type NC_CHAR

[pwest](User:Pwest "wikilink") 14:31, 7 January 2008 (MST) Received
message from Russ Rew

Yes, that's fine and follows a loose convention for names of
string-length dimensions for netCDF-3 character arrays.

For netCDF-4 of course, no string-length dimension is needed, as strings
are supported as a netCDF data type.

### Structures

- Flatten
- prepend name of structure with a dot followed by the variable name.
  Keep track as there might be embedded structures, grids, etc...

[jimg](User:Jimg "wikilink") 17:53, 18 December 2008 (PST) Use the
procedure described above in *How to flatten hierarchical types*.

[jimg](User:Jimg "wikilink") 17:53, 18 December 2008 (PST) I would use a
dot even though I know that dots in variable names are, in general, a
bad idea. If we use underscores then it maybe hard for clients to form a
name that can be used to access values from a server based on the
information in the file.

### Grid

- Flatten.
- Use the name of the grid for the array of values
- prepend the name of the grid plus a dot to the names of each of the
  map vectors. [jimg](User:Jimg "wikilink") 11:31, 21 December 2008
  (PST) A more sophisticated version might look at the values of two or
  more grids that use the same names and have the same type (e.g.,
  Float64 lon\[360\]) and if they are the same, make them shared
  dimensions.

Please read:

- [netcdf guide on
  components](https://www.unidata.ucar.edu/software/netcdf/guidec/guidec-7.html)
- [DAP 2 spec](http://opendap.org/pdf/ESE-RFC-004v1.1.pdf) on Grids and
  look for naming conventions

The idea here is that each of the map vectors will become an array with
one dimension, the name of the dimension the same as the name of the
variable (be careful about nested maps, see flatten). Then the grid
array of values uses the same dimensions as those used in the map
variables.

If there are multiple grids then they either use the same map variables
and dimensions or they use different variables with different
dimensions. In other words, if one grid has a map called x with
dimension x, and another grid has a map called x then it better be the
same variable with the same dimension and values. If not, it's an error,
it should be using a map called y that gets written out as variable y
with dimension y.

1.  Read the dap spec on grids and see if this is the convention.
2.  Read the netcdf component guide (section 2.2.1 and 2.3.1)

Example files:

- coads_climatology.nc (4 grids, same maps and dimensions)

Example:

`Dataset {`
`    Grid {`
`      Array:`
`        Float32 X[TIME = 12][COADSY = 90][COADSX = 180];`
`      Maps:`
`        Float64 TIME[TIME = 12];`
`        Float64 COADSY[COADSY = 90];`
`        Float64 COADSX[COADSX = 180];`
`    } X;`
`    Grid {`
`      Array:`
`        Float32 Y[TIME = 12][COADSY = 90][COADSX = 180];`
`      Maps:`
`        Float64 TIME[TIME = 12];`
`        Float64 COADSY[COADSY = 90];`
`        Float64 COADSX[COADSX = 180];`
`    } Y;`
`    Grid {`
`      Array:`
`        Float32 Z[TIME = 14][COADSY = 75][COADSX = 75];`
`      Maps:`
`        Float64 TIME[TIME = 14];`
`        Float64 COADSY[COADSY = 75];`
`        Float64 COADSX[COADSX = 75];`
`    } Z;`
`    Grid {`
`      Array:`
`        Float32 T[TIME = 14][COADSY = 75][COADSX = 90];`
`      Maps:`
`        Float64 TIME[TIME = 14];`
`        Float64 COADSY[COADSY = 75];`
`        Float64 COADSX[COADSX = 90];`
`    } T;`
`} coads_climatology.nc;`

### Array

- write_array appears to be working just fine.
- If array of complex types?

[pwest](User:Pwest "wikilink") 16:43, 8 January 2008 (MST) - DAP allows
for the array dimensions to not have names, but NetCDF does not allow
this. If the dimension name is empty then create the dimension name
using the name of the variable + "_dim" + dim_num. So, for example, if
array a has three dimensions, and none have names, then the names will
be a_dim1, a_dim2, a_dim3.

### Sequences

- For now throw an exception [jimg](User:Jimg "wikilink") 11:31, 21
  December 2008 (PST) Initial version should elide these I think because
  there are important cases where they appear as part of a dataset but
  not the main part. We can represent these as arrays easily in the
  future.

[jimg](User:Jimg "wikilink") 11:39, 21 December 2008 (PST) To translate
a Sequence, there are several cases to consider:

1.  A Sequence of simple types only (which means a one-level sequence):
    translate to a set of arrays using a name-prefix flattening scheme.
2.  A nested sequence (otherwise with only simple types) should first be
    flattened to a one level sequence and then that should be flattened.
3.  A Sequence with a Structure or Grid should be flattened by
    recursively applying the flattening logic to the components.

### Attributes

- Global Attributes?
  - For single container DDS (no embedded structure) just write out the
    global attributes to the netcdf file
  - For multi-container DDS (multiple files each in an embedded
    Structure), take the global attributes from each of the containers
    and add them as global attributes to the target netcdf file. If the
    value already exists for the attribute then discard the value. If
    not then add the value to the attribute as attributes can have
    multiple values.
- Variable Attributes
  - This is the way attributes should be stored in the DAS. In the entry
    class/structure there is a vector of strings. Each of these strings
    should contain one value for the attribute. If the attribute is a
    list of 10 int values then there will be 10 strings in the vector,
    each string representing one of the int values for the attribute.
  - What about attributes for structures? Should these attributes be
    created for each of the variables in the structure? So, if there is
    a structure Point with variables x and y then the attributes for a
    will be attributes for Point.x and Point.y? Or are there attributes
    for each of the variables in the structure? Or both.
    [jimg](User:Jimg "wikilink") 10:13, 6 January 2009 (PST) See above
    under the information about hierarchical types.
  - For multi-dimensional datasets there will be a structure for each
    container, and each of these containers will have global attributes.
    [jimg](User:Jimg "wikilink") 10:13, 6 January 2009 (PST) I don't
    understand this statement.
  - Attribute containers should be treated just as structures. The
    attributes will be flattened with dot separation of the names. For
    example, if there is an attribute a that is a container of
    attributes with attributes b and c then we will create an attribute
    a.b and a.c for that variable.
  - Attributes with multiple string values will be handled like so. The
    individual values will be put together with a newline character at
    the end of each, making one single value.

### Added attributes

[pwest](User:Pwest "wikilink") 14 January, 2009 - This feature will not
be added as part of 1.5, but a future release.

After doing some kind of translation, whether with constraints,
aggregation, file out, whatever, we need to add information to the
resulting data product telling how we came about this result. Version of
the software, version of the translation (file out), version of the
aggregation engine, whatever. How do we do that?

The ideas might be not to have all of this information in, say, the
GLOBAL attributes section of the data product, or in the attributes of
the opendap data product (DDX, DataDDX, whatever) but instead a URI
pointing to this information. Perhaps this information is stored at
OPeNDAP, provenance information for the different software components.
Perhaps the provenance information for this data product is stored
locally, referenced in the data product, and this provenance information
references software component provenance.

<http://www.opendap.org/provenance?id=xxxxxx>

might be something referenced in the local provenance. The local
provenance would keep track of:

- containers used to generate the data product
- constraints (server side functions, projections, etc...)
- aggregation handler and command
- data product requested
- software component versions

Peter Fox mentions that we need to be careful of this sort of thing
(storing provenance information locally) as this was tried with log
information. Referencing this kind of information is dangerous.

## Support for CF

If we can recognize and support files that contain CF-compliant
information, we should strive to make sure that the resulting netCDF
files built by this module from those files are also CF compliant. This
will have a number of benefits, most of which are likely unknown right
now because acceptance of CF is not complete. But one example is that
ArcGIS understands CF, so that means that returning a netCDF file that
follows CF provides a way to get information from our servers directly
into this application without any modification to the app itself.

Here's a link to information about CF: [Grid
Mappings](http://cf-pcmdi.llnl.gov/documents/cf-conventions/1.4/cf-conventions.html#appendix-grid-mappings).

[BES File Out NetCDF](Category:Development "wikilink") [BES File Out
NetCDF](Category:Hyrax_Development "wikilink")