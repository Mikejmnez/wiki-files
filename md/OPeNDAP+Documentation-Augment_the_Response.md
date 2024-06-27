Back: [AIS Using NcML](AIS_Using_NcML "wikilink")

## Organization of Instances in Memory

Object diagram: <img src="NcML_Object_diagram_1.png"
title="An object diagram showing an example of a DDS, its child BaseType instances and their child AttrTable instances. This is collection of C++ object instances that is represented by the DAP&#39;s DDX response."
width="400"
alt="An object diagram showing an example of a DDS, its child BaseType instances and their child AttrTable instances. This is collection of C++ object instances that is represented by the DAP&#39;s DDX response." />
A [high
resolution](http://docs.opendap.org/images/8/86/NcML_Object_diagram_1.png)
version of the figure to the right.

The object diagram to the right shows the organization in memory of
class instances that corresponds to a fictitious data set. The
[DDS](http://www.opendap.org/api/pref/html/classlibdap_1_1DDS.html)
instance is the root of the data set and contains its name. Each
variable in the data set is represented by an instance of a class that
is a child of
[BaseType](http://www.opendap.org/api/pref/html/classlibdap_1_1BaseType.html).
In the picture, I used
[Array](http://www.opendap.org/api/pref/html/classlibdap_1_1Array.html)
and
[Structure](http://www.opendap.org/api/pref/html/classlibdap_1_1Structure.html)
objects and I left out the child objects of the Array class that
determine the type of the array to emphasize the relationship between
the variables and the attribute tables. Each of these 'type classes' is
a child of the BaseType class and that class includes an
[AttrTable](http://www.opendap.org/api/pref/html/classlibdap_1_1AttrTable.html)
(DAP attribute table) instance. Each AttrTable instance uses zero or
more instances of the
[entry](http://www.opendap.org/api/pref/html/structlibdap_1_1AttrTable_1_1entry.html)
class to hold actual DAP attribute values.

Note: The [online reference documentation for
libdap](http://www.opendap.org/api/pref/html/index.html), which provides
all of these classes.

## Processing the NcML Elements

State diagram:
![](NcML_XML_Parsing_state_diagram_1.png "NcML_XML_Parsing_state_diagram_1.png")
A [high
resolution](http://docs.opendap.org/images/1/1c/NcML_XML_Parsing_state_diagram_1.png)
version of the NcML parsing state diagram.

The diagram to the right shows how the NcML elements are to be
processed. Initially the *<netcdf>* element is processed and the target
data source is read as the vale of the *netcdf@location* attribute. At
the same time the *readMetadta* or *explicit* elements are read and the
DDX fetched. If the *explicit* element is found, the the DAP attributes
are all removed from the DDX's DDS and BaseType instances.

After processing the initial part of the NcML document, a sequence of
*variable* and *attribute* elements are read. Each *variable* element
updated the current variable. The NcML handler can maintain a pointer to
the BaseType instance that corresponds to this variable. When
*attribute* elements are processed they are applied to the 'current'
BaseType instance.

The easiest way to traverse (and modify in the case of the AttrTAble
and/or entry instances) is to use the class's iterator objects. For the
AttrTable class, there are two different interfaces present, one which
is older and uses names to find values and a newer one that uses C++
iterators. Use the iterator-based interface whenever possible.