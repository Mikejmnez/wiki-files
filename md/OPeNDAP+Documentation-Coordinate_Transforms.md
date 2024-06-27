## We need Coordinate Systems

I think that the next really important feature of Scientific Data
Format/Model design is adding good API support for geo-referencing
coordinate systems. Arguably, HDF, netCDF, and OpenDAP are the
best/widely used general purpose scientific data format/access
protocols. All have made some steps towards specifying coordinate
systems (netCDF coordinate variables, HDF dimension scales, OpenDAP Grid
map variables) but I think none have done a complete job.

Most well-written datasets specify the coordinate systems in the
dataset, but without explicit library support, they do so in different
ways, often documented by a
[Convention](http://www.unidata.ucar.edu/packages/netcdf/conventions.html).
We are working closely with the HDF5 developers, who are interested in
extending dimensions scales, and we are considering extensions of the
netCDF model to comprise netCDF-4. With OpenDAP 4.0 under consideration,
it seems like a unique and fortuitous time for all 3 data models to
consider new features in ways that are compatible with each other.

I have done some prototyping of the functionality I think we need for
coordinate systems in the netcdf-Java library, see the user manual,
chapter
[3.2](http://www.unidata.ucar.edu/packages/netcdf-java/v2.1/NetcdfJavaUserManual.htm#_Toc42914935)
for some of the details, and implemented the prototype in the
CoordinateSystem, CoordinateAxis, and CoordinateTransform classes of the
ucar.nc2.dataset package (see the
[javadoc](http://www.unidata.ucar.edu/packages/netcdf-java/v2.1/javadoc/index.html)).

To summarize some definitions:

1.  A <b>general coordinate system</b> assigns to each value in a data
    field a list of coordinate values.
2.  A <b>georeferencing coordinate system</b> assigns space and time
    coordinates.
3.  A <b>coordinate transform</b> is a function from one coordinate
    system to another.

Some of the things we may need that are not currenly expressible by the
Grid datatype:

1.  Multidimensional coordinate axes are needed, eg to assign lat/lon
    coordinates to model output on a projection.
2.  There can be multiple coordinate systems for a single variable,
    e.g., specify both lat/lon and x,y coordinates on the projection
    plane.
3.  "Station Data" is a collection of point data that you may want to
    geo-reference, that are not in a Grid.
4.  Expressing coordinate transformation can be done with attribute
    conventions, but can get complicated.
5.  As coordinate systems and transforms get more complicated, its
    compelling to factor them out into their own structure, since
    typically they are used by many or most of the fields in a dataset.

There is a good argument whether HDF/netCDF/OpenDAP should remain
concerned only with the syntax of datasets, and build layers on top to
handle the semantics. I think the layered approach is correct, but I
think each of those libraries should ship with a standard API for
handling semantics. This will then encourage dataset providers and
server writers to write the datasets in ways that get correctly
interpreted by the semantic handler(s). Further, to make dataset entries
in DL/GCMD-type search facilities useful, we need at least spatial and
temporal bounding boxes, and viewers need full coordinate systems to
correctly visualize the data. This is why I think a standard way of
specifying geo-referencing coordinates systems is <b>the</b> most
important thing we could add to our Data Models and APIs.

But I am aware that this level of semantics may seem out of scope for
OpenDAP, so I wont generate any detailed proposals unless it seems
useful to others to explore it more. John Caron - 13 Oct 2003