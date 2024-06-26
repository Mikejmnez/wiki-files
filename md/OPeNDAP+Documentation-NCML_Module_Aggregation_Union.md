# NCML Module: Union Aggregation

The current trunk version of the module supports the union aggregation
element of the form:

    <netcdf>
      <aggregation type="union">
          <!-- some <netcdf> nodes -->
      </aggregation>
    </netcdf>

Note that we do not implement <scan> yet, so all member datasets of the
union must be explicitly listed as a <netcdf> element.

## Functionality

The union aggregation specifies the attributes and variables (and
perhaps dimensions) for the dataset it is contained within (i.e. it's
parent <netcdf> node, which must be be virtual, i.e. have no location
specifed). To do this it:

- Processes each child netcdf element recursively, creating the final
  transformed dataset
- Scans the processed child datasets in order of specification and:
  - Adds to the parent dataset any attribute, variable, or dimension
    that doesn't already exist in the parent dataset
  - Skips any attribute or variable that already exists in the parent
    dataset
  - Skips any dimension already in the parent dataset, unless the
    lengths do not match, in which case it throws a parse error.

Note that the module processes each child dataset entirely as if it were
a top level element, obeying all the normal processing for a dataset,
but collecting the result into that netcdf node. This means that any
child netcdf of an aggregation may refer to a location, have
transformations applied to it, have metadata removed, or may even
contain its own nested aggregation!

Which items will show up in the output? We need to discuss this in a
little more detail, in particular since we have deviated slightly from
the Unidata implementation.

### Order of Element Processing

The NCML Module processes the nodes in a <netcdf> element in the order
encountered. This means that the parent dataset of an aggregation may
place attributes and variables into the union prior to an aggregation
taking place, meaning that those items matching the name in the
aggregation itself will be skipped. It also implies that any changes to
existing metadata within a member of the aggregation by using an
attribute element, for example, must come AFTER the actual aggregation
element, or else a parse error will be thrown.

#### Shadowing an Aggregation Member

For example, the following examples show how to "shadow" a variable
contained in an aggregation by specifying it in the parent dataset prior
to the aggregation:

    <netcdf>

      <variable name="Foo" type="string">
        <values>I come before the aggregation, so will appear in the output!</values>
      </variable>

      <aggregation type="union">

        <netcdf>
          <variable name="Foo" type="string">
        <values>I will be skipped since there's a Foo in the dataset prior to the aggregation.</values>
          </variable>
       </netcdf>

        <netcdf>
          <variable name="Bar" type="string">
        <values>I do not exist prior, so will be in the output!</values>
          </variable>
        </netcdf>

      </aggregation>

    </netcdf>

The values make it clear what the output will be. The variable "Foo" in
the first child will be skipped since the parent dataset already
specified it, but the variable "Bar" in the second child dataset will
show up in the output since it doesn't already exist in either the
parent or the previous child. Note that this would also work on an
attribute or dimension.

#### Modifying the "Winner" of the Union Aggregation

The following example shows how to modify the "winning" variable in a
union aggregation by specifying the attribute change AFTER the
aggregation element:

    <netcdf>
      <aggregation type="union">

        <netcdf>
          <variable name="Foo" type="string">
        <attribute name="Description" type="string" value="Winning Foo before we modify, should NOT be in output!"/>
        <values>I am the winning Foo!</values>
          </variable>
        </netcdf>

        <netcdf>
          <variable name="Foo" type="string">
        <attribute name="Description" type="string" value="I will be the losing Foo and should NOT be in output!"/>
        <values>I am the losing Foo!</values>
          </variable>
        </netcdf>

      </aggregation>

      <!-- Now we modify the "winner" of the previous union -->
      <variable name="Foo">
        <attribute name="Description" type="string" value="I am Foo.Description and have modified the winning Foo and deserve to be in the output!"/>
      </variable>

    </netcdf>

In this case the output dataset will have the variable Foo with a value
of "I am the winning Foo!", but its metadata will have been modified by
the transformation after the aggregation, so its attribute "Description"
will have the value "I am Foo.Description and have modified the winning
Foo and deserve to be in the output!".

If this entire netcdf element were contained within another aggregation,
then other transformations might be applied after the fact as well,
again in the order encountered for clarity.

### Dimensions

Since the DAP2 does not specify dimensions as explicit data items, a
union of dimensions is only done *if the child netcdf elements
explicitly declare dimensions*. In practice, this is mostly of little
utility since the only time dimensions are specified is to create
virtual array variables (Note: we do not load dimensions from wrapped
sets, so effectively they **do not exist** in them, even if the wrapped
dataset was an NcML file!)

If a dimension does exist explicitly in a child dataset and a second
with the same name is encountered in another child dataset, the
cardinalities are checked and a parse error is thrown if they do not
exist. This is a simple check that can be done to ensure the resulting
arrays are of the correct size. Note that even if an array had a named
dimension within a wrapped set, we **do not check** that these match at
this time.

Here is an example of a valid use of dimension in the current module:


    <netcdf ns="http://www.unidata.ucar.edu/namespaces/netcdf/ncml-2.2">

      <!-- Test that a correct union with dimensions in the virtual datasets will work if the dimensions match as they need to -->
      <attribute name="title" type="string" value="Testing union with dimensions"/>

      <aggregation type="union">

        <netcdf>
          <attribute name="Description" type="string" value="The first dataset"/>
          <dimension name="lat" length="5"/>

          <!-- A variable that uses the dimension, this one will be used -->
          <variable name="Grues" type="int" shape="lat">
        <attribute name="Description" type="string">I should be in the output!</attribute>
        <values>1 3 5 3 1</values>
          </variable>

        </netcdf>

        <netcdf>
          <attribute name="Description" type="string" value="The second dataset"/>

          <!-- This dimension will be skipped, but the length matches the previous as required -->
          <dimension name="lat" length="5"/>

          <!-- This dimension is new so will be used... -->
          <dimension name="station" length="3"/>

          <!-- A variable that uses it, this one will NOT be used -->
          <variable name="Grues" type="int" shape="lat">
        <attribute name="Description" type="string">!!!! I should NOT be in the output! !!!!</attribute>
        <values>-3 -5 -7 -3 -1</values>
          </variable>

          <!-- This variable uses both and will show up in output correctly -->
          <variable name="Zorks" type="int" shape="station lat">
        <attribute name="Description" type="string">I should be in the output!</attribute>
        <values>
          1  2   3   4   5
          2  4   6   8  10
          4  8  12 16 20
        </values>
          </variable>

       </netcdf>

      </aggregation>

    </netcdf>

Here is an example that will produce a dimension mismatch parse error:

    <netcdf ns="http://www.unidata.ucar.edu/namespaces/netcdf/ncml-2.2">

      <!-- Test that a union with dimensions in the virtual datasets will ERROR if the child set dimensions DO NOT match as they need to -->
      <attribute name="title" type="string" value="Testing union with dimensions"/>

      <aggregation type="union">

        <netcdf>
          <dimension name="lat" length="5"/>
          <!-- A variable that uses the dimension, this one will be used -->
          <variable name="Grues" type="int" shape="lat">
        <attribute name="Description" type="string">I should be in the output!</attribute>
        <values>1 3 5 3 1</values>
          </variable>
        </netcdf>

        <netcdf>
          <!-- This dimension WOULD be skipped, but does not match the representative and will cause an error on union! -->
          <dimension name="lat" length="6"/>
         <!-- This dimension is new so will be used... -->
          <dimension name="station" length="3"/>
          <!-- A variable that uses it, this one will NOT be used -->
          <variable name="Grues" type="int" shape="lat">
        <attribute name="Description" type="string">!!!! I should NOT be in the output! !!!!</attribute>
        <values>-3 -5 -7 -3 -3 -1</values>
          </variable>

          <!-- This variable uses both and will show up in output correctly -->
          <variable name="Zorks" type="int" shape="station lat">
        <attribute name="Description" type="string">I should be in the output!</attribute>
        <values>
          1  2   3   4   5  6
          2  4   6   8  10  12
          4  8  12 16 20  24
        </values>
          </variable>

       </netcdf>

      </aggregation>

    </netcdf>

Note that the failure is that the second dataset has had an extra "lat"
sample added to it, but the prior dataset has not. Again, these
dimension checks only occur now in a **pure virtual dataset** like we
see here. Using netcdf@location will effectively "hide" all the
dimensions within it at this point.

#### Thoughts About Future Directions for Dimension

For a future implementation, we may want to consider a DAP2 Grid Map
vector as a dimension and do cardinality checks on them if we have
multiple grids in a union each of which specify the same names for their
map vectors. One argument is that this should be done if an explicit
dimension element with the map vector name is specified in the parent
dataset and is explicitly specified as "isShared". Though DAP2 does not
have shared dimensions, this would be a basic first step in the error
checking that will have to be done for shared dimensions.

## Notes About Changes from NcML 2.2 Implementation

In the Aggregation tutorial, it is mentioned that in a given <netcdf>
node, the <aggregation> element is process prior to any other nodes,
which reflects an explicitly DOM implementation of the NcML parser.
Since we are using a SAX parser for efficiency, we cannot follow this
prescription. Instead, we process the elements in the order encountered.
We argue that this approach, while more efficient, also allows for more
explicit control over which attributes and variables show up in the
dataset which is the parent node of the aggregation. The examples above
show this extra power gained by allowing elements to be added to the
resultant dataset prior to or after the aggregation has been processed.
In particular, it will let us shadow potential members of the
aggregation.

[Category:Aggregation](Category:Aggregation "wikilink")
[Category:NCML](Category:NCML "wikilink")