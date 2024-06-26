[return to FreeForm](FreeForm "wikilink")

# FreeForm ND Conventions

File name conventions have been defined for FreeForm ND. If you follow
these conventions, FreeForm ND can locate format files through a default
search sequence. Using the file name conventions also lets you reduce
the number of arguments on the command line. In addition to standard
file names, FreeForm ND programs recognize various standard command line
arguments.

## File Name Conventions

Naming conventions have been established for files accessed by FreeForm
ND. Although you are not required to follow these conventions, using
them lets you enter abbreviated commands when you are using FreeForm
ND-based programs. FreeForm ND can then automatically execute several
operations:

- Determination of input and output formats when they are not explicitly
  identified in the relevant format descriptions in format files
- Location of format files when they are not specified on the command
  line

## File Name Extensions

The expected extensions for data files are as follows:

> <font color='green'>.dat</font> : For ASCII, e.g., <font color='green'>latlon.dat</font>
>
> <!-- -->
>
> <font color='green'>.dab</font> : For dBASE, e.g., <font color='green'>latlon.dab</font>
>
> <!-- -->
>
> <font color='green'>.bin</font> : binary or anything that is not <font color='green'>.dat</font> or <font color='green'>.dab</font>, e.g., <font color='green'>latlon.bin</font>

The expected extension for format description files is
<font color='green'>.fmt</font>, e.g.,
<font color='green'>latlon.fmt</font>. You should not use mixed case
extensions for format description files if you want to take advantage of
FreeForm ND's default search capabilities. If you explicitly specify the
names of format description files on the command line, you can use mixed
case extensions.

> Previous versions of FreeForm ND used variable description files
> (formerly called format specification files) each of which contained
> variable descriptions for one file. Expected extensions for these
> files were <font color='green'>.afm</font> (ASCII),
> <font color='green'>.bfm</font> (binary), and
> <font color='green'>.dfm</font> (dBASE). Variable descriptions for one
> or more files can now be incorporated into a single format description
> file. It is recommended that you convert and combine (as appropriate)
> existing variable description files into format description files.

## File Name Relationships

FreeForm ND-based programs expect certain relationships between data
file and format description file names as outlined below.

- The data file is named datafile.ext where datafile is the file name of
  your choosing and ext is the extension. Example:
  <font color='green'>latlon.dat</font>
- The corresponding format description file should be named
  datafile.fmt. Example: <font color='green'>latlon.fmt</font>
- If one format description file is used for multiple data files, all
  with the same extension, the format description file should be named
  ext.fmt. Example: <font color='green'>ll.fmt</font> is the format
  description file for <font color='green'>lldat1.ll</font>,
  <font color='green'>lldat2.ll</font>, and
  <font color='green'>lldat3.ll</font>.

Again, although not required, it is to your advantage to use these
conventions.

## Determining Input and Output Formats

You can optionally include the read/write type
("<font color='green'>input</font>" or
"<font color='green'>output</font>") in format descriptors, e.g.,
<font color='green'>ASCII_input_data</font>. You may not want to specify
the read/write type in some circumstances. For example, you may need to
translate from native ASCII to binary, then back to ASCII. ASCII is the
input format in the first translation and the output format in the
second translation, vice versa for binary. You would need to edit the
format description file before executing the second translation if you
included read/write type in the format descriptors.

> If you use the -ft option, you do not need to edit the format
> description file. See below.

If you do not specify read/write type, FreeForm ND can nevertheless
determine which format in a format description file is input and which
is output as long as you have adhered to FreeForm ND filenaming
conventions.

- If the input format is not specified, and
  - the input data filename extension is
    <font color='green'>.bin</font>, assume binary input.
  - the input data filename extension is
    <font color='green'>.dab</font>, assume dBASE input.
  - the input data filename extension is
    <font color='green'>.dat</font>, assume ASCII input.
  - the input data filename extension is anything else, assume binary
    input.


- If the output format is not specified, and
  - the input format is dBASE, the output is ASCII or binary, whichever
    is found first.
  - the input format is ASCII, the output is binary or dBASE, whichever
    is found first.



> The appropriate format descriptions must be in the format description
> file(s) used by FreeForm ND for a translation. If, for example,
> FreeForm ND determines the input format is binary and the output
> format is ASCII, there must be a format description for each type.

The checkvar program needs only an input format.

## Locating Format Files

FreeForm ND programs use the following search sequence to find a format
file (format or variable description file) for the data file
datafile.ext when the format file name is not explicitly specified on
the command line. In summary, FreeForm ND searches the directory
specified by the GeoVu keyword <font color='green'>format_dir</font>
(defined in a equivalence table or in the environment), the current or
working directory, and the data file's home directory. The rules are
applied in the order given below until a format file is found or all
rules have been exhausted. If the relevant format file does not follow
FreeForm ND conventions for name or location, it should be explicitly
specified on the command line.

> GeoVu is a FreeForm ND-based application for data access and
> visualization. FreeForm ND applications other than GeoVu use GeoVu
> keywords.

For information about equivalence tables, see the GeoVu Tools Reference
Guide, available from the NGDC.

### Search Sequence

1.  Search the directory given by the GeoVu keyword
    <font color='green'>format_dir</font> for a format description file
    named datafile.fmt.
2.  Search the directory given by the GeoVu keyword
    <font color='green'>format_dir</font> for variable description files
    named <font color='green'>datafile.afm</font>,
    <font color='green'>datafile.bfm</font>, and
    <font color='green'>datafile.dfm</font>.
    > Step 2 is included to accommodate variable description files that
    > were created using previous versions of FreeForm ND. It is
    > recommended that you convert existing variable description files
    > to format description files.
3.  Search the directory given by the GeoVu keyword
    <font color='green'>format_dir</font> for a format description file
    named ext.fmt. If the GeoVu keyword
    <font color='green'>format_dir</font> is not found, FreeForm ND
    continues the search for a format file as follows.
4.  Search the current (default) directory for a format description file
    named datafile.fmt.
5.  Search the current directory for variable description files named
    <font color='green'>datafile.afm</font>,
    <font color='green'>datafile.bfm</font>, and
    <font color='green'>datafile.dfm</font>. Use the criteria in step 2
    for determining input and output format files.
6.  Search the current directory for a format description file named
    <font color='green'>ext.fmt</font>. If the data file's home
    directory is not the same as the current directory, FreeForm ND
    continues the search for a format file with steps 7-9. The data
    file's home directory is given by the directory path component of
    the data file name. If the data file name has no directory path
    component, the home directory search is not done.
7.  Search the data file's home directory for a format description file
    named <font color='green'>datafile.fmt</font>.
8.  Search the data file's home directory for variable description files
    named <font color='green'>datafile.afm</font>,
    <font color='green'>datafile.bfm</font>, and
    <font color='green'>datafile.dfm</font>. Use the criteria in step 2
    for determining input and output format files.
9.  Search the data file's home directory for a format description file
    named <font color='green'>ext.fmt</font>.

### Case Sensitivity

FreeForm ND adheres to the following rules for case sensitivity (in
applicable operating systems) when it searches for a format file for the
data file datafile.ext.

- FreeForm ND preserves the case of datafile, for example, the default
  format file for the data file <font color='green'>LATLON.BIN</font> is
  <font color='green'>LATLON.fmt</font> (or
  <font color='green'>LATLON.bfm</font>).
- FreeForm ND searches for a format file with a lower case extension.
  That is, the format file must have its extension in lower case no
  matter what the case of datafile. For example, the default format file
  for the data file <font color='green'>LatLon.dat</font> is
  <font color='green'>LatLon.fmt</font> (or
  <font color='green'>LatLon.afm</font>), and
  <font color='green'>TIMEDATE.fmt</font> (or
  <font color='green'>TIMEDATE.bfm</font>) is the default format file
  for <font color='green'>TIMEDATE.bin</font>.
- In searching for a format description file of type
  <font color='green'>ext.fmt</font>, FreeForm ND preserves the case of
  ext. For example, for data files named
  <font color='green'>lldat1.LL</font>,
  <font color='green'>lldat2.LL</font>, and
  <font color='green'>latlon3.LL</font>, the default format description
  file is <font color='green'>LL.fmt</font>.

## Command Line Arguments

FreeForm ND programs can take various command line arguments. The most
widely used or standard arguments are discussed in this section. They
are used for several different purposes: identifying input and output
files, identifying format files and titles, changing run-time operation
parameters, and defining data filters.

The only required argument for any FreeForm ND program is the name of
the input file or file to be processed. All other arguments are optional
and can be in any order following the input file name. The command line
of a FreeForm ND program with the standard arguments has the following
form:

application_name input_file \[-f format_file\]

\[-if input_format_file\] \[-of output_format_file\] \[-ft "title"\]
\[-ift "title"\] \[-oft "title"\] \[-b local_buffer_size\] \[-c count\]
\[-v var_file\] \[-q query_file\] \[-o output_file\]

> NOTE: To see a summary of command line usage for a FreeForm ND
> program, enter the program's name on the command line without any
> arguments.

### Specifying Input and Output Files

**input_file** : Name of the file to be processed. Following FreeForm ND naming

conventions, the standard extensions for data files are
<font color='green'>.dat</font> for ASCII format,
<font color='green'>.bin</font> for binary, and
<font color='green'>.dab</font> for dBASE.

**-o output_file** : Option flag followed by the name of the output file. The standard extensions are the same as for input files.

### Specifying Format Description Source

FreeForm ND offers a number of command line options for specifying the
source of the format descriptions that a program must find in order to
process data. The proper option or combination of options to use depends
on how you have constructed your format files.

-f format_file : Option flag followed by the name of the format description file

describing both input and output data.

-if input_format_file : Option flag followed by the name of the format description file describing the input data. Also use this option for an input variable description file written using earlier versions of FreeForm ND.

<!-- -->

-of output_format_file : Option flag followed by the name of the format description file describing the output data. Also use this option for an output variable description file written using earlier versions of FreeForm ND.

<!-- -->

-ft title : Option flag followed by the title (enclosed in quotes) of the format to be used for both input and output data, in which case there is no reformatting. The title follows format type on the first line of a format description in a format description file.

<!-- -->

-ift title : Option flag followed by the title (enclosed in quotes) of the desired input format.

<!-- -->

-oft title : Option flag followed by the title (enclosed in quotes) of the desired output format.

> NOTE: Previous versions of FreeForm ND used variable description files
> (<font color='green'>.afm</font>, <font color='green'>.bfm</font>,
> <font color='green'>.dfm</font>). It is recommended that you convert
> and combine (as appropriate) existing variable description files into
> format description files.

The various options available for specifying the source of a format
description offer you a great deal of flexibility-in naming files,
setting up format description files, and on the command line. In using
these options, you need to consider the content of your format
description files and how FreeForm ND will interpret the arguments on
the command line.

### Changing Run-time Parameters

FreeForm ND includes three arguments that let you change run-time
parameters according to your needs. One argument lets you specify local
buffer size, another indicates the number of records to process, and the
third indicates which variables to process.

-b local_buffer_size: Option flag followed by the size of the memory buffer used to

process the data and format files. Default buffer size is 32,768. You
many want to decrease the buffer size if you are running with low
memory. Keep in mind that too small a buffer may result in unexpected
behavior.

-c count: Option flag followed by a number that specifies how many data

records at the head or tail of the file to process. If $count > 0$,
*count* records at the beginning of the file are processed. If
$count < 0$, *count* records at the tail or end of the file are
processed.

-v var_file : Option flag followed by the name of a variable file. The file

contains names of the variables in the input data file to be processed
by the FreeForm ND program. Variable names in var_file can be separated
by one or more spaces or each name can be on a separate line.

### Defining Filters

The query option lets you define data filters via a query file so you
can precisely specify which data to process. The FreeForm ND program
will process only those records meeting the query criteria.

-q query_file :

Option flag followed by the name of the file containing query criteria.