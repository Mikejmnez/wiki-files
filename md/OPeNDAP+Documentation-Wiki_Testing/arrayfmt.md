[return to FreeForm](FreeForm "wikilink")

# Format Descriptions for Array Data

If the tabular format discussed in [Table
Format](Wiki_Testing/tblfmt "wikilink") doesn't describe your data well,
FreeForm ND's array notation may prove useful. Describing a data file's
organization as a set of as n-dimensional arrays allows for much more
flexibility in writing format definitions. It also enables subsetting,
pixel-manipulation, and reorienting arrays of arbitrary dimension and
character.

## Array Descriptor Syntax

FreeForm ND allows you to describe the same fundamental FreeForm ND data
types in array notation. The arrays can have any number of dimensions,
any number of elements in each dimension, and either an increasing or a
decreasing sequencing in each dimension. Furthermore, elements in any
dimension may be separated from each other (demultiplexed) and may even
be placed in separate files. However, every element of an array must be
of the same type.

Array descriptors are a string of n dimension descriptions for arrays of
n dimensions. FreeForm ND accepts descriptions with the most significant
dimension first (i.e. row-major order for 2 dimensions, plane-major
order in 3 dimensions).

Individual dimension descriptions are enclosed in brackets. Each
dimension description can contain various keywords and values which
specify how the dimension is set up. Some of the specifications are
optional; if you do not specify them, they default to a specific value.

> You must not mix array and tabular formats within the input and output
> sections of the format definition file. Only one type of notation can
> be used within each section of the format description file, although
> the sections may use different forms. For example, a file's input
> format could use array definitions, but the output format might be
> entirely tabular.

The dimension description variables include:

dimension name (REQUIRED) :

A name for the dimension. This can be any ASCII string enclosed in
double-quotes ("). The name for each dimension must be unique throughout
the array descriptor. This example specifies that a dimension named
"latitude" exists:

    ["latitude" 0 to 180]

starting and ending indices (REQUIRED) :

A starting and ending index specifying a range for the dimension. The
starting and ending indices are specified as integers separated by the
word "to" following the dimension name. As long as both numbers are
integral, there are no other restrictions on their values. This example
specifies that the dimension "temperature" has indices ranging from -50
to +50:

    ["temperature" -50 to 50]

granularity (optional) :

A specification for the density of elements in the indices. The number
provided after the "by" keyword specifies how many index positions are
to be skipped to find the next element. This example specifies that
index values 0, 50, 100, 150 and 200 are the only valid index values for
the dimension "height":

    ["height" 0 to 200 by 50]

grouping (optional) :

A specification for splitting an array across "partitions" (files or
buffers in memory). The number provided after the "gb" or "groupedby"
keyword specifies how many elements of the dimension are in each
partition. If no value is specified, the default is 0 (no partitioning).
Each partition must have the same number of elements. Every
more-significant dimension description (those to the left) must also
have a grouping specified- "dangling" grouping specifications are not
allowed. If a dimension is not partitioned, but is required to have a
grouping specification because a less-significant dimension is
partitioned, a grouping of M can be specified, where:

    M = [end_index -  start_index] + 1

This example specifies that the dimension "latitude" is partitioned into
9 chunks of 10 "bands" of latitude each:

    ["latitude" 1 to 90 gb 10]

separation (optional) :

A specification for "unused space" in the array. The number provided
after the "sb" or "separation" keyword specifies how many bytes of data
following each element in the dimension should not be considered part of
the array. An "element in the dimension" is considered to be everything
which occurs in one index of that dimension. separation takes on a
slightly different meaning if the dimension also has a specified
grouping. In dimensions with a specified grouping, the separation occurs
at the end of each partition, not after every element. This example
specifies a 2-dimensional array with 4 bytes between the elements in the
"columns" and an additional 2 bytes at the end of every row:

    ["lat" -90 to 90 sb 2]["lon" -180 to 179 sb 4]

## Handling Newlines

The convention of expecting a newline to follow each record of ASCII
data becomes troublesome when dealing with array data, especially when
expressed using format description notation that is intended for tabular
data. It is the FreeForm ND convention that there is an implicit newline
after the last variable of an ASCII format.

For example, these two format descriptions are equivalent:

    ASCII_data "broken time --- BIP"
    year 1 2 uint8 0
    month 3 4 uint8 0
    day 5 6 uint8 0
    hour 7 8 uint8 0
    minute 9 10 uint8 0
    second 11 14 uint16 2

    dBASE_data "broken time --- BIP"
    year 1 2 uint8 0
    month 3 4 uint8 0
    day 5 6 uint8 0
    hour 7 8 uint8 0
    minute 9 10 uint8 0
    second 11 14 uint16 2
    EOL 15 16 constant 0

However, the EOL variable shown here assumes that newlines are two bytes
long, which is true only on a PC. FreeForm ND adjusts to this by
assuming that ASCII data always has native newlines, and it updates the
starting and ending position of EOL variables and subsequent variables
accordingly.

The EOL variable is typically used to define a record layout that spans
multiple lines. However, the EOL variable in combination with the dBASE
format type can completely replace the ASCII format type.We recommend
using the dBASE format type when describing ASCII arrays, to ensure that
separation, if specified, takes into account the length of any newlines.
In this output format a newline separates each band of data, but it
would be just as easy to omit the newlines entirely.

    dBASE_input_data "broken time --- BIP"
    year 1 2 array["x" 1 to 10 sb 14] of uint8 0
    month 3 4 array["x" 1 to 10 sb 14] of uint8 0
    day 5 6 array["x" 1 to 10 sb 14] of uint8 0
    hour 7 8 array["x" 1 to 10 sb 14] of uint8 0
    minute 9 10 array["x" 1 to 10 sb 14] of uint8 0
    second 11 14 array["x" 1 to 10 sb 12] of uint16 2
    EOL 15 16 array["x" 1 to 10 sb 14] of constant 0

    dBASE_output_data "broken time - BSQ"
    year 1 2 array["x" 1 to 10] of uint8 0
    EOL 21 22 constant 0
    month 23 24 array["x" 1 to 10] of uint8 0
    EOL 43 44 constant 0
    day 45 46 array["x" 1 to 10] of uint8 0
    EOL 65 66 constant 0
    hour 67 68 array["x" 1 to 10] of uint8 0
    EOL 87 89 constant 0
    minute 90 91 array["x" 1 to 10] of uint8 0
    EOL 110 111 constant 0
    second 112 115 array["x" 1 to 10] of uint16 2
    EOL 132 133 constant 0

> The separation size now takes into account the two-character PC
> newline. To use this format description with a native ASCII file on
> UNIX platforms, it would be necessary to change the separation sizes
> of 12 and 14 to 11 and 13, respectively.

## Examples

The following examples should be helpful in understanding the array
notation.

### Tabular versus Array Descriptions

Array notation can simply replace the tabular format description, as in
these examples.

A single element can be described in tabular format:

    year 1 2 uint8 0

or as an array:

    year 1 2 array["x" 1 to 10] of uint8 0

An image file can be described in tabular format:

    binary_input_data "grid data"
    data 1 1 uint8 0

or as an array:

    binary_input_data "grid data"
    data 1 1 array["rows" 1 to 180] ["cols" 1 to 360] of uint8 0

Multiplexed data can be described in tabular format:

    ASCII_data "broken time --- tabular"
    year 1 2 uint8 0
    month 3 4 uint8 0
    day 5 6 uint8 0
    hour 7 8 uint8 0
    minute 9 10 uint8 0
    second 11 14 uint16 2

or as an array:

    ASCII_data "broken time -- BIP"
    year 1 2 array["x" 1 to 10 sb 12] of uint8 0
    month 3 4 array["x" 1 to 10 sb 12] of uint8 0
    day 5 6 array["x" 1 to 10 sb 12] of uint8 0
    hour 7 8 array["x" 1 to 10 sb 12] of uint8 0
    minute 9 10 array["x" 1 to 10 sb 12] of uint8 0
    second 11 14 array["x" 1 to 10 sb 10] of uint16 2

These two format descriptions communicate much the same information, but
the array example also indicates that the data file is blocked into ten
data values for each variable.

In this example, the data is not multiplexed:

    ASCII_data "broken time -- BSQ"
    year 1 2 array["x" 1 to 10] of uint8 0
    month 21 22 array["x" 1 to 10] of uint8 0
    day 41 42 array["x" 1 to 10] of uint8 0
    hour 61 62 array["x" 1 to 10] of uint8 0
    minute 81 82 array["x" 1 to 10] of uint8 0
    second 101 104 array["x" 1 to 10] of uint16 2

The starting position indicates the file offset of the first element of
each array, the same as with the alternative definition given for
starting position in tabular data format descriptions.

### Array Manipulation

Consider a 6x6 array of data with an "XXXX" header and a "YY" trailer on
each line. Each data element is a space, a row ("y") index, a comma, and
a column ("x") index, as shown below:

    XXXX 0,0 0,1 0,2 0,3 0,4 0,5YY
    XXXX 1,0 1,1 1,2 1,3 1,4 1,5YY
    XXXX 2,0 2,1 2,2 2,3 2,4 2,5YY
    XXXX 3,0 3,1 3,2 3,3 3,4 3,5YY
    XXXX 4,0 4,1 4,2 4,3 4,4 4,5YY
    XXXX 5,0 5,1 5,2 5,3 5,4 5,5YY

The goal is to produce a data file that looks like the data below. To do
that, we need to strip the headers and trailers, and transpose rows and
columns:

    0,0 1,0 2,0 3,0 4,0 5,0
    0,1 1,1 2,1 3,1 4,1 5,1
    0,2 1,2 2,2 3,2 4,2 5,2
    0,3 1,3 2,3 3,3 4,3 5,3
    0,4 1,4 2,4 3,4 4,4 5,4
    0,5 1,5 2,5 3,5 4,5 5,5

The key to writing the input format description is understanding that
the input data file is composed of four interleaved arrays:

1.  The "XXXX" headers
2.  The data
3.  The "YY" trailers
4.  The newlines

The array of headers is a one-dimensional array composed of six elements
(one for each line) with each element being four characters wide and
separated from the next element by 28 bytes (24 + 2 + 2 --- 24 bytes for
a row of data plus 2 bytes for the trailer plus two bytes for the
newline).

The array of data is a two-dimensional array of six elements in each
dimension with each element being four characters wide, each row is
separated from the next by eight bytes (columns are adjacent and so have
zero separation), and the first element begins in the fifth byte of the
file (counting from one).

The array of trailers is a one-dimensional array composed of six
elements with each element being two characters wide, each element is
separated from the next by 30 bytes, and the first element begins in the
29th byte of the file.

The array of newlines is a one-dimensional array composed of six
elements with each element being two characters wide (on a PC), each
element is separated from the next by 30 bytes, and the first element
begins in the 31st byte of the file.

The FreeForm ND input format description needed is:

    dBASE_input_data "one"
    headers 1 4 ARRAY["line" 1 to 6 separation 28] OF text 0
    data 5 8 ARRAY["y" 1 to 6 separation 8]["x" 1 to 6] OF text 0
    trailers 29 30 ARRAY["line" 1 to 6 separation 30] OF text 0
    PCnewline 31 32 ARRAY["line" 1 to 6 separation 30] OF text 0

The output data is composed of two interleaved arrays:

1.  The data
2.  The newlines

The array of data now has a separation of two bytes between each row,
the first element begins in the first byte of the file, and the order of
the dimensions has been switched.

The array of newlines now has a separation of 24 bytes and the first
element begins in the 25th byte of the file. Each array can be operated
on independently. In the case of the data array we simply transposed
rows and columns, but we could do other reorientations as well, such as
resequencing elements within either or both dimensions.

The FreeForm ND output format description needed is:

    dBASE_output_data "two"
    data 1 4 ARRAY["x" 1 to 6 separation 2]["y" 1 to 6] OF text 0
    PCnewline 25 26 ARRAY["line" 1 to 6 separation 24] OF text 0

### Sampling and Data Manipulation

With a wider range of descriptive possibilities, FreeForm can more
easily be used for sampling and subsetting data, as in these examples.

The following array descriptor pair subsets a two-dimensional array,
retrieving one quarter (the north-west quarter of the earth).

    INPUT: ["latitude" -90 to 90] ["longitude" -179 to 180]
    OUTPUT: ["latitude" 0 to 90] ["longitude" -179 to 0]

The following array descriptor pair flips a two-dimensional array
row-wise (vertically).

    INPUT: ["row" 0 to 100] ["column" 13 to 42]
    OUTPUT: ["row" 100 to 0] ["column" 13 to 42]

The following array descriptor pair rotates a two-dimensional array 90
degrees (exchanging rows and columns).

    INPUT: ["row" 0 to 10] ["column" 0 to 42]
    OUTPUT: ["column" 0 to 42] ["row" 0 to 10]

The following array descriptor pair outputs every other plane from a
three-dimensional array (essentially cutting the depth resolution in
half).

    INPUT: ["plane" 1 to 18] ["row" 0 to 10] ["column" 0 to 42]
    OUTPUT: ["plane" 1 to 18 by 2] ["row" 0 to 10] ["column" 0 to 42]

The following array descriptor pair replicates every plane from a
three-dimensional array three times (essentially tripling the depth).

    INPUT: ["plane" 1 to 54 by 3] ["row" 0 to 10] ["column" 0 to 42]
    OUTPUT: ["plane" 1 to 54] ["row" 0 to 10] ["column" 0 to 42]

This array descriptor pair outputs the middle 1/27 of a three
dimensional array with depth and width exchanged and height halved and
flipped:

    INPUT: ["plane" 1 to 27] ["row" 1 to 27] ["column" 1 to 27]
    OUTPUT: ["column" 10 to 18] ["row" 18 to 10 by 2] ["plane" 10 to 18]