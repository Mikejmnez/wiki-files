# Quick Tour of the OPeNDAP FreeForm ND Data Handler

This chapter provides you a quick introduction to the OPeNDAP FreeForm
ND Data Handler , including writing format descriptions and serving test
datasets.

## Getting Started Serving Data

To get going with the OPeNDAP FreeForm ND Data Handler , follow these
steps:

1.  See [Hyrax](Hyrax "wikilink") for instructions about installing the
    OPeNDAP data server
2.  Install the OPeNDAP FreeForm ND Data Handler.
3.  Examine the structure of the data file(s) you intend to serve, and
    construct a OPeNDAP FreeForm ND format definition file that
    describes the layout of data in the files. (Refer to the ([Table
    Format](Wiki_Testing/tblfmt "wikilink")) for instructions about
    sequence data and ([Array Format](Wiki_Testing/arrayfmt "wikilink"))
    for array data. Consult
    \[<http://docs.opendap.org/index.php/UserGuide><cite>The OPeNDAP
    User Guide</cite>\] if you do not know the difference between the
    two data types.)
4.  If you wish, you may include an output definition format within this
    file, to allow you to test that your input description is accurate.
    You can use the OPeNDAP FreeForm ND utilities, such as
    <font color='green'>newform</font>, to validate the conversion. The
    ([Format Conversion](Wiki_Testing/fmtconv "wikilink")) contains a
    detailed description of <font color='green'>newform</font>. This
    step is optional, since the OPeNDAP FreeForm ND Data Handler ignores
    the output definition section of the format definition file.
5.  Although the OPeNDAP FreeForm ND Data Handler can generate default
    DDS and DAS files, you may want to write these files yourself, to
    override the default data descriptions, or to add attribute data.
    The default descriptions are based on the format of the data the the
    OPeNDAP FreeForm ND Data Handler receives from the OPeNDAP FreeForm
    ND engine.
6.  Place the data files, and a corresponding format file for each data
    file, in a place where [Hyrax](Hyrax "wikilink") can find them. See
    the Hyrax configuration instructions for information about where
    Hyrax looks for its files.

Your data is now available to anyone who knows about your server.

## Examples

You can easily create FreeForm ND format description files that describe
the formats of input and output data and headers. The OPeNDAP FreeForm
ND Data Handler and other OPeNDAP FreeForm ND -based programs then use
these files to correctly access and manipulate data in various formats.
An example format description file is shown and described below.

For complete information about writing format descriptions, see [Chapter
3](Wiki_Testing/tblfmt "wikilink") and
[Chapter4](Wiki_Testing/arrayfmt "wikilink").

### Sequence Data

Here is a data file, containing a sequence of four data types. (This
data file and several of the other examples in this chapter are
available.

Here is the data file, called
[<cite>ffsimple.dat</cite>](http://www.opendap.org/examples/ffsimple.dat):

    Latitude and Longitude:   -63.223548 54.118314  -176.161101 149.408117
    -47.303545 -176.161101 11.7125 34.4634
    -25.928001   -0.777265 20.7288 35.8953
    -28.286662   35.591879 23.6377 35.3314
    12.588231  149.408117 28.6583 34.5260
    -63.223548   55.319598  0.4503 33.8830
    54.118314 -136.940570 10.4085 32.0661
    -38.818812   91.411330 13.9978 35.0173
    -34.577065   30.172129 20.9096 35.4705
    27.331551 -155.233735 23.0917 35.2694
    11.624981 -113.660611 27.5036 33.7004

The file consists of a single header line, followed by a sequence of
records, each of which contains a latitude, longitude, temperature, and
salinity.

Here is a format file you can use to read
<font color='green'>ffsimple.dat</font>. It is called
[<cite>ffsimple.fmt</cite>](http://www.opendap.org/examples/ffsimple.fmt):

    ASCII_file_header "Latitude/Longitude Limits"
    minmax_title 1 24 char 0
    latitude_min 25 36 double 6
    latitude_max 37 46 double 6
    longitude_min 47 59 double 6
    longitude_max 60 70 double 6

    ASCII_data "lat/lon"
    latitude 1 10 double 6
    longitude 12 22 double 6
    temp 24 30 double 4
    salt 32 38 double 4

    ASCII_output_data "output"
    latitude 1 10 double 3
    longitude_deg 11 15 short 0
    longitude_min 16 19 short 0
    longitude_sec 20 23 short 0
    salt 31 40 double 2
    temp 41 50 double 2

The format file consists of three sections. The first shows OPeNDAP
FreeForm ND\\ how to parse the file header. The second section describes
the contents of the data file. The third part describes how to write the
data to another file. This part is not important for the OPeNDAP
FreeForm ND Data Handler , but is useful for debugging the input
descriptions.

Download the <font color='green'>ffsimple</font> files described above,
and type:

    > newform ffsimple.dat

You should see results like this:

    Welcome to Newform release 4.2.3 -- an NGDC FreeForm ND application

    (ffsimple.fmt) ASCII_input_file_header  "Latitude/Longitude Limits"
    File ffsimple.dat contains 1 header record (71 bytes)
    Each record contains 6 fields and is 71 characters long.

    (ffsimple.fmt) ASCII_input_data "lat/lon"
    File ffsimple.dat contains 10 data records (390 bytes)
    Each record contains 5 fields and is 39 characters long.

    (ffsimple.fmt) ASCII_output_data        "output"
    Program memory contains 10 data records (510 bytes)
    Each record contains 7 fields and is 51 characters long.


    -47.304 -176   9  40            34.46     11.71
    -25.928    0 -46  38            35.90     20.73
    -28.287   35  35  31            35.33     23.64
    12.588  149  24  29            34.53     28.66
    -63.224   55  19  11            33.88      0.45
    54.118 -136  56  26            32.07     10.41
    -38.819   91  24  41            35.02     14.00
    -34.577   30  10  20            35.47     20.91
    27.332 -155  14   1            35.27     23.09
    11.625 -113  39  38            33.70     27.50
    100\

Now take both the <font color='green'>ffsimple</font> files and put them
into a directory in your web server's document root directory. (Refer to
the
\[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The
OPeNDAP User Guide</cite>\] for some tips on figuring out where that
is.)

Here's an example, on a computer on which the web server document root
is <font color='green'>/export/home/http/htdocs</font>:

    > mkdir /export/home/http/htdocs/data
    > cp ffsimple.* /export/home/http/htdocs/data

Now, using a common web browser such as Netscape Navigator, enter the
following URL (substitute your machine name and CGI directory for the
ones in the example):

    http://test.opendap.org/opendap/nph-dods/data/ff/ffsimple.dat.asc

You should get something like the following in your web browser's
window:

    latitude, longitude, temp, salt
    -47.3035, -176.161, 11.7125, 34.4634
    -25.928, -0.777265, 20.7288, 35.8953
    -28.2867, 35.5919, 23.6377, 35.3314
    12.5882, 149.408, 28.6583, 34.526
    -63.2235, 55.3196, 0.4503, 33.883
    54.1183, -136.941, 10.4085, 32.0661
    -38.8188, 91.4113, 13.9978, 35.0173
    -34.5771, 30.1721, 20.9096, 35.4705
    27.3316, -155.234, 23.0917, 35.2694
    11.625, -113.661, 27.5036, 33.7004

Try this URL:

    http://test.opendap.org/opendap/nph-dods/data/ffsimple.dat.dds

This will show a description of the dataset structure (See
\[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The
OPeNDAP User Guide</cite>\] for a detailed description of the DAP2
"Dataset Description Structure," or DDS.):

     Dataset {
        Sequence {
            Float64 latitude;
            Float64 longitude;
            Float64 temp;
            Float64 salt;
        } lat/lon;
    } ffsimple;

### Array Data

If your data more naturally comes in arrays, you can still use the
OPeNDAP FreeForm ND Data Handler to serve your data. The OPeNDAP
FreeForm ND format for sequence data is somewhat simpler than the format
for array data, so you may find it easier to begin with the example in
the previous section.

#### One-dimensional Arrays

Here is a data file, called
[<cite>ffarr1.dat</cite>](http://www.opendap.org/examples/ffarr1.dat),
containingfour ten-element vectors:

     123456789012345678901234567
     1.00  50.00 0.1000  1.1000
     2.00  61.00 0.3162  0.0953
     3.00  72.00 0.5623 -2.3506
     4.00  83.00 0.7499  0.8547
     5.00  94.00 0.8660 -0.1570
     6.00 105.00 0.9306 -1.8513
     7.00 116.00 0.9647  0.6159
     8.00 127.00 0.9822 -0.4847
     9.00 138.00 0.9910 -0.7243
    10.00 149.00 0.9955 -0.3226

Here is a format file to read this data
([<cite>ffarr1.fmt</cite>](http://www.opendap.org/examples/ffarr1.fmt)):

    ASCII_input_data "simple array format"
    index 1 5 ARRAY["line" 1 to 10 sb 23] OF float 1
    data1 6 12 ARRAY["line" 1 to 10 sb 21] OF float 1
    data2 13 19 ARRAY["line" 1 to 10 sb 21] OF float 1
    data3 20 27 ARRAY["line" 1 to 10 sb 20] OF float 1

    ASCII_output_data "simple array output"
    index 1 7 ARRAY["line" 1 to 10] OF float 0
    /data1 6 12 ARRAY["line" 1 to 10 sb 21] OF float 1
    /data2 13 19 ARRAY["line" 1 to 10 sb 21] OF float 4
    /data3 20 27 ARRAY["line" 1 to 10 sb 20] OF float 4

The output section is not essential for the OPeNDAP FreeForm ND Data
Handler , but is included so you can check out the data with the
<font color='green'>newform</font> command.

Download the files from the OPeNDAP web site, and try typing:

    > newform ffarr1.dat

You should see the <font color='green'>index</font> array printed out.
Uncomment different lines in the output section of the example file to
see different data vectors.

Now look a little closer at the input section of the file:

    index 1 5 ARRAY["line" 1 to 10 sb 23] OF float 1

This line says that the array in question---called "index"---starts in
column one of the first line, and each element takes up five bytes. The
first element starts in column one and goes into column five. The array
has one dimension, "line", and is composed of floating point data. The
remaining elements of this array are found by skipping the next 23 bytes
(the newline counts as a character), reading the following five bytes,
skipping the next 23 bytes, and so on.

Of course, the 23 bytes skipped in between the
<font color='green'>index</font> array elements also contain data from
other arrays. The second array, <font color='green'>data1</font>, starts
in column 6 of line one, and has 21 bytes between values. The third
array starts in column 13 of the first line, and the fourth starts in
column 20.

Move the <font color='green'>ffarr1.\*</font> files into your data
directory:

    > cp ffarr1.* /export/home/http/htdocs/data

Now you can look at this data the same way you looked at the sequence
data. Request the DDS for the dataset with a URL like this one:

    http://test.opendap.org/opendap/nph-dods/data/ffarr1.dat.dds

You can see that the dataset is a collection of one-dimensional vectors.
You can see the individual vectors with a URL like this:

    http://test.opendap.org/opendap/nph-dods/data/ffarr1.dat.asc?index

#### Multi-dimensional Arrays

Here's another example, with a two-dimensional array.
([<cite>ffarr2.dat</cite>](http://www.opendap.org/examples/ffarr2.dat)):


              1         2         3         4
    1234567890123456789012345678901234567890
      1.00  2.00  3.00  4.00  5.00  6.00
      7.00  8.00  9.00 10.00 11.00 12.00
     13.00 14.00 15.00 16.00 17.00 18.00
     19.00 20.00 21.00 22.00 23.00 24.00
     25.00 26.00 27.00 28.00 29.00 30.00

There are no spaces between the data columns within an array row, but in
order to skip reading the newline character, we have to skip one
character at the end of each row. Here is a format file to read this
data
([<cite>ffarr2.fmt</cite>](http://www.opendap.org/examples/ffarr2.fmt)):

    ASCII_input_data "one"
    data 1 6 ARRAY["y" 1 to 5 sb 1]["x" 1 to 6] OF float 1

    ASCII_output_data "two"
    data 1 4 ARRAY["x" 1 to 6 sb 2]["y" 1 to 5] OF float 1

Again, the output section is only for using with the
<font color='green'>newform</font> tool. Put these data files into your
<font color='green'>htdocs</font> directory, and look at the DDS as you
did with the previous example.

#### A Little More Complicated

You can use the OPeNDAP FreeForm ND Data Handler to serve data with
multi-dimensional arrays and one-dimensional vectors interspersed among
one another. Here's a file containing this kind of data
([<cite>ffarr3.dat</cite>](http://www.opendap.org/examples/ffarr3.dat)):


    1         2         3         4
    1234567890123456789012345678901234567890123
    XXXX  1.00  2.00  3.00  4.00  5.00  6.00YY
    XXXX  7.00  8.00  9.00 10.00 11.00 12.00YY
    XXXX 13.00 14.00 15.00 16.00 17.00 18.00YY
    XXXX 19.00 20.00 21.00 22.00 23.00 24.00YY
    XXXX 25.00 26.00 27.00 28.00 29.00 30.00YY

In order to read this file successfully, we define three vectors to read
the "XXXX", the "YY", and the newline. Here is a format file that does
this
([<cite>ffarr3.fmt</cite>](http://www.opendap.org/examples/ffarr3.fmt)):

    dBASE_input_data "one"
    headers 1 4 ARRAY["line" 1 to 5 sb 39] OF text 0
    data 5 10 ARRAY["y" 1 to 5 sb 7]["x" 1 to 6] OF float 1
    trailers 41 42 ARRAY["line" 1 to 5 sb 41] OF text 0
    newline 43 43 ARRAY["line" 1 to 5 sb 42] OF text 0

    ASCII_output_data "two"
    data 1 4 ARRAY["x" 1 to 6 sb 2]["y" 1 to 5] OF float 0
    /headers 1 6 ARRAY["line" 1 to 5] OF text 0
    /trailers 1 4 ARRAY["line" 1 to 5] OF text 0
    /newline 1 4 ARRAY["line" 1 to 5] OF text 0

The following chapters offer more detailed information about how exactly
to create a format description file.

#### Non-interleaved Multi-dimensional Arrays

So far the array examples have shown how to read interleaved arrays
(either vectors or higher dimensional arrays). Reading array data where
one array follows another is pretty straightforward. Use the same syntax
as for the interleaved array case, but set the start and stop points to
be *the same* and to be the offset from the start of the data file. Here
is a format file for a real dataset that contains a number of arrays of
binary data:

    BINARY_input_data "AMSR-E_Ocean_Product"
    time_a 1 1 array["lat" 1 to 720]["lon" 1 to 1440] OF uint8 0
    sst_a 1036801 1036801 array["lat" 1 to 720]["lon" 1 to 1440] OF uint8 0
    wind_a 2073601 2073601 array["lat" 1 to 720]["lon" 1 to 1440] OF uint8 0

Note that the array *time_a* uses start and stop values of *1* and then
the array *sst_a* uses start and stop values of *1 036 801* which is
exactly the size of the preceding array. Note that in this dataset, each
array is of an unsigned 8-bit integer. Here's another example with
different size and type arrays:

    BINARY_input_data "test_data"
    time_a 1 1 array["lat" 1 to 10]["lon" 1 to 10] OF uint8 0
    sst_a 101 108 array["lat" 1 to 10]["lon" 1 to 20] OF float64 0
    wind_a 301 302 array["lat" 1 to 10]["lon" 1 to 5] OF uint16 0

The first array starts at offset 1; the second array starts at offset
100 (10 \* 10); and the third array starts at 300 (100 + (10 \* 20).
Note that FreeForm offsets are given in terms of *elements*, not bytes.