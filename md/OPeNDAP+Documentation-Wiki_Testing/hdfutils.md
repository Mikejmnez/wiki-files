# HDF Utilities

FreeForm ND includes three utilities for use with HDF (hierarchical data
format) files: <font color='green'>makehdf</font>,
<font color='green'>splitdat</font>, and
<font color='green'>pntshow</font>. These programs were built using both
the FreeForm library and the HDF library, which was developed at the
National Center for Supercomputer Applications (NCSA).

The <font color='green'>makehdf</font> program converts binary and ASCII
data files to HDF files and converts multiplexed band interleaved by
pixel image files into a series of single parameter files. The
<font color='green'>splitdat</font> program is used to separate and
reformat data files containing headers and data into separate header and
data files, or to translate them into HDF files. The
<font color='green'>pntshow</font> program extracts point data from HDF
files into binary or ASCII format.

It is assumed in this chapter that you have a working familiarity with
HDF terminology and conventions. See the HDF user documentation for
detailed information.

> NOTE: Do not try the examples in this chapter. The example file set is
> incomplete.

## <font color='green'>makehdf</font>

Using <font color='green'>makehdf</font> you can convert data files with
formats described in a FreeForm format file into HDF files. You should
follow FreeForm naming conventions for the data and format files. For
details about FreeForm conventions, see ([Chapter
8](Wiki_Testing/convs "wikilink")).

> A dBASE input file must be converted to ASCII or binary using
> <font color='green'>newform</font> before you can run
> <font color='green'>makehdf</font> on it.

The HDF file resulting from a conversion consists either of a group of
scientific datasets (SDS's), one for each variable in the input data
file, or of a *vgroup* containing all the variables as one *vdata*. If
you are working with grid data, you will want SDS's (the default) in the
output HDF file. A vdata (<font color='green'>-vd</font> option) is the
appropriate choice for point data.

The <font color='green'>makehdf</font> command has the following form:

        makehdf input_file [-r rows] [-c columns] [-v var_file] [-d HDF_description_file]

        [-xl x_label -yl y_label] [-xu x_units -yu y_units] [-xf x_format -yf y_format] [-id file_id] [-vd [vdata_file]] [-dmx [-sep]] [-df] [-md missing_data_file] [-dof HDF_file]

*input_file* :

Name of the input data file. Following FreeForm naming conventions, the
standard extensions for data files are <font color='green'>.dat</font>
for ASCII format and <font color='green'>.bin</font> for binary.

-r rows :

Option flag followed by the number of rows in each resulting scientific
dataset. The number of rows must be specified through this option on the
command line, or in an equivalence table, or in a header
(<font color='green'>.hdr</font>) file defined according to FreeForm
standards.

-c columns :

Option flag followed by the number of columns in each resulting
scientific dataset. The number of columns must be specified through this
option on the command line, or in an equivalence table, or in a header
(<font color='green'>.hdr</font>) file defined according to FreeForm
standards. For information about equivalence tables, see the GeoVu Tools
Reference Guide.

-v var_file :

Option flag followed by the name of the variable file. The file contains
names of the variables in the input data file to be processed by
<font color='green'>makehdf</font>. Variable names in \var{var_file} can
be separated by one or more spaces or each name can be on a separate
line.

-d HDF_description_file :

Option flag followed by the name of the file containing a description of
the input file. The description will be stored as a file annotation in
the resulting HDF file.

-xl x_label -yl y_label :

Option flags followed by strings (labels) describing the x and y axes;
labels must be in quotes (<font color='green'>" "</font>) if more than
one word.

-xu x_units -yu y_units :

Option flags followed by strings indicating the measurement units for
the x and y axes; strings must be in quotes (<font color='green'>"
"</font>) if more than one word.

\- xf x_format -yf y_format :

Option flags followed by strings indicating the formats to be used in
displaying scale for the x and y dimensions; strings must be in quotes
(<font color='green'>" "</font>) if more than one word.

\- id file_id :

Option flag followed by a string that will be stored as the ID of the
resulting HDF file.

\- vd \[vdata_file\] :

Option flag indicating that the output HDF file should contain a vdata.
The optional file name specifies the name of the output HDF file; the
default is <font color='green'>input_file.HDF</font>.

\- dmx \[-sep\] :

The option flag <font color='green'>-dmx</font> indicates that input
data should be demultiplexed from band interleaved by pixel to band
sequential form in <font color='green'>input_file.dmx</font>. If
<font color='green'>-dmx</font> is followed by
<font color='green'>-sep</font>, the input data are demultiplexed into
separate variable files called <font color='green'>data_file.1</font>
\ldots <font color='green'>data_file.n</font>

\- df :

To use this option, the input file
(<font color='green'>data_file.ext</font>) must be a binary
demultiplexed (band sequential) file. For each input variable in the
applicable FreeForm format description file, there is a corresponding
demultiplexed section in the output HDF file.

\- md missing_data_file :

Option flag followed by the name of the file defining missing data (data
you want to exclude). Use this option only along with the vdata (-vd)
option. Each line in the missing data file has the form:

    variable_name lower_limit upper_limit

The precision of the upper and lower limits matches the precision of the
input data.

\- dof HDF_file :

Option flag followed by the name of the output HDF file. If you do not
use the <font color='green'>-dof</font> option, the default output file
name is <font color='green'>input_file.HDF</font>.

### Example

You will use <font color='green'>makehdf</font> to store
<font color='green'>latlon.dat</font> as an HDF file. The HDF file will
consist of two SDS's, one each for the two variables latitude and
longitude. Each SDS will have four rows and five columns.

To convert <font color='green'>latlon.dat</font> to an HDF file, enter
the following command:

    makehdf latlon.dat -r 4 -c 5

As <font color='green'>makehdf</font> translates
<font color='green'>latlon.dat</font> into HDF, processing information
is displayed on the screen:

    1   Caches (1150 bytes) Processed: 800 bytes written to latlon.dmx
    Writing latlon.HDF and calculating maxima and minima ...

    Variable latitude:
    Minimum: -86.432712  Maximum 89.170904
    Variable longitude:
    Minimum: -176.161101  Maximum 165.066193

The output from <font color='green'>makehdf</font> is an HDF file named
<font color='green'>latlon.HDF</font> (by default). It contains the
minimum and maximum values for the two variables as well as the two
SDS's.

A temporary file named <font color='green'>latlon.dmx</font> was also
created. It contains the data from latlon.dat in demultiplexed form .
The data was converted from its original multiplexed form to enable
<font color='green'>makehdf</font> to write sections of data to SDS's.

If you start with a demultiplexed file such as
<font color='green'>latlon.dmx</font>, the translation process is much
quicker, particularly for large data files. As an illustration, try
this. Rename <font color='green'>latlon.dmx</font> to
<font color='green'>latlon.bin</font> (renaming is necessary for
<font color='green'>makehdf</font> to find the format description file
<font color='green'>latlon.fmt</font> by default). Enter the following
command:

    makehdf latlon.bin -df -r 4 -c 5

The output file again is <font color='green'>latlon.HDF</font>, but
notice that no demultiplexing was done.

## <font color='green'>splitdat</font>

The <font color='green'>splitdat</font> program translates files with
headers and data into separate header and data files or into HDF files.
If the translation is to separate header and data files, the header file
can include indexing information.

The combination of header and data records in a file is often used for
point data sets that include a number of observations made at one or
more stations or locations in space. The header records contain
information about the stations or locations of the measurements. The
data records hold the observational data. A station record usually
indicates how many data records follow it. The structure of such a file
is similar to the following:

    Header for Station 1
    Observation 1 for Station 1
    Observation 2 for Station 1

    .

    .
    Observation N for Station 1

    Header for Station 2
    Observation 1 for Station 2
    Observation 2 for Station 2

    .

    .

    .
    Observation N for Station 2

    Header for Station 3

    .

    .

    .

Many applications have difficulty reading this sort of heterogeneous
data file. One solution is to split the data into two homogeneous files,
one containing the headers, the other containing the data. With
<font color='green'>splitdat</font>, you can easily create the separate
data and header files. To use <font color='green'>splitdat</font> for
this purpose, the input and output formats for the record headers and
the data must be described in a FreeForm format description file. To use
<font color='green'>splitdat</font> for translating files to HDF, the
input format must be described in a FreeForm format description file.
You should follow FreeForm naming conventions for the data and format
files. For details about FreeForm conventions, see ([<cite>
ff,convs</cite>](http://www)).

The <font color='green'>splitdat</font> command has the following form:

\proto{<font color='green'>splitdat</font> \var{input_file}
\[\var{output_data_file} \> \var{output_header_file}\]}

> \var{input_file} :
>
> Name of the file to be processed. Following FreeForm naming
> conventions, the standard extensions for data files are
> <font color='green'>.dat</font> for ASCII format and
> <font color='green'>.bin</font> for binary.
>
> \var{output_data_file} :
>
> Name of the output file into which data are transferred with the
> format specified in the applicable FreeForm format description file.
> The standard extensions are the same as for input files. If an output
> file name is not specified, the default is standard output.
>
> \var{output_header_file} :
>
> Name of the output file into which headers from the input file are
> transferred with the format specified in the applicable FreeForm
> format description file. If an output header file name is not
> specified, the default is standard output.

### Index Creation

You can use the two variables begin and extent (described below) in the
format description for the output record headers to indicate the
location and size of the data block associated with each record header.
If you then use <font color='green'>splitdat</font>, the header file
that results can be used as an index to the data file.

> <font color='green'>begin</font> :
>
> Indicates the offset to the beginning of the data associated with a
> particular header. If the data is being translated to HDF, the units
> are records; if not, the units are bytes.
>
> <font color='green'>extent</font> :
>
> Indicates the number of records (HDF) or bytes (non-HDF) associated
> with each header record.

#### Example

You will use <font color='green'>splitdat</font> to extract the headers
and data from a rawinsonde (a device for gathering meteorological data)
ASCII data file named <font color='green'>hara.dat</font> (HARA =
Historic Arctic Rawinsonde Archive) and create two output
files-<font color='green'>23338.dat</font> containing the ASCII data and
<font color='green'>23338hdr.dat</font> containing the ASCII headers.
The format description file <font color='green'>hara.fmt</font> should
contain the necessary format descriptions.

Here is <font color='green'>hara.fmt</font>:

    ASCII_input_record_header "ASCII Location Record input format"
    WMO_station_ID_number 1 5 char 0
    latitude 6 10 long 2
    longitude_east 11 15 long 2
    year 17 18 uchar 0
    month 19 20 uchar 0
    day 21 22 uchar 0
    hour 23 24 uchar 0
    flag_processing_1 28 28 char 0
    flag_processing_2 29 29 char 0
    flag_processing_3 30 30 char 0
    station_type 31 31 char 0
    sea_level_elev 32 36 long 0
    instrument_type 37 38 uchar 0
    number_of_observations 40 42 ushort 0
    identification_code 44 44 char 0

    ASCII_input_data "Historical Arctic Rawinsonde Archive input format"
    atmospheric_pressure 1 5 long 1
    geopotential_height 7 11 long 0
    temperature_deg 13 16 short 0
    dewpoint_depression 18 20 short 0
    wind_direction 22 24 short 0
    wind_speed_m/s 26 28 short 0
    flag_qg 30 30 char 0
    flag_qg1 31 31 char 0
    flag_qt 33 33 char 0
    flag_qt1 34 34 char 0
    flag_qd 36 36 char 0
    flag_qd1 37 37 char 0
    flag_qw 39 39 char 0
    flag_qw1 40 40 char 0
    flag_qp 42 42 char 0
    flag_levck 43 43 char 0

    ASCII_output_record_header "ASCII Location Record output format"

    .

    .

    .

    ASCII_output_data "Historical Arctic Rawinsonde Archive output format"

    .

    .

    .

To "split" <font color='green'>hara.dat</font>, enter the following
command:

    splitdat hara.dat 23338.dat > 23338hdr.dat

The data values from <font color='green'>hara.dat</font> are stored in
<font color='green'>23338.dat</font> and the headers in
<font color='green'>23338hdr.dat</font>.

Because the variables begin and extent were used in the header output
format in <font color='green'>hara.fmt</font> to indicate data offset
and number of records, <font color='green'>23338hdr.dat</font> has two
columns of data showing offset and extent. Thus, it can serve as an
index into <font color='green'>23338.dat</font>.

### HDF Translation

If output files are not specified on the
<font color='green'>splitdat</font> command line, a file named
<font color='green'>input_file.HDF</font> is created. It is
hierarchically named and organized as follows:


    vgroup

    input_file_name

    /      \

    /        \

    vdata1       vdata2
    "PointIndex"      "input_file_name"

- <font color='green'>vdata1</font> contains the record headers
- <font color='green'>vdata2</font> contains the data
- If writing to a Vset (represented by a vgroup), both output

formats are converted to binary, if not binary already.

#### Example

To create the file <font color='green'>hara.HDF</font> from
<font color='green'>hara.dat</font>, enter the following abbreviated
command:

    splitdat hara.dat

The output formats in <font color='green'>hara.fmt</font> are
automatically converted to binary, and subsequently the ASCII data in
<font color='green'>hara.dat</font> are also converted to binary for HDF
storage.

## <font color='green'>pntshow</font>

The <font color='green'>pntshow</font> program is a versatile tool for
extracting point data from HDF files containing scientific datasets and
Vsets. The extraction can be done into any binary or ASCII format
described in a FreeForm format description file. Before using
<font color='green'>pntshow</font> on an HDF file, you should pack the
file using the NCSA-developed HDF utility hdfpack.

You can use <font color='green'>pntshow</font> to extract headers and
data from an HDF file into separate files or to extract just the data.
It's a good idea to define GeoVu keywords in an equivalence table to
facilitate access to HDF objects. For information about equivalence
tables, see the GeoVu Tools Reference Guide. The input and output
formats must be described in a FreeForm format description file. You
should follow FreeForm naming conventions for the data and format files.
For details about FreeForm conventions, see ([<cite>
ff,convs</cite>](http://www)).

If a format description file is not specified on the command line, the
output format is taken by default from the FreeForm output format
annotation stored in the HDF file. If there is no annotation, a default
ASCII output format is used.

> An equivalence table takes precedence over everything. (vdata=1963,
> SDS=702)

If you have not specified an HDF object in an equivalence table,
<font color='green'>pntshow</font> uses the following sequence to
determine the appropriate source for output:

1.  Output the first vdata with class name Data.
2.  Output the largest vdata.
3.  Output the first SDS.

If no vdatas exist in the file, but an SDS is found, it is extracted and
a default ASCII output format is used.

### Extracting Headers and Data

The <font color='green'>pntshow</font> command takes the following form
when you want to extract headers and data from HDF files into separate
files.

        pntshow input_HDF_file [-h [output_header_file]] [-hof output_header_format_file]

        [-hof output_header_format_file] [-d [output_data_file]] [-dof output_data_format_file]

> \var{input_HDF_file} :
>
> Name of the input HDF file, which has been packed using
> <font color='green'>hdfpack</font>.
>
> \hdfh :
>
> Option flag followed optionally by the name of the file designated to
> contain the record headers currently stored in a vdata with a class
> name of Index. If an output header file name is not specified, the
> default is standard output.
>
> \hdfhof :
>
> Option flag followed by the name of the FreeForm format file that
> describes the format for the headers extracted to standard output or
> output_header_file.
>
> \hdfd :
>
> Option flag followed optionally by the name of the file designated to
> contain the data currently stored in a vdata with a class name of
> Data. If an output file name is not specified, the default is standard
> output.
>
> \hdfdof :
>
> Option flag followed by the name of the FreeForm format file that
> describes the format for data extracted to standard output or
> \var{output_data_file}.

#### Example

You will extract data and headers from
<font color='green'>hara.HDF</font> (created by
<font color='green'>splitdat</font> in a previous example). This file
contains two vdatas: one has the class name Data and the other has the
class name Index. Because this file is extremely small, no appending
links were created in the file, so there is no need to pack the file
before using <font color='green'>pntshow</font>, though you can if you
wish.

To extract data and headers from <font color='green'>hara.HDF</font>,
enter the following command:

    pntshow hara.HDF -d haradata.dat -h harahdrs.dat

The data from the vdata designated as Data in
<font color='green'>hara.HDF</font> are now stored in
<font color='green'>haradata.dat</font>. The data are in their original
format because the original output format was stored by
<font color='green'>splitdat</font> in the HDF file. The header data
from the vdata designated as Index in
<font color='green'>hara.HDF</font> are now stored in
<font color='green'>harahdrs.dat</font>. In addition to the original
header data, the variables begin and extent have also been extracted to
<font color='green'>harahdrs.dat</font>.

### Extracting Data Only

The <font color='green'>pntshow</font> command takes the following form
when you want to extract just the data from an HDF file:

        pntshow input_HDF_file [-of default_output_format_file]

        [> output_file]

> \var{input_HDF_file} :
>
> Name of the input HDF file, which has been packed using
> <font color='green'>hdfpack</font>.
>
> \hdfof :
>
> Option flag followed by the name of the FreeForm format file that
> describes the format for data extracted to standard output or
> \var{output_file.}
>
> \var{output_file} :
>
> Name of the output file into which data is transferred. If an output
> file name is not specified, the default is standard output.

#### Examples

You can use <font color='green'>pntshow</font> to extract designated
variables from an HDF file. In this example, you will extract
temperature and pressure values from <font color='green'>hara.HDF</font>
to an ASCII format. First, the following format description file must
exist.

Here is <font color='green'>haradata.fmt</font>:

    ASCII_output_data "ASCII format for pressure, temp"
    atmospheric_pressure 1 10 long 1
    temperature_deg 15 25 float 1

To create a file named <font color='green'>temppres.dat</font>
containing only the temperature and pressure variables, enter either of
the following commands:

    pntshow hara.HDF -of haradata.fmt > temppres.dat

or

    pntshow hara.HDF -d temppres.dat -dof haradata.fmt

If you use the first command, <font color='green'>pntshow</font>
searches <font color='green'>hara.HDF</font> for a vdata named Data.
Since <font color='green'>hara.HDF</font> contains only one vdata named
<font color='green'>Data</font>, this vdata is extracted by default with
the format specified in <font color='green'>haradata.fmt</font>.

The results are the same if you use the second command. Now, try running
<font color='green'>pntshow</font> on the previously created file
<font color='green'>latlon.HDF</font>, which contains two SDS's. Use the
following command:

    pntshow latlon.HDF > latlon.SDS

The <font color='green'>latlon.SDS</font> file now contains the latitude
and longitude values extracted from
<font color='green'>latlon.HDF</font>. They have the default ASCII
output format. You could have used the -of option to specify an output
format included in a FreeForm format description file.