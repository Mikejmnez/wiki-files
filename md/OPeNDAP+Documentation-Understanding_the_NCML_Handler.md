The NCML handler has 53 classes. The classes fall into four broad
categories:

Utilities: Used to perform various routine tasks (namespace *agg_util*)
BES Framework: Used to instantiate the module and provide interface hooks for the BES DAP module (namespace *ncml_module*)
NCML Parser: A SAX2 parser that uses the NCML text to build one or more DAP objects (DDS, DataDDS, etc.) (namespace *ncml_module*)
AIS code: Classes that add, modify or remove variables and attributes. (namespace *ncml_module*)
Aggregation code: Classes that build *Join New* and *Join Existing* aggregations (namespace *ncml_module*)

The following sections list the classes/files that fall into these five
categories along with some notes about those classes where appropriate.

In the listing of classes, indentation is used to show parent-child
relationships within the module. For classes that inherit from BES
framework or libdap classes, *child*: public *parent* is used.

## Utilities

1.  RCobject - A reference counted object class. This is used to add
    reference counting to the DDS, etc., objects the handler makes. This
    could possibly be refactored out of the code in favor of
    shared_ptr\<\> if we adopt C++-x11. This class also seems to have
    support for a memory pool, but comments imply that it's never been
    implemented.
2.  RCObjectInterface - An interface for the RCObject class.
3.  NCMLUtil
4.  AggregationUtil
5.  DirectoryUtil
6.  NCMLDebug.h - Macros
7.  MyBaseTypeFactory - Used by VariableElement and AggregationElement
8.  NCMLBaseArray: public libdap::Array - An abstract class
    1.  NCMLArray<T> - This is only used in MyBaseTypeFactory
9.  DDSAccessInterface - Interface class for any object that can
    contains a DDS
10. DDSLoader - Helper class for temporarily hijacking an existing dhi
    to load a DDX response for one particular file.

## BES Framework

1.  NCMLModule: public BESAbstractModule
2.  NCMLRequestHandler: public BESRequestHandler

## NCML Parser

The classes the make up the core of the SAX2 parser are:

1.  SaxParser
    1.  SimpleLocationParser
    2.  OtherXMLParser
2.  SimpleTimeParser - Odd that this is not a child of SaxParser...
3.  XMLHelper
4.  SaxParserWrapper

Those classes are augmented by the *Element* classes that are used to
build the in-memory objects the NCML needs to carry out the AIS or
Aggregation tasks specified by a given NCML file. They are:

1.  NCMLElement
    1.  AggregationElement
    2.  AttributeElement
    3.  DimensionElement
    4.  ExplicitElement
    5.  NetcdfElement
    6.  ReadMetadataElement
    7.  RemoveElement
    8.  ValuesElement
    9.  ScanElement
    10. VariableElement
    11. VariableAggElement

## AIS Code

1.  RenamedArrayWrapper: public libdap::Array

## Aggregation code

1.  AggregationException: public std::runtime_error
2.  Dimension - Struct for holding information about a dimension of
    data, minimally a name and a cardinality (size)
3.  AggMemberDataset: public RCObject
    1.  AggMemberDatasetWithDimensionCacheBase
        1.  AggMemberDatasetDDSWrapper
        2.  AggMemberDatasetSharedDDSWrapper
        3.  AggMemberDatasetUsingLocationRef
4.  ArrayAggregationBase : public libdap::Array
    1.  ArrayAggregateOnOuterDimension
    2.  ArrayJoinExistingAggregation
5.  GridAggregationBase : public libdap::Grid
    1.  GridAggregateOnOuterDimension
    2.  GridJoinExistingAggregation

## How it works

This is a narrative description of how the handler works. With 49
classes (well, 49 header files), it's pretty complicated. This is an
abridged explanation!

### Building DDS and DataDDS objects using NCML

To build a DDS response from a NCML file, the handler runs
NCMLRequestHandler::ncml_build_dds. The ncml_build_dds() method first
parses the NCML text and returns a BESDapResponse object that holds
either a BESDDSResponse that in turn holds a DDS. The actual parse
operation is done by SaxParserWrapper::parse() once the SaxParserWrapper
class is instantiated using an instance of NCMLParser. The NCMLParser
object provides the SaxParserWrapper with a response object, response
type and filename. The SaxParserWrapper::parse() takes the filename of
the NCML to parse (which is redundant since the SaxParserWrapper
instance was initializes using the NCMLParser object which has the
filename in it as a field. The parse() method returns the BesDapResponse
object (a BESDDSResponse that holds a DDS). *I don't know why it returns
a BesDapResponse with a ... instead of just returning a DDS.*

Once the handler has the DDS in the BESDDSResponse in the
BesDapResponse, it copies the variables and attributes from it to the
BESDDSResponse that was passed into the
NCMLRequestHandler::ncml_build_dds() method via its DHI
BESDataHandlerInterface parameter. It then sets the constraint, file
name and dataset name and returns, with the DHI all set for use in the
rest of the BES pipeline.

Oddly, the Data code seems to do just about the same thing, but a bit
more simply, leaving out the copy step.

The handler returns a DSS/DataDDS with variables that match the
'commands' in the NCML file. In the case of NCML that specifies AIS
operations only, the variables are 'normal' with the possible exception
that a renamed variable uses a special set of classes the wrap the
original variable providing a new name so that the client will both see
that new name and be ale to send the server a CE using that name. The CE
parser will work using the new name, but the variable's read() method
will have access to the *old name* when it tries to read data from the
file, etc. This is critical since the NetCDF, HDF4, etc., API has no
notion that the name is 'different' and must be given the variable's
real name.

For aggregations the handler's operation is effectively the same except
that the variables in the DDS are actually made up of many variables.

### How variables are renamed

The renaming of variables is handled by the construction of runtime
objects in the class
[`VariableElement`](https://github.com/OPENDAP/ncml_module/blob/master/VariableAggElement.cc).
As the NcML parser encounters variable elements in the NcML document,
the
[`VariableElement`](https://github.com/OPENDAP/ncml_module/blob/master/VariableAggElement.cc)
is instantiated and the `ncml_module::VariableElement::processBegin()`
method is called in which the presence of the attribute *orgName* is
detected. If *orgName* is present, the method
`ncml_module::VariableElement::processRenameVariable()` is called. This
method is, I think, is a problem.

In `ncml_module::VariableElement::processRenameVariable()`:

If the request is NOT "data" request, then:

- It makes a copy of the libdap variable using
  `libdap::BaseType::ptr_duplicate()`.
- It changes the name of the newly minted copy.
- It deletes the original variable from the DDS.
- It adds a copy (wtf?) of the new variable to the DDS at the same scope
  (but not in the same lexical position)

If the request is a "data" request, then:

- If the variable IS NOT a `libdap::Array` (checked using a
  dynamic_cast) it calls the variables `libdap::BaseType::read()`
  method.
  - THIS IS MAY BE A PROBLEM: If the variable is a `libdap::Grid` or a
    `libdap::Structure` that contains large data objects, such as
    arrays, all of this data will be read into program memory. This is
    especially troubling if one considers that such a `Structure` may be
    part of the aggregation and thus many, many of them could be read
    into memory.
- The next line of code calls
  `ncml_module::VariableElement::replaceArrayIfNeeded()` which:
  - Checks that the passed variable is a `libdap::Array` using a
    dynamic_cast and returns the unmodified variable if not an array.
  - Creates a new instance of `ncml::RenamedArrayWrapper` using a copy
    (via `ptr_duplicate()`) of the original Array.
  - It deletes the original Array from the DDS.
  - It sets the name of the `ncml::RenamedArrayWrapper` instance to the
    new name..
  - It adds a copy (again wtf?) of the new instance of
    `ncml::RenamedArrayWrapper` to the DDS at the same scope (but not
    the same lexical position) as the source array.

The `ncml::RenamedArrayWrapper` is essentially a wrapper class for the
original `libdap:Array` which is held internally as private data. The
methods of `ncml::RenamedArrayWrapper` simply wrap (shadow??) the
methods of the original array, with the exception of the print_\*
methods which rely on the nominal implementation to return the DDS/DMR
components with the new name.

### How attributes are modified, added or removed

### How Join New aggregations are built

### How Join Existing aggregations are built