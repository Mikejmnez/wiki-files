## Kinds of files the handler will serve

This release of the server supports HDF5 files written using any version
of the HDF5 API. The handler should be built/linked with version 1.8.x
of the API.

### Mappings between the HDF5 data model and DAP2 data types

The mapping between the HDF5 and HDF-EOS5 data model and DAP2 is
documented in a NASA Technical Note
([ESDS-RFC-017](http://www.esdswg.org/spg/rfc/esds-rfc-017)). This note
is quite detailed; a summary from its appendix is provided below.

### Special characters in HDF identifiers

A number of non-alphanumeric characters (e.g., space, \#, +, -) used in
HDF identifiers are not allowed in the names of DAP objects, object
components or in URLs. The HDF5 data handler therefore deals internally
with translated versions of these identifiers. To translate the WWW
convention of escaping such characters by replacing them with "%"
followed by the hexadecimal value of their ASCII code is used. For
example, "Raster Image \#1" becomes "Raster%20Image%20%231". These
translations should be transparent to users of the server (but they will
be visible in the DDS, DAS and in any applications which use a client
that does not translate the identifiers back to their original form).

### Known problems

#### Handling of floating point attributes

Because the DAP software encodes attribute values as ASCII strings there
will be a loss of accuracy for floating point attributes. This loss of
accuracy is dependent on the version of the C++ I/O library used in
compiling/linking the software (i.e., the amount of floating point
precision preserved when outputting to ASCII is dependent on the
library). Typically it is very small (e.g., at least six decimal places
are preserved).

## Configuration parameters

H5.EnableCF: This is an option to support NASA HDF5/HDF-EOS5 data products. The HDF5/HDF-EOS5 data products do not follow CF conventions. However, hdf5_handler can make them follow the conventions if you turn on this option. The key benefit of this option is to allow OPeNDAP visualization clients to display remote data seamlessly. Please visit [here](http://hdfeos.org/software/hdf5_handler/doc/cf.php) for details.

<!-- -->

H5.IgnoreUnknownTypes: Ignore variables that use data types the handler cannot process. In practice this means 64-bit integers. DAP2 does not support the 64-bit integer type; using this parameter (i.e., setting its value to *yes* or *true*) means that 64-bit integer variables are ignored and the rest of the variables in the file can be read. The default value of this parameter (*no* or *false*) configures the handler to return an error when a 64-bit integer variable is found.

<!-- -->

H5.EnableCheckNameClashing: This option checks if there are duplicate variable names when group information is removed and some characters are replaced with underscore character. Although this option is required to ensure that no two variable names are same, the operation is quite costly and thus can degrade the performance significantly. If you are certain that your HDF5 data won't have any duplicate name, you can turn this off to gain the performance of the server. For the NASA HDF5/HDF-EOS5 products we tested (AURA OMI/HIRDLS/MLS/TES, MEaSUREs SeaWiFS/Ozone, Aquarius, GOSAT/acos, SMAP), we did not find any name clashing for those objects. So the name clashing check seems unnecessary and this option is turned off by default. The handler will check the name clashing for any products not tested regardless of turning this option on or off.

<!-- -->

H5.EnableAddPathAttr: When this option is turned off, the HDF5 handler will not insert the **fullnamepath** and **origname** attribute in DAS output. For example, the DAS output like below:

`     temperature {`
`        String units "K";`
`        String origname "temperature";`
`        String fullnamepath "/HDFEOS/GRIDS/GeoGrid/Data Fields/temperature";`
`        String orig_dimname_list "XDim ";`
`      }`


will change to:

`     temperature {`
`        String units "K";`
`        String orig_dimname_list "XDim ";`
`     }`

H5.EnableDropLongString: NetCDF Java client cannot handle string size bigger than 32767 and will throw an error if such variable is seen. Thus, the HDF5 handler need to hide such long string variables from DAS and DDS output. Setting this option to true will ensure that NetCDF Java OPeNDAP visualization clients such as IDV and Panoply can visualize other important variables.

<!-- -->

H5.DisableStructMetaAttr: When this option is true, StructMetadata attribute will not be generated in DAS output.

## Appendix

| HDF5 data type                 | DAP2 data name    | Notes                                                                                                                                                                                                                                                   |
|--------------------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 8-bit unsigned integer         | Byte              |                                                                                                                                                                                                                                                         |
| 8-bit signed integer           | Int16             |                                                                                                                                                                                                                                                         |
| 16-bit unsigned integer        | UInt16            |                                                                                                                                                                                                                                                         |
| 16-bit signed integer          | Int16             |                                                                                                                                                                                                                                                         |
| 32-bit unsigned integer        | UInt32            |                                                                                                                                                                                                                                                         |
| 32-bit signed integer          | Int32             |                                                                                                                                                                                                                                                         |
| 64-bit unsigned integer        | N/A               | Results in an error unless H5.IgnoreUnknownTypes is true. See above.                                                                                                                                                                                    |
| 64-bit signed integer          | N/A               | ... H5.IgnoreUnknownTypes ...                                                                                                                                                                                                                           |
| 32-bit floating point          | Float32           |                                                                                                                                                                                                                                                         |
| 64-bit floating point          | Float64           |                                                                                                                                                                                                                                                         |
| String                         | String            |                                                                                                                                                                                                                                                         |
| Object/region reference        | URL               |                                                                                                                                                                                                                                                         |
| Compound                       | Structure         | HDF5 compound can be mapped to DAP2 under the condition that the base members (excluding object/region references) of compound can be mapped to DAP2.                                                                                                   |
| Dataset                        | Variable          | HDF5 dataset can be mapped to DAP2 under the condition that the datatype of the HDF5dataset can be mapped to DAP2.                                                                                                                                      |
| Attribute                      | Attribute         | HDF5 attribute can be mapped to DAP2 under the condition that the datatype of the HDF5 dataset can be mapped to DAP2, and the data is either scalar or one-dimensional array.                                                                           |
| Group                          | naming convention | A special attribute *HDF5_ROOT_GROUP* is used to represent the HDF5 group structure; The absolute path of the HDF5 dataset as the DAP2 variable name; HDF5 group can be mapped to DAP2 under the condition that the file structure is a tree structure. |
| HDF-EOS5 grid w/1-D projection | Grid              | The latitude and longitude are encoded according to CF                                                                                                                                                                                                  |
| HDF-EOS5 grid w/2-D projection | Arrays            | Map data variables to DAP2 Arrays; generate DAP2 Arrays for latitude and longitude (following CF); add a *coordinates* attribute for each variable providing the names of the coordinate variables (following CF).                                      |
| HDF-EOS5 Swath                 | Arrays            | Follow the same prescription as with HDF-EOS5 2-D grids                                                                                                                                                                                                 |

*The complete set of mappings for the types in the HDF5 and HDF-EOS5
data model*