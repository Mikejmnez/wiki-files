## Overview

We have written server functions that utilize Bill Howe's Gridfields
library to subset unstructured grids (aka irregular meshes) that conform
to the UGrid 0.9 specification that is part of the CF-1.6 specification.
Our function has a little latitude in its requirements because it also
supports features found in earlier versions of the UGrid specification
(e.g., the 'mesh array' can be either the Nx3 or 3xN). However, our
function only supports triangular (2D) meshes, not the other mesh
schemes outlined in the specification. The function will subset one or
more *range variables* based on the values of the *domain variables* as
expressed in a simple relational expression. The result of the function
is an unstructured grid - that is, the subsetting operation will return
a a set of variables that could be written to a netcdf file and the
result would conform to the UGrid 0.9 specification. Because the
specification has evolved over time and older data are still perfectly
valid, we often 'tweak' those older datasets using NcML so that the
contain the variables and attributes required by the newer versions of
the UGrid specification. Our function supports both zero- and ones-based
indexing for both the range and domain variables.

## Background information on UGrid 0.9.0

The [UGrid specification](https://github.com/ugrid-conventions) moved to
GitHub in 2013. The 0.9.0 version of the specification was last edited
on 29 Oct 2013. Our function conforms to that specification and to an
older version (0.6?), where the major difference is the organization of
the mesh array data. For a triangular array, the 0.9.0 version specifies
the array be a Nx3 while the older specification specified a 3xN. Our
code supports both.

## The ugrid subsetting functions

These functions allow you to subset the UGrid dataset by the values
associated with nodes, edges, or faces.

### ugnr: Subset by node value.

Name
***ugnr*** - Subset an unstructured 2D Mesh Topology grid based on
domain values associated with the nodes

Syntax
`ugnr(rangeVariable:string, [rangeVariable:string, ... ] condition:string)`

- The first, second, ..., parameter is a list of (*range*) data variable
  names that you wish to subset, they may be associated with nodes,
  edges, or faces of the mesh.
- The last parameter is an expression that defines conditions that the
  *domain* variables must meet in order to be included in the result.

Here is an example:


`ugnr(depth, "28.0<lat & lat<29.0 & -89.0<lon & lon<-88.0")`

Used in a url (that returns the DDS for the subset data):


`[`[`http://52.70.199.67:8080/opendap/hyrax/ugrids/Ike/2D_varied_manning_windstress/test_dir-norename.ncml.dds?ugnr(depth,%2228.0%3Clat&lat%3C29.0&-89.0%3Clon&lon%3C-88.0%22`](http://52.70.199.67:8080/opendap/hyrax/ugrids/Ike/2D_varied_manning_windstress/test_dir-norename.ncml.dds?ugnr(depth,%2228.0%3Clat&lat%3C29.0&-89.0%3Clon&lon%3C-88.0%22)`) `[`http://52.70.199.67:8080/opendap/hyrax/ugrids/Ike/2D_varied_manning_windstress/test_dir-norename.ncml.dds?ugnr(depth`](http://52.70.199.67:8080/opendap/hyrax/ugrids/Ike/2D_varied_manning_windstress/test_dir-norename.ncml.dds?ugnr(depth)`,"28.0<lat & lat<29.0 & -89.0<lon & lon<-88.0")]`

<!-- -->

Subsetting the range variables
In the example dataset
[NECOFS_GOM3_FORECAST.nc](http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc)
([DDS](http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.dds)
[DAS](http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.das))
we can see that there are range variables associated with the nodes of
the mesh that have a *time* dimension and a *siglay* dimension. Since
the *time* dimension has 145 values, and *siglay* dimension has 40
values it may be that you wish to only retrieve a single time slice
and/or siglay slice. This can accomplished by using an array constraint
on the variable as it is passed to the ugrid function. For example, to
retrieve the 12th siglay slice of the 5th time slice of the variable
salinity you would constrain it like *salinity\[4\]\[11\]\[\*\]* (note
that the indices begin at zero). In the request URL the function call is


`[`[`http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.dds?ugnr(salinity%5B4%5D%5B11%5D%5B*%5D,%2238.0%3Clat%20&%20lat%3C42.0%20&%20-64.0%3Clon%20&%20lon%3C-60.0%22`](http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.dds?ugnr(salinity%5B4%5D%5B11%5D%5B*%5D,%2238.0%3Clat%20&%20lat%3C42.0%20&%20-64.0%3Clon%20&%20lon%3C-60.0%22)`) ugnr(salinity[4][11][*],"38.0<lat & lat<42.0 & -64.0<lon & lon<-60.0")]`

It is also possible to request, say, every 5th time slice
*salinity\[0:5:\*\]\[\*\]\[\*\]* or every 3rd siglay slice from 3 to 30
*salinity\[4\]\[3:3:30\]\[\*\]*. In a request URL this would look like


`[`[`http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.dds?ugnr(salinity%5B4%5D%5B3:3:30%5D%5B*%5D,%2238.0%3Clat%20&%20lat%3C42.0%20&%20-64.0%3Clon%20&%20lon%3C-60.0%22`](http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.dds?ugnr(salinity%5B4%5D%5B3:3:30%5D%5B*%5D,%2238.0%3Clat%20&%20lat%3C42.0%20&%20-64.0%3Clon%20&%20lon%3C-60.0%22)`) ugnr(salinity[4][3:3:30][*],"38.0<lat & lat<42.0 & -64.0<lon & lon<-60.0")]`

### uger: Subset by edge value.

Name
***uger*** - Subset an unstructured 2D Mesh Topology grid based on
domain values associated with the edges

Syntax
`uger(rangeVariable:string, [rangeVariable:string, ... ] condition:string)`

- The first, second, ..., parameter is a list of (*range*) data variable
  names that you wish to subset, they may be associated with nodes,
  edges, or faces of the mesh.
- The last parameter is an expression that defines conditions that the
  *domain* variables must meet in order to be included in the result.

Here is an example:


`uger(depth, "28.0<lat & lat<29.0 & -89.0<lon & lon<-88.0")`

### ugfr: Subset by face value.

Name
***ugfr*** - Subset an unstructured 2D Mesh Topology grid based on
domain values associated with the faces

Syntax
`ugfr(rangeVariable:string, [rangeVariable:string, ... ] condition:string)`

- The first, second, ..., parameter is a list of (*range*) data variable
  names that you wish to subset, they may be associated with nodes,
  edges, or faces of the mesh.
- The last parameter is an expression that defines conditions that the
  *domain* variables must meet in order to be included in the result.

Here is an example:


`ugfr(depth, "28.0<lat & lat<29.0 & -89.0<lon & lon<-88.0")`

## Retired Functions

### The ugr5 Function

***This function is deprecated.** Supported by Hyrax 1.12.2 and
earlier.*

Name
ugr5 - Restrict the domain of an unstructured 2D Mesh Topology grid

<!-- -->

Syntax
ugr5(0, rangeVariable:string, \[rangeVariable:string, ... \]
condition:string)

- The first parameter indicates that the supplied relational constraint
  condition is to be applied to coordinate values at the nodes (0) or
  the faces (2) of the mesh.
- The second, third, ..., parameter is a list of (*range*) data
  variables that you wish to subset, they may be associated with nodes
  or faces of the mesh.
- The last parameter is an expression that defines conditions that the
  *domain* variables must meet in order to be included in the result.

Here is an example:


ugr5(0, depth, "28.0\<lat & lat\<29.0 & -89.0\<lon & lon\<-88.0")

Used in a url (that returns the DDS for the subset data):


\[<http://52.70.199.67:8080/opendap/hyrax/ugrids/Ike/2D_varied_manning_windstress/test_dir-norename.ncml.dds?ugr5(0,depth,%2228.0%3Clat&lat%3C29.0&-89.0%3Clon&lon%3C-88.0%22>)
<http://52.70.199.67:8080/opendap/hyrax/ugrids/Ike/2D_varied_manning_windstress/test_dir-norename.ncml.dds?ugr5(0,depth>,"28.0\<lat
& lat\<29.0 & -89.0\<lon & lon\<-88.0")\]

<!-- -->

Subsetting the range variables
In the example dataset
[NECOFS_GOM3_FORECAST.nc](http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc)
([DDS](http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.dds)
[DAS](http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.das))
we can see that there are range variables associated with the nodes of
the mesh that have a *time* dimension and a *siglay* dimension. Since
the *time* dimension has 145 values, and *siglay* dimension has 40
values it may be that you wish to only retrieve a single time slice
and/or siglay slice. This can accomplished by using an array constraint
on the variable as it is passed to the ugrid function. For example, to
retrieve the 12th siglay slice of the 5th time slice of the variable
salinity you would constrain it like *salinity\[4\]\[11\]\[\*\]* (note
that the indices begin at zero). In the request URL the function call is


\[<http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.dds?ugr5(0,salinity%5B4%5D%5B11%5D%5B*%5D,%2238.0%3Clat%20&%20lat%3C42.0%20&%20-64.0%3Clon%20&%20lon%3C-60.0%22>)
ugr5(0,salinity\[4\]\[11\]\[\*\],"38.0\<lat & lat\<42.0 & -64.0\<lon &
lon\<-60.0")\]

It is also possible to request, say, every 5th time slice
*salinity\[0:5:\*\]\[\*\]\[\*\]* or every 3rd siglay slice from 3 to 30
*salinity\[4\]\[3:3:30\]\[\*\]*. In a request URL this would look like


\[<http://52.70.199.67:8080/opendap/hyrax/ugrids/NECOFS_GOM3_FORECAST.nc.dds?ugr5(0,salinity%5B4%5D%5B3:3:30%5D%5B*%5D,%2238.0%3Clat%20&%20lat%3C42.0%20&%20-64.0%3Clon%20&%20lon%3C-60.0%22>)
ugr5(0,salinity\[4\]\[3:3:30\]\[\*\],"38.0\<lat & lat\<42.0 & -64.0\<lon
& lon\<-60.0")\]

## Sample Data

A server with unstructured grid data and subsetting function on board is
here: <http://52.70.199.67:8080/opendap/>

In the directory [ugrids](http://52.70.199.67:8080/opendap/ugrids/) You
can find a number of test datasets:

fvcom_1step.nc, hex.nc
These are test data from Bill Howe. The *hex* file is small but it lacks
the special variable the UGrid uses to encode metadata the function
depends on to read the unstructured data.

test4.nc, test4-rename-nodedata.ncml, test4-ugrid.ncml
*test4* also lacks the special 'metadata variable' but we have written
NcML (*test4-ugrid*) that adds it to the dataset, so you can use ugr5()
with a URL that points at the virtual dataset that NcML file defines.
The companion NcML file (*test4-rename-nodedata.ncml*) uses NcML to
rename the node data variable and while this works fine for normal DAP
accesses, the ugr5() won't work with this - the purpose of this example
is to document a known bug where using NcML to rename a range variable
breaks the function.

NECOFS_GOM3_FORECAST.nc, NECOFS_WAVE_FORECAST.nc
These files us a 3xN face node connectivity array (corresponding to the
older version of the UGrid specification)

Ike/2D_varied_manning_windstress and Ike/2D_varied_manning_windstress_wave
These directories have data from c. 2008 that requires (minimal) NcML to
work with our function

RENCI
Data used by the ODSIP EarthCube project. This directory has the same
data (or nearly so) where the face node connectivity array is organized
a Nx3 and 3xN.

lana
This is a bit of a mystery; it may have been lumped into the ugrid
sample data by mistake.

README
A brief recap of this information