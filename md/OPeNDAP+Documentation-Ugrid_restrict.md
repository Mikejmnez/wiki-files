ugrid_restrict is a function creates a grid object out of an input grid
according to to an input restriction constraint. At this time the input
grid is assumed to be an unstructured grid made up of counter-clockwise
oriented triangles.

# Input Arguments

Calls to the function take the form **ugrid_restrict(int dim,string
constraint)**. The first input must be an integer that denotes the
dimension which will be restricted. For example, setting dim to be 0
would indicate the restriction condition applies to the data bound to
the nodes (the \> 0-cells).The second input is a string which describes
the constraint that should be enforced.

# Examples

ugrid_restrict(0,"elevation\>0.0")

ugrid_restrict(0,"X\>-68.0&X\<0&Y\>41.5&Y\<42.78&elevation\>0.0&elevation\<500")

The first call is pretty basic. This call is restricting the mesh to the
nodes such that elevation (some array bound to the nodes) is greater
than 0. You can also have much more complicated constraints: The second
call actually combines 6 constraints.

# Requirements for the Input File

Following the UGRID standard being developed by the community, \> every
array every array must have an attribute known as grid_location that can
take two possible values. If grid_location="element" this indicates that
data is associated with the triangular cell. Otherwise if
grid_location="node" the data is associated with the nodes of the grid.

In addition, the array containing the connectivity information of the
triangular grid must be an n by 3 array that has an attribute cell_type
which is equal to "tri_ccw" which indicates the cells are counter
clockwise oriented triangles. In addition, the connectivity array has an
index_origin attribute which must be an integer that gives the value
associated with the first cell (usually 1). Finally it must have an
attribute coordinates_node that gives the coordinates that will be used
for the nodes (for example "X Y").

More about UGRID can be found on the google group:
[UGRID](https://groups.google.com/forum/#!forum/ugrid-interoperability)

An example file is found
\[<http://ec2-174-129-186-110.compute-1.amazonaws.com:8088/opendap/data/nc/test4.nc.nc>?
here\]

# Demo

A demo that allows easy experimentation with this function can be found
[here](http://ec2-107-22-36-153.compute-1.amazonaws.com/graph) The
dataset for this demo is a restricted version of the usgs dataset found
[here](http://geoport.whoi.edu/thredds/catalog/usgs/data1/rsignell/models/adcirc/catalog.html?dataset=usgs/data1/rsignell/models/adcirc/fort.64.nc)

# Possible URLs

Possible URLs -\> Example OPeNDAP URLs You can paste these into your
browser to download the data.

ec2-174-129-186-110.compute-1.amazonaws.com:8088/opendap/data/nc/test4.nc.nc?ugrid_restrict(0,"Y\>41.5&Y\<42.75&X\>-68.0&X\<-66.0")

ec2-174-129-186-110.compute-1.amazonaws.com:8088/opendap/data/nc/test4.nc.nc?ugrid_restrict(0,"nodedata\>0.0&nodedata\<200")

# Getting GridFields

The google code site is found
[here](http://code.google.com/p/gridfields/)

To checkout the C code necessary to run this function use the command

svn co <https://gridfields.googlecode.com/svn/tags/gridfieldsclib-0.7/>