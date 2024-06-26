[return to FreeForm](FreeForm "wikilink")

# Header Formats

Headers are one of the most commonly encountered forms of metadata-data
about data. Applications need the information contained in headers for
reading the data that the headers describe. To access these data,
applications must be able to read the headers. Just as there are many
data formats, there are numerous header formats. You can include header
format descriptions, which have exactly the same form as data format
descriptions, in format description files.

## Header Treatment in FreeForm ND

FreeForm ND is not 100 percent backwards compatible with FreeForm in the
area of header treatment.

Headers have traditionally been handled differently from data in
FreeForm ND. If a header format was not specified as either input or
output, it was taken as both input and output.
<font color='green'>newform</font> did little in processing headers, and
FreeForm ND relied on extraneous utilities to work with headers.

### New Behavior

In FreeForm ND, header formats are treated the same as data formats.
This means that header formats must be identified as either input or
output, explicitly or implicitly. If done explicitly, then either the
input or the output descriptor will form the format type (e.g.,
<font color='green'>ASCII_input_header</font>). If done implicitly, then
the same ambiguity resolution rules that apply to data formats will be
applied to header formats. This means that ASCII header formats will be
taken as input for data files with a <font color='green'>.dat</font>
extension, dBASE header formats will be taken as input for data files
with a <font color='green'>.dab</font> extension, and binary header
formats will be taken as input for all other data files.

If an embedded header and the data have different file types, then
either the header format or data format (preferably both) must be
explicitly identified as input or output (for example, an ASCII header
embedded in a binary data file). Obviously, ambiguous formats with
different file types cannot both be resolved as input formats.

The same header format is no longer used as both an input and an output
header format.

In FreeForm ND, <font color='green'>newform</font> honors output header
formats that are separate (e.g.,
<font color='green'>ASCII_output_header_separate</font>). The header is
written to a separate file which, unless otherwise specified, is named
after the output data file with a <font color='green'>.hdr</font>
extension. This requires that you name the output file using the
<font color='green'>-o</font> option flag; redirected output cannot be
used with separate output headers. The output header file name and path
can be specified using the same keywords that tell FreeForm ND how to
find an input separate header file (i.e.,
<font color='green'>header_file_ext</font>,
<font color='green'>header_file_name</font>, and
<font color='green'>header_file_path</font>).

When defining keywords to specify how an output header file is to be
named, you must use a new type of equivalence section,
<font color='green'>input_eqv</font>, which must appear in the format
file along with <font color='green'>output_eqv</font>.

## Header Types

FreeForm ND recognizes two types of headers. File headers describe all
the data in a file whereas record headers describe the data in a single
record or data block. FreeForm ND can read headers included in the data
file or stored in a separate file. Header formats, like data formats,
are described in format description files.

### File Headers

A file header included in a data file is at the beginning of the file.
Only one file header can be associated with a data file. Alternatively,
a file header can be stored in a file separate from the data file.

In the following example, a file header is used to store the minimum and
maximum for each variable and the data are converted from ASCII to
binary. There are two variables, latitude and longitude. The file header
format and data formats are described in the format description file
<font color='green'>llmaxmin.fmt</font>.

Here is <font color='green'>llmaxmin.fmt</font>:

    ASCII_file_header "Latitude/Longitude Limits"
    minmax_title 1 24 char 0
    latitude_min 25 36 double 6
    latitude_max 37 46 double 6
    longitude_min 47 59 double 6
    longitude_max 60 70 double 6

    ASCII_data "lat/lon"
    latitude 1 10 double 6
    longitude 12 22 double 6

    binary_data "lat/lon"
    latitude 1 4 long 6
    longitude 5 8 long 6

The example ASCII data file <font color='green'>llmaxmin.dat</font>
contains a file header and data as described in
<font color='green'>llmaxmin.fmt</font>.

<font color='green'>llmaxmin.dat</font>:


    1         2         3         4         5         6         7
    1234567890123456789012345678901234567890123456789012345678901234567890

    Latitude and Longitude:   -83.223548 54.118314  -176.161101 149.408117
    -47.303545 -176.161101
    -25.928001    0.777265
    -28.286662   35.591879

    12.588231  149.408117
    -83.223548   55.319598

    54.118314 -136.940570

    38.818812   91.411330
    -34.577065   30.172129

    27.331551 -155.233735

    11.624981 -113.660611

This use of a file header would be appropriate if you were interested in
creating maps from large data files. By including maximums and minimums
in a header, the scale of the axes can be determined without reading the
entire file.

FreeForm ND naming conventions have been followed in this example, so to
convert the ASCII data in the example to binary format, use the
following simple command:

    newform llmaxmin.dat -o llmaxmin.bin

The file header in the example will be written into the binary file as
ASCII text because the header descriptor in
<font color='green'>llmaxmin.fmt</font>
(<font color='green'>ASCII_file_header</font>) does not specify
read/write type, so the format is used for both the input and output
header.

### Record Headers

Record headers occur once for every block of data in a file. They are
interspersed with the data, a configuration sometimes called a format
sandwich. Record headers can also be stored together in a separate file.

The following format description file specifies a record header and
ASCII and binary data formats for aeromagnetic trackline data.

Here is <font color='green'>aeromag.fmt</font>:

    ASCII_record_header "Aeromagnetic Record Header Format"
    flight_line_number 1 5 long 0
    count 6 13 long 0
    fiducial_number_corresponding_to_first_logical_record 14 22 long 0
    date_MMDDYY_or_julian_day 23 30 long 0
    flight_number 31 38 long 0
    utm_easting_of_first_record 39 48 float 0
    utm_northing_of_first_record 49 58 float 0
    utm_easting_of_last_record 59 68 float 0
    utm_northing_of_last_record 69 78 float 0
    blank_padding 79 104 char 0

    ASCII_data "Aeromagnetic ASCII Data Format"
    flight_line_number 1 5 long 0
    fiducial_number 6 15 long 0
    utm_easting_meters 16 25 float 0
    utm_northing_meters 26 35 float 0
    mag_total_field_intensity_nT 36 45 long 0
    mag_residual_field_nT 46 55 long 0
    alt_radar_meters 56 65 long 0
    alt_barometric_meters 66 75 long 0
    blank 76 80 char 0
    latitude 81 92 float 6
    longitude 93 104 float 6

    binary_data "Aeromagnetic Binary Data Format"
    flight_line_number 1 4 long 0
    fiducial_number 5 8 long 0
    utm_easting_meters 9 12 long 0
    utm_northing_meters 13 16 long 0
    mag_total_field_intensity_nT 17 20 long 0
    mag_residual_field_nT 21 24 long 0
    alt_radar_meters 25 28 long 0
    alt_barometric_meters 29 32 long 0
    blank 33 37 char 0
    latitude 38 41 long 6
    longitude 42 45 long 6

The example ASCII file <font color='green'>aeromag.dat</font> contains
two record headers followed by a number of data records. The header and
data formats are described in <font color='green'>aeromag.fmt</font>.
The variable count (second variable defined in the header format
description) is used to indicate how many data records occur after each
header.

<font color='green'>aeromag.dat</font>:


    1         2         3         4         5         6         7         8         9         10
    123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345

    420       5     5272     178       2   413669.  6669740.   333345.  6751355.

    420      5272   413669.  6669740.   2715963   2715449      1088      1348        60.157307 -154.555191

    420      5273   413635.  6669773.   2715977   2715464      1088      1350        60.157593 -154.555817

    420      5274   413601.  6669807.   2716024   2715511      1088      1353        60.157894 -154.556442

    420      5275   413567.  6669841.   2716116   2715603      1079      1355        60.158188 -154.557068

    420      5276   413533.  6669875.   2716263   2715750      1079      1358        60.158489 -154.557693

    411      10     8366     178       2   332640.  6749449.   412501.  6668591.

    411      8366   332640.  6749449.   2736555   2736538       963      1827        60.846806 -156.080185

    411      8367   332674.  6749415.   2736539   2736522       932      1827        60.846516 -156.079529

    411      8368   332708.  6749381.   2736527   2736510       917      1829        60.846222 -156.078873

    411      8369   332742.  6749347.   2736516   2736499       922      1832        60.845936 -156.078217

    411      8370   332776.  6749313.   2736508   2736491       946      1839        60.845642 -156.077560

    411      8371   332810.  6749279.   2736505   2736488       961      1846        60.845348 -156.076904

    411      8372   332844.  6749245.   2736493   2736476       982      1846        60.845062 -156.076248

    411      8373   332878.  6749211.   2736481   2736463      1015      1846        60.844769 -156.075607

    411      8374   332912.  6749177.   2736470   2736452      1029      1846        60.844479 -156.074951

    411      8375   332946.  6749143.   2736457   2736439      1041      1846        60.844189 -156.074295

This file contains two record headers. The first occurs on the first
line of the file and has a count of 5, so it is followed by 5 data
records. The second record header follows the first 5 data records. It
has a count of 10 and is followed by 10 data records.

The FreeForm ND default naming conventions have been used here so you
could use the following abbreviated command to reformat
<font color='green'>aeromag.dat</font> to a binary file named
<font color='green'>aeromag.bin</font>:

    newform aeromag.dat -o aeromag.bin

The ASCII record headers are written into the binary file as ASCII text.

### Separate Header Files

You may need to describe a data set with external headers. An external
or separate header file can contain only headers-one file header or
multiple record headers.

#### Separate File Header

Suppose you want the file header used to store the minimum and maximum
values for latitude and longitude (from the llmaxmin example) in a
separate file so that the data file is homogenous, thus easier for
applications to read. Instead of one ASCII file
(<font color='green'>llmaxmin.dat</font>), you will have an ASCII header
file, say it is named <font color='green'>llmxmn.hdr</font>, and an
ASCII data file-call it <font color='green'>llmxmn.dat</font>.

Here is <font color='green'>llmxmn.hdr</font>:

    Latitude and Longitude:   -83.223548 54.118314  -176.161101 149.408117

And here is <font color='green'>llmxmn.dat</font>:

    -47.303545 -176.161101
    -25.928001    0.777265
    -28.286662   35.591879

    12.588231  149.408117
    -83.223548   55.319598

    54.118314 -136.940570

    38.818812   91.411330
    -34.577065   30.172129

    27.331551 -155.233735

    11.624981 -113.660611

You will need to make one change to
<font color='green'>llmaxmin.fmt</font>, adding the qualifier separate
to the header descriptor, so that FreeForm ND will look for the header
in a separate file. The first line of
<font color='green'>llmaxmin.fmt</font> becomes:

    ASCII_file_header_separate "Latitude/Longitude Limits"

Save <font color='green'>llmaxmin.fmt</font> as
<font color='green'>llmxmn.fmt</font> after you make the change.

To convert the data in <font color='green'>llmxmn.dat</font> to binary
format in <font color='green'>llmxmn.bin</font>, use the following
command:

    newform llmxmn.dat -o llmxmn.bin

> NOTE: When you run <font color='green'>newform</font>, it will write
> the separate header to <font color='green'>llmxmn.bin</font> along
> with the data in <font color='green'>llmxmn.dat</font>.

#### Separate Record Headers

Record headers in separate files can act as indexes into data files if
the headers specify the positions of the data in the data file. For
example, if you have a file containing data from 25 observation
stations, you could effectively index the file by including a station ID
and the starting position of the data for that station in each record
header. Then you could use the index to quickly locate the data for a
particular station.

Returning to the <font color='green'>aeromag</font> example, suppose you
want to place the two record headers in a separate file. Again, the only
change you need to make to the format description file
(<font color='green'>aeromag.fmt</font>) is to add the qualifier
separate to the header descriptor. The first line would then be:

    ASCII_record_header_separate "Aeromagnetic Record Header Format"

The separate header file would contain the following two lines:

    420       5     5272     178       2   413669.  6669740.   333345.  6751355.
    411      10     8366     178       2   332640.  6749449.   412501.  6668591.

The data file would look like the current
<font color='green'>aeromag.dat</font> with the first and seventh lines
removed.

Assuming the data file is named <font color='green'>aeromag.dat</font>,
the default name and location of the header file would be
<font color='green'>aeromag.hdr</font> in the same directory as the data
file. Otherwise, the separate header file name and location need to be
defined in an equivalence table. (For information about equivalence
tables, see the GeoVu Tools Reference Guide.)

### The dBASEfile Format

Headers and data records in dBASE format are represented in ASCII but
are not separated by end-of-line characters. They can be difficult to
read or to use in applications that expect newlines to separate records.
By using <font color='green'>newform</font>, dBASE data can be
reformatted to include end-of-line characters.

In this example, you will reformat the dBASE data file
<font color='green'>oceantmp.dab</font> (see below) into the ASCII data
file <font color='green'>oceantmp.dat</font>. The input file
<font color='green'>oceantmp.dab</font> contains a record header at the
beginning of each line. The header is followed by data on the same line.
When you convert the file to ASCII, the header will be on one line
followed by the data on the number of lines specified by the variable
count. The format description file
<font color='green'>oceantmp.fmt</font> is used for this reformatting.

Here is <font color='green'>oceantmp.fmt</font>:

    dbase_record_header "NODC-01 record header format"
    WMO_quad 1 1 char 0
    latitude_deg_abs 2 3 uchar 0
    latitude_min 4 5 uchar 0
    longitude_deg_abs 6 8 uchar 0
    longitude_min 9 10 uchar 0
    date_yymmdd 11 16 long 0
    hours 17 19 uchar 1
    country_code 20 21 char 0
    vessel 22 23 char 0
    count 24 26 short 0
    data_type_code 27 27 char 0
    cruise 28 32 long 0
    station 33 36 short 0

    dbase_data "IBT input format"
    depth_m 1 4 short 0
    temperature 5 8 short 2

    RETURN "NEW LINE INDICATOR"

    ASCII_data "ASCII output format"
    depth_m 1 5 short 0
    temperature 27 31 float 2

This format description file contains a header format description, a
description for dBASE input data, the special RETURN descriptor, and a
description for ASCII output data. The variable *count* (fourth from the
bottom in the header format description) indicates the number of data
records that follow each header. The descriptor RETURN lets
<font color='green'>newform</font> skip over the end-of-line marker at
the end of each data block in the input file
<font color='green'>oceantmp.dab</font> as it is meaningless to
<font color='green'>newform</font> here. Because the end-of-line marker
appears at the end of the data records in each input data block, RETURN
is placed after the input data format description in the format
description file.

<font color='green'>oceantmp.dab</font>:


    1         2         3         4         5         6         7
    1234567890123456789012345678901234567890123456789012345678901234567890
    11000171108603131109998  4686021000000002767001027670020276700302767
    110011751986072005690AM  4686091000000002928001028780020287200302872
    11111176458102121909998  4681011000000002728009126890241110005000728
    112281795780051918090PI  268101100000000268900402711

Each dBASE header in <font color='green'>oceantmp.dab</font> is located
from position 1 to 36. It is followed by four data records of 8 bytes
each. Each record comprises a depth and temperature reading. The
variable count in the header (positions 24-26) indicates that there are
4 data records each in the first 3 lines and 2 on the last line. This
will all be more obvious after conversion.

To reformat <font color='green'>oceantmp.dab</font> to ASCII, use the
following command:

    newform oceantmp.dab -o oceantmp.dat

The resulting file <font color='green'>oceantmp.dat</font> is much
easier to read. It is readily apparent that there are 4 data records
after the first three headers and 2 after the last.

Here is <font color='green'>oceantmp.dat</font>:


    1         2         3         4
    1234567890123456789012345678901234567890
    11000171108603131109998  46860210000

    0                     27.67

    10                     27.67

    20                     27.67

    30                     27.67
    110011751986072005690AM  46860910000

    0                     29.28

    10                     28.78

    20                     28.72

    30                     28.72
    11111176458102121909998  46810110000

    0                     27.28

    91                     26.89

    241                     11.00

    500                     07.28
    112281795780051918090PI  26810110000

    0                     26.89

    40                     27.11