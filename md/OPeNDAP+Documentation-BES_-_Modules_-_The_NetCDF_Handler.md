## Kinds of files the handler will serve

There are several versions of the netCDF software for reading and
writing data and using those different versions, it's possible to make
several different kinds of data files. For the most part, netCDF strives
to maintain compatibility so that any older file can be read using any
newer version of the library. To ensure that the netCDF handler can read
(almost) any valid netCDF data file, you should make sure to use the
latest version of the netCDF library when you build or install the
handler.

However, as of netCDF 4, there are some new data model components in
netCDF that are hard to represent in DAP2 (hence the 'almost' in the
preceding paragraph). If the handler, as of version 3.10.x, is linked
with netCDF 4.1.x or later, you will be able to read any netCDF file
that fits the 'classic' model of netCDF (as defined by [Unidata's
documentation](http://www.unidata.ucar.edu/software/netcdf/docs/netcdf/Which-Format.html#Which-Format))
which essentially means any file that uses only data types present in
the netCDF 3.x API but with the addition that these files can employ
both internal compression and chunking.

The new data types present in the netCDF data model present more of a
challenge. However, as of version 3.10.x, the Hyrax data handler will
serve most of the new cardinal types and the more commonly used 'user
defined types'.

### Mappings between NetCDF version 4 data model and DAP2 data types

All of the cardinal types in the netCDF 4 data model map directly to
types in DAP2 except for the following:

NC_BYTE: There is no 'signed byte' type in DAP2 so these map to an unsigned byte or signed Int16, depending on the value of the option NC.PromoteByteToShort (see below where the configuration parameters are described).
NC_CHAR: There is no 'character' type in DAP2 so these map to DAP Strings of length one. Arrays of *N* characters in netCDF map to arrays of *N-1* Strings in DAP
NC_INT64, NC_UINT64: DAP2 does not support 64-bit integers (this will be added soon to the next version of the protocol).

#### Mappings for netCDF 4's User Defined types

In the netCDF documentation, types such as Compound (which is
effectively C's *struct* type), et c., are called *User Defined* types.
Unlike the cardinal types, netCDF 4'S user defined types don't always
have a simple mapping to DAP2's types. However, the most important of
the user defined types, NC_COMPOUND, does map directly to DAP2's
Structure. Here's how the user defined types are mapped by the handler
as of version 3.10:

NC_COMPOUND: This maps directly to a DAP2 Structure. The handler works with both compound variables and attributes. For attributes, the handler only recognizes scalar and vector (one-dimensional) compounds. For variables scalar and array compounds are supported including compounds within compounds and compounds with fields that are arrays.
NC_VLEN: Not supported
NC_ENUM: Supported so long as the 'base type' is not a 64-bit integer. We add extra attributes to help the downstream user. We add *DAP2_OriginalNetCDFBaseType* with the value *NC_ENUM* and *DAP2_OriginalNetCDFTypeName* with the name of the type from the file (Enums in netCDF are user-defined types, so they have names set y the folks who wrote the file). We also add two attributes that provide information about the integral values and they names (e.g., Clear = 0, Cumulonimbus = 1, Stratus = 2, ..., Missing = 255) using two attributes: *DAP2_EnumValues* and *DAP2_EnumNames*.
NC_OPAQUE: This type is mapped to an array of Bytes (so the scalar NC_OPAQUE becomes a one-dimensional array in DAP2). If a netCDf file contains an array (with *M* dimensions) of NC_OPAQUE vars, then the DAP response will contain a Byte array with *M+1* dimensions. In addition, the handler adds an attribute *DAP2_OriginalNetCDFBaseType* with the value *NC_OPAQUE* and *DAP2_OriginalNetCDFTypeName* with the name of the type from the file to the Byte variable so that savvy clients can see what's going on. Even though the DAP2 object for an NC_OPAQUE is an array, it cannot be subset (but arrays of NC_OPAQUEs can be subset with the restriction that *M+1* dimensional DAP2 Byte array can only be subset in the original NC_OPAQUE's *M* dimensions).

#### NetCDF 4's Group

The netCDF handler currently reads only from the root group.

## Configuration parameters

#### IgnoreUnknownTypes

When the handler reads a type that it does not recognize, it will
normally signal an error and stop processing. Setting this parameter to
true will cause it to silently ignore the unknown type (an error message
may be written to the bes log file).

Accepted values: true,yes\|false,no, defaults to false.

Example:

`NC.IgnoreUnknownTypes=true`

#### ShowSharedDimensions

Include shared dimensions as separate variables. This feature is
included to support older clients based on the netCDF library. Some
versions of the library depend on the shared dimensions appearing as
variables at the 'top' of the file.

Clients that announce to the server that they understand newer versions
of the DAP (3.2 and up) won't need these extra variables, while older
ones likely will. In the 3.10.0 version of the handler, the DAP version
that clients announce they can accept will determine how the handler
responses *unless* this parameter is set, in which case, the value set
in the configuration file will override that default behavior.

Accepted values: true,yes\|false,no, defaults to false.

Example:

`NC.ShowSharedDimensions=false`

#### PromoteByteToShort

This option first appears in Hyrax 1.8; version 3.10.0 of the
netcdf_handler.

**Note**: Hyrax version 1.8 ships with this turned on in the netcdf
handler's configuration file, even though the default for the option is
*off*.

Use this option to promote DAP2 *Byte* variables and attributes to
*Int16*, noting that Byte is unsigned and Int16 is signed, so this is a
way to preserve the sign of netCDF's *signed Byte* data type.

For netcdf4 files, this option behaves the same except that NC_OPAQUE
variables are externalized as DAP Bytes regardless of the option's
value; their Byte attributes, on the other hand, as promoted to Int16
when the option is *true*.

Backstory: In NetCDF the *Byte* data type is signed while in DAP2 it is
unsigned. For data (i.e., variables) this often makes no real difference
because byte data are often read from the network and dumped into an
array where their sign is interpreted (correctly or not) by the client
software - in other words byte-data is often a special case. However,
this is, strictly speaking, wrong. In addition, and maybe more
importantly, with attributes the values are interpreted by the server
and represented in ASCII (and sent to the client as text), so the sign
is interpreted by the server and and the resulting text is converted
into a binary value by the client; the simple trick of letting the
default C types handle the value's sign won't work. One way around this
incompatibility is to promote *Byte* in DAP2 to *Int16*, which is a
signed type.

`Accepted values: true,yes|false,no, defaults to false, the server's original behavior.`

Example:

`NC.PromoteByteToShort=true`

## Appendix

<table>
<caption><em>The complete set of mappings for the types in the netCDF 4
data model<br />
(entries in <font color="gray">gray</font> are new types not currently
supported; entries in <font color="green">green</font> are new types
that are supported)</em></caption>
<thead>
<tr class="header">
<th><p>netCDF type name</p></th>
<th><p>netCDF type description</p></th>
<th><p>DAP2 type name</p></th>
<th><p>DAP2 type description</p></th>
<th><p>Notes</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>NC_BYTE</p></td>
<td><p>8-bit signed integer</p></td>
<td><p>dods_byte<br />
<em>dods_int16</em> (see note)</p></td>
<td><p>8-bit unsigned integer<br />
<em>16-bit signed int</em> (see note)</p></td>
<td style="text-align: left;"><p>The DAP2 type is unsigned; This mapping
can be changed so that netcdf Byte mapps to DAP2 Int16 (which will
preserve the netCDF Byte's sign bit (see the NC.PromoteByteToShort
configuration parameter).</p></td>
</tr>
<tr class="even">
<td><p>NC_UBYTE</p></td>
<td><p>8-bit unsigned integer</p></td>
<td><p>dods_byte</p></td>
<td><p>8-bit unsigned integer</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>NC_CHAR</p></td>
<td><p>8-bit unsigned integer</p></td>
<td><p>dods_str</p></td>
<td><p>variable length character string</p></td>
<td style="text-align: left;"><p>Treated as character data; arrays are
treated specially (see text)</p></td>
</tr>
<tr class="even">
<td><p>NC_SHORT</p></td>
<td><p>16-bit signed integer</p></td>
<td><p>dods_int16</p></td>
<td><p>16-bit signed integer</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>NC_USHORT</p></td>
<td><p>16-bit unsigned integer</p></td>
<td><p>dods_uint16</p></td>
<td><p>16-bit unsigned integer</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>NC_INT</p></td>
<td><p>32-bit signed integer</p></td>
<td><p>dods_int32</p></td>
<td><p>32-bit signed integer</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>NC_UINT</p></td>
<td><p>32-bit unsigned integer</p></td>
<td><p>dods_uint32</p></td>
<td><p>32-bit unsigned integer</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>NC_INT64</p></td>
<td><p>64-bit signed integer</p></td>
<td><p>None</p></td>
<td></td>
<td style="text-align: left;"><p>Not supported</p></td>
</tr>
<tr class="odd">
<td><p>NC_UINT64</p></td>
<td><p>64-bit unsigned integer</p></td>
<td><p>None</p></td>
<td></td>
<td style="text-align: left;"><p>Not supported</p></td>
</tr>
<tr class="even">
<td><p>NC_FLOAT</p></td>
<td><p>32-bit floating point</p></td>
<td><p>dods_float32</p></td>
<td><p>32-bit floating point</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>NC_DOUBLE</p></td>
<td><p>64-bit floating point</p></td>
<td><p>dods_float64</p></td>
<td><p>64-bit floating point</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>NC_STRING</p></td>
<td><p>variable length character string</p></td>
<td><p>dods_str</p></td>
<td><p>variable length character string</p></td>
<td style="text-align: left;"><p>In DAP2 it's impossible to distinguish
this from an array of NC_CHAR</p></td>
</tr>
<tr class="odd">
<td><p>NC_COMPOUND</p></td>
<td><p>A user defined type similar to C's struct</p></td>
<td><p>dods_structure</p></td>
<td><p>A DAP Structure; similar to C's struct</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>NC_OPAQUE</p></td>
<td><p>A BLOB data type</p></td>
<td><p>dods_byte</p></td>
<td><p>an array of bytes</p></td>
<td style="text-align: left;"><p>The handler adds two attributes
(<em>DAP2_OriginalNetCDFBaseType</em> with the value NC_OPAQUE<br />
and <em>DAP2_OriginalNetCDFTypeName</em> with the type's name) that
provide info for savvy clients;<br />
see text above about subsetting details</p></td>
</tr>
<tr class="odd">
<td><p>NC_ENUM</p></td>
<td><p>Similar to C's enum</p></td>
<td><p>dods_byte, ..., dods_uint32</p></td>
<td><p>any integral type</p></td>
<td style="text-align: left;"><p>The handler chooses an integral type
depending on the type used in the NetCDF file.<br />
It adds the <em>DAP2_OriginalNetCDFBaseType</em> and
<em>DAP2_OriginalNetCDFTypeName</em> attributes<br />
as with NC_OPAQUE and also <em>DAP2_EnumNames</em> and
<em>DAP2_EnumValues</em>. Enums with 64-bit<br />
integer base types are not supported.</p></td>
</tr>
<tr class="even">
<td><p>NC_VLEN</p></td>
<td><p>variable length arrays</p></td>
<td><p>None</p></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>

*The complete set of mappings for the types in the netCDF 4 data model
(entries in <font color="gray">gray</font> are new types not currently
supported; entries in <font color="green">green</font> are new types
that are supported)*