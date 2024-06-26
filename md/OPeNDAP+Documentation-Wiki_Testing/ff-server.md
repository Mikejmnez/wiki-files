[return to FreeForm](FreeForm "wikilink")

# The OPeNDAP FreeForm ND Data Handler

The OPeNDAP FreeForm ND Data Handler is a OPeNDAP server add-on that
uses OPeNDAP FreeForm ND to convert and serve data in formats that are
not directly supported by existing [Hyrax](Hyrax "wikilink") data
handlers. Bringing OPeNDAP FreeForm ND 's data conversion capacity into
the OPeNDAP world widens data access for DAP2 clients, since any format
that can be described in OPeNDAP FreeForm ND can now be served by the
OPeNDAP data server.

Like all DAP2 servers, the OPeNDAP FreeForm ND Data Handler responds to
client requests for data by returning either data values or information
about the data. It differs from other DAP2 servers because it invokes
OPeNDAP FreeForm ND to read the data from disk before serving it to the
client.

The following sequence of steps illustrates how the OPeNDAP FreeForm ND
Data Handler works:

1.  A DAP2 client sends a request for data to a OPeNDAP FreeForm ND Data
    Handler . The request must include the name of the file that
    contains the data, and may include a constraint expression to sample
    the data.
2.  The OPeNDAP FreeForm ND Data Handler looks in its path for two
    files: a data file with the name sent by the client, and a format
    definition file to use with the data file. The format definition
    file contains a description of the data format, constructed
    according to the OPeNDAP FreeForm ND syntax.
3.  The server uses both files in invoking the OPeNDAP FreeForm ND
    engine. The OPeNDAP FreeForm ND engine reads the data file and the
    format file, using the instructions in the latter to convert the
    former into data which is then passed back to the OPeNDAP FreeForm
    ND Data Handler .
4.  On receiving the converted data, the OPeNDAP FreeForm ND Data
    Handler converts the data into the DAP2 transmission format. The
    conversion may involve some adjustment of data types; these are
    listed below. The server also applies any constraint expressions the
    client sent along with the URL.
5.  The server then constructs DDS and DAS files based on the format of
    the converted data. If the server has access to DDS and DAS files
    that describe the data, it applies those definition before sending
    them back to the client.
6.  Finally, the server sends the DDS, DAS, and converted data back to
    the client.

For information about how to write a OPeNDAP FreeForm ND data
description, refer to the [Table Format](Wiki_Testing/tblfmt "wikilink")
for sequence data and [Array Format](Wiki_Testing/arrayfmt "wikilink")
for array data.

For an introduction to DAP2 and to the OPeNDAP project, please refer to
The Opendap User Guide.

## Differences between OPeNDAP FreeForm ND and the OPeNDAP FreeForm ND Data Handler

The OPeNDAP FreeForm ND Data Handler is based on the same libraries used
to make the OPeNDAP FreeForm ND utilities. However, there are some
important differences in the resulting software:

- The OPeNDAP FreeForm ND Data Handler is a OPeNDAP FreeForm ND
  application that converts data *on receiving a client request for that
  data*, and not before. Data served by the OPeNDAP FreeForm ND Data
  Handler remains in its original format.
- The OPeNDAP FreeForm ND Data Handler does not produce an output file
  containing the converted data, but serves it directly over the network
  to the DAP2 client. Therefore, the OPeNDAP FreeForm ND Data Handler
  ignores the output section of the format definition file.
- To sample a data file, you do not write format definitions that cause
  the OPeNDAP FreeForm ND engine to sample the data file. Instead, you
  add a DAP2 "constraint expression" to the URL that the client sends to
  the OPeNDAP FreeForm ND Data Handler .
- The OPeNDAP FreeForm ND Data Handler performs data conversion on the
  fly. Conversion only takes place when the client sends a URL
  requesting data from the OPeNDAP FreeForm ND Data Handler .
- Unlike OPeNDAP FreeForm ND , there is no static file created by the
  conversion.

(If you wish to create or work with such a file, use the OPeNDAP
FreeForm ND utilities, such as <font color='green'>newform</font>.)

## Data Type Conversions

The OPeNDAP FreeForm ND Data Handler performs data conversions, based on
the data it receives from the OPeNDAP FreeForm ND engine. Note that
OPeNDAP does not recommend the use of <font color='green'>int64</font>
and <font color='green'>uint64</font> in the format definition file.

<center>

DAP2 Data Type Conversions

| **OPeNDAP FreeForm ND**                                              | **DAP2**                           |
|----------------------------------------------------------------------|------------------------------------|
| <font color='green'>text</font>                                      | <font color='green'>String</font>  |
| <font color='green'>int8</font>, <font color='green'>uint8</font>    | <font color='green'>Byte</font>    |
| <font color='green'>int16</font>                                     | <font color='green'>Int16</font>   |
| <font color='green'>int32</font>, <font color='green'>int64</font>   | <font color='green'>Int32</font>   |
| <font color='green'>uint16</font>                                    | <font color='green'>UInt16</font>  |
| <font color='green'>uint32</font>, <font color='green'>uint64</font> | <font color='green'>UInt32</font>  |
| <font color='green'>float32</font>                                   | <font color='green'>Float32</font> |
| <font color='green'>float64</font>, <font color='green'>enote</font> | <font color='green'>Float64</font> |

</center>

### Conversion Examples

The examples show how the OPeNDAP FreeForm ND Data Handler treats data
received from the OPeNDAP FreeForm ND engine. Please see the OPeNDAP
FreeForm ND Data Handler distribution for more test data and format
definition files, and the ([Table
Format](Wiki_Testing/tblfmt "wikilink")) for more information on writing
format definitions.

#### Arrays

If you define a variable as an array in the OPeNDAP FreeForm ND format
definition file, the OPeNDAP FreeForm ND Data Handler produces an array
of variables with matching types.

For exmple, this entry in the format definition file:

    binary_input_data "array"
    fvar1 1 4 ARRAY["records" 1 to 101] of int32 0

in converted by the OPeNDAP FreeForm ND Data Handler to:

    Int32 fvar1[records = 101]

#### Collections of Variables

If you define several variables in the format definition file, the
OPeNDAP FreeForm ND Data Handler produces a Sequence of variables with
matching types.

For example, this entry in the format definition file:

    ASCII_input_data "ASCII_data"
    fvar1   1 10  int32 2
    svar1  13 18  int16 0
    usvar1 21 26 uint16 1
    lvar1  29 39  int32 0
    ulvar1 42 52 uint32 4

is converted by the OPeNDAP FreeForm ND Data Handler to:

    Sequence {
    Int32 fvar1;
    Int32 svar1;

    ...
    } ASCII_data;

#### Multiple Arrays

If you define a collection of arrays in the format definition file, as
you would expect, the OPeNDAP FreeForm ND Data Handler produces a
dataset containing multiple arrays.

For example, this entry in the format definition file:

    binary_input_data "arrays"
    fvar1 1 4 ARRAY["records" 1 to 101] of int32 0
    fvar2 1 4 ARRAY["records" 1 to 101] of int32 0

is converted by the OPeNDAP FreeForm ND Data Handler to:

    Dataset {
    Int32 fvar1[records=101]
    Int32 fvar2[records=101]
    };