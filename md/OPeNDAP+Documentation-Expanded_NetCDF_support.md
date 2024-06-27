# Expanded NetCDF Support

## Use Cases

### NC4.1: Files that use the NetCDF4 Classic model

File that conform to the **NC_CLASSIC_MODEL** data model as documented
by Unidata will open and work without error.

Although the files use only data model features found in the netCDF 3
data model/API, using the netCDF 4 API will enable chunking and internal
compression *if* the files in question were written that way.

To be safe, the software that writes the files should use the
**NC_CLASSIC_MODEL** when it makes/modifies the file(s).

### NC4.2: Files that use some new features

The specific features that will be supported are:

- All of the [External Data
  Types](http://www.unidata.ucar.edu/software/netcdf/docs/netcdf/External-Types.html#External-Types)
  *except* the **int64** and **uint64**.
- Enum: Supported by using an int and a set of attributes
- Opaque: Supported by using a Byte array and an attribute indicating
  that the data are to be treated as an atomic entity
- Groups: Encoded using slash (**/**) characters in variable names in a
  way that is compatible with the convention established by The HDF
  Group and NASA for HDF5 files.

**Proposed changes (9/15)**:

- All of the [External Data
  Types](http://www.unidata.ucar.edu/software/netcdf/docs/netcdf/External-Types.html#External-Types)
  *except* the **int64** and **uint64**.
- Compound: complete support for this type using Structures.
  - Compound variables (including nested compounds and compounds that
    contain arrays as fields)
  - Compound array variables (including nested compounds and compounds
    that contain arrays as fields)
- Enum: Supported by using an int and a set of attributes
- Opaque: Supported by using a Byte array and an attribute indicating
  that the data are to be treated as an atomic entity
- vlen: No support
- Group: read data in the root group only

### NC4.3: Files that use (potentially) all new features

These will fail with an error message indicating they are not supported
along with the specific feature that caused the failure.

Unsupported NetCDF4 model/API features are:

- User defined types
- Multiple unlimited dimensions
- Int64 and Unsigned Int64 [External Data
  Types](http://www.unidata.ucar.edu/software/netcdf/docs/netcdf/External-Types.html#External-Types)

**Proposed changes (9/15)**:

- Instead of a hard failure when an unknown type is found, the handler
  will be configurable so that *either* a hard error can be returned
  *or* the handler will print a message to standard error and ignore the
  unknown type (continuing on to process other variables in the data
  set).
- I did not realize that netCDF considers Compound, Opaque, Enum and
  Vlen to be user defined types; all but Vlen will be supported.
- Multiple unlimited dimensions will be supported to the extent that DAP
  is a read-only protocol and these really affect writing only.
- The 64-bit ints will not be supported until DAP4.

## Definitions

NetCDF4 Data Model: The collection of data types and their interrelations as defined by NetCDF4. It is not a software library but an abstract design.

<!-- -->

NetCDF4 API: The library that implements the NetCDF4 Data Model. This same library also implements the netCDF3 data model and contains a separate API that can be used only with those kinds of files. The netCDF4 API can read file written using the netCDF3 API.

<!-- -->

NC_CLASSIC_MODEL: Constant defined by the netCDF4 API to indicate that although a file is being written as a NetCDF4 file, possibly using chunking and/or internal compression, it otherwise follows the definitions of the netCDF3 data model.

## Background

The netCDF4 API offers some real benefits for those serving data,
particularly because of its support for file chunking and internal
compression. These features enable a data server to decompress only the
needed parts of a large file, resulting in significant performance
gains. This feature is present in HDF4 and HDF5 where is can reduce data
volumes in temporary storage by an order of magnitude or more.

NetCDF4 also supports a more advanced data model.

## Design

This is an extension of an existing design.

For the External Types we will simply map as appropriate to the existing
DAP2 types.

For other types, as noted in the NC4.2 use case, we will us DAP2
attributes to encode extra information. This information will be used
during server-side processing of data requests.

## Deliverables

Two versions of the NetCDF handler will be delivered:

1.  A version that builds with and uses the netCDF4 API library. This
    version corresponds to use case NC4.1
2.  A version that includes software to process those new features of
    the data model as described in use case NC4.2

**Proposed addition (9/15)**:

1.  Handler documentation: A page [documenting the
    handler](BES_-_Modules_-_The_NetCDF_Handler "wikilink") will be
    added to the Hyrax documentation.

In both cases the software will be made available using our normal
software deliver channels (web site: Source, RPM and OS/X package binary
distributions).

## Period of use

May 2011 onward

[Category:HAI](Category:HAI "wikilink")
[Category:Hyrax](Category:Hyrax "wikilink")