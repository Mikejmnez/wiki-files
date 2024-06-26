# Union Aggregation -- Explicit Netcdf Elements

Parent Design Page:
[BES_Aggregation_using_NcML](BES_Aggregation_using_NcML "wikilink")

This page describes the design for the "union" type of aggregation for
the case where the <netcdf> elements are listed explicitly (as opposed
to with a <scan> element).

## Example Test Cases

We need examples of proper union that we know the answer to, as well as
examples that we require to fail.

### Basic Acceptance Example

This example file is clearly contrived but will demonstrate the basic
required functionality for this portion of the design:


    <?xml version="1.0" encoding="UTF-8"?>
    <!-- An example of a union aggregation fully specified in this file -->
    <netcdf>

      <!-- Can specify attributes outside of aggregation -->
      <attribute name="Dataset_Description" type="string" value="Virtual union aggregation example"/>

      <!-- Can specify variables at this level still, outside aggregation -->
      <variable name="SelfReferentialVariable" type="string">
        <values>My name is "SelfReferentialVariable"</values>
      </variable>

      <aggregation type="union">

        <!-- Dataset One -->
        <netcdf>
          <!-- This one should "win" since it comes first... -->
          <attribute name="Description" type="string" value="Dataset One"/>
          <variable name="Foo" type="string">
        <values>Foo</values>
          </variable>
        </netcdf>

        <!-- Dataset Two -->
        <netcdf>
          <!-- This one should "lose" since it comes later... -->
          <attribute name="Description" type="string" value="Dataset Two"/>

          <!-- This one should "lose" since it comes later... -->
          <variable name="Foo" type="string">
        <values>Foo2</values>
          </variable>

          <!-- But this one will be added -->
          <variable name="Bar" type="string">
        <values>Bar</values>
          </variable>
       </netcdf>

        <!-- Dataset Three -->
        <netcdf>
          <!-- This one should "lose" since it comes later... -->
          <attribute name="Description" type="string" value="Dataset Three"/>

         <!-- But this one will be added -->
          <variable name="Baz" type="string">
        <values>Baz</values>
          </variable>
        </netcdf>

      </aggregation>

      <!-- TODO Theoretically we should be able to apply NcML transformations to elements in the aggregation above, according to the tutorial.
           I'd like to see an example of this... Since we disallow changing variable values but only attributes, presumably an author
           could change an attribute known to be in the aggregation?  -->

    </netcdf>

### Rejection Examples

*TODO* We also need to create several examples of REJECTION cases in
order to elicit the parse exceptions listed below.

### Nested Examples

In order to validate the changes listed below, we also need an example
of a properly nested union, which contains an aggregation containing a
netcdf element (or more) that themselves contain nested aggregations. We
need to make sure these work properly.

## New Components

The parser design needs to change somewhat to handle aggregation. These
changes are:

- We need a new class AggregationElement : public NCMLElement with:
  - Parent pointer to containing NetCDFElement (which will also contain
    an owning ptr to this AggregationElement)
  - vector\<NetCdfElement\*\> to contain the ordered list of datasets
    which we need to process the union on.
    - Should ref() contained NetcdfElement's as they are added to the
      vector.
    - Should deref() and clear out the vector of NetcdfElement in dtor
      which should, in correct ref counts, destruct the netcdf.

<!-- -->

- It will need to be added to the NCMLElement::Factory

On handleBegin() it will:

1.  Make sure there is no other aggregation element set in the current
    NetcdfElement scope (There can be only one!)
2.  Validate that its type=="union" or exception
3.  Make sure dimName is unspecified or exception (invalid for union
    agg).
4.  Add itself to the current scope NetcdfElement, making sure it gets
    ref() counted.

On handleContent() it will:

1.  Exception UNLESS the entire content is only whitespace since it's
    invalid to specify content for an aggregation.

On handleEnd() it will:

1.  Process the union by forward iterating down the
    vector\<NetcdfElement\*\> and for each NetcdfElement\*
    1.  For each attribute in the dataset (DDS) attribute table:
        1.  If it doesn't exist in the AggregationElement-\>getParent()
            dataset attribute table, add it to this table, else skip.
    2.  For each variable in the DDS of the NetcdfElement:
        1.  If a variable doesn't exist in the
            AggregationElement-\>getParent() DDS, then add it, else
            skip.

If we expect that the AggregationElement was NOT ref()'d by the netcdf,
then NCMLParser will deref() it after handleEnd() which will cause it's
dtor to be invoked, dereffing all the elements of the dataset vector and
clearing it out.

### Exceptions

AggregationElement **must** throw a BESSyntaxUserError (Parse error):

- if any unhandled attributes are specified so the user knows we cannot
  handle them yet
- if the aggregation type is anything other than "union"
- if a <scan> element is specified within the aggregation
- if a second aggregation element is specified in the currently parsed
  DDS

## Changes to Existing Components

### NetcdfElement

- Must now contain its DDS rather than having a single DDS (actually, a
  BEPDapResponse containing either a DDS or DataDDS) in the NCMLParser
- Will contain a single ptr (possibly NULL) to its contained
  AggregationElement. NULL implies no aggregation at current point in
  parse.
- Will need to know if we are doing a data parse or just ddx (das and
  dds are subsets of this).

### NCMLParser

- Can no longer contain a direct pointer to its BESDapResponse and
  contained DDS (or DataDDS)
- Instead will contain a single owning pointer to the top level <netcdf>
  element, with its newly contained BESDapRessponse being the
  "top-level" response of the parse call.
- Can no longer delete NCMLElement's as it pops them off the
  ElementStack after calling handleEnd(). Instead, it will need to:
  - ref() some reference counting mechanism on NCMLElement when it
    pushes it onto the ElementStack.
  - deref() some reference counting mechanism when it pops them off the
    ElementStack after handleEnd() is called
- Needs to parse <netcdf> elements correctly for nested cases:
  essentially its current scope BECOMES the nested <netcdf> element when
  it's encountered, BUT THE ROOT NETCDF ELEMENT IS NOT TOUCHED. We need
  a way to keep these notions separate.
- Need a way to get at the "current aggregation element" which would be
  the AggregationElement member of the CURRENTLY BEING PARSED NETCDF.
  This needs to work on nesting as well.

Since we will want to destroy the root NetcdfElement after the parse
call, but return it's BESDapResponse without its dtor killing it, we
need to make sure we use an auto_ptr or soemthing for the BESDapResponse
so if we return it it's null'ed and not deleted, but if we exception or
if it's not released() it gets destroyed (which will be the case for the
NetcdfElement's that live inside the AggregationElement vector).

### NCMLElement

As shown above, we need to add some reference counting or equivalent
smart pointer system on NCMLElement in order to allow for the two cases:

- Some elements are meant to be destroyed immediately when they are
  closed and popped off the NCMLParser ElementStack.
- Some elements are contained within other elements, in particular
  NetcdfElement's are contained in AggregationElement, and need to
  remain until the aggregation operation has been completed.

We need to decide between :

- Using some sort of template smart pointer wrapper for NCMLElement that
  adds strong ref counting and smart deletion when the strong reference
  count hits 0.
- Incorporating the ref counting into NCMLElement or a superclass of it,
  like OpenInventor used to do.