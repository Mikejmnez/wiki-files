[return to FreeForm](FreeForm "wikilink")

# Data Checking

The FreeForm ND-based utility program
<font color='green'>checkvar</font> creates variable summary files,
lists of maximum and minimum values, and summaries of processing
activity. You can use this information to check data quality and to
examine the distribution of the data.

## Generating the Summaries

A variable summary file (or list file), which contains histogram
information showing the variable's distribution in the data file, is
created for each variable (or designated variables) in the specified
data file. You can optionally specify an output file in which a summary
of processing activity is saved.

Variable summaries (list files) can be helpful for performing quality
control checks of data. For example, you could run
<font color='green'>checkvar</font> on an ASCII file, convert the file
to binary, and then run <font color='green'>checkvar</font> on the
binary file. The output from <font color='green'>checkvar</font> should
be the same for both the ASCII and binary files. You can also use
variable summaries to look at the data distribution in a data set before
extracting data.

The <font color='green'>checkvar</font> command has the following form:


        checkvar input_file [-f format_file] [-if input_format_file] [-of output_format_file]

        [-ft "title"] [-ift "title"] [-oft "title"] [-b local_buffer_size] [-c count] [-v var_file] [-q query_file]  [-p precision] [-m maxbins] [-md missing_data_flag] [-mm] [-o processing_summary]

The <font color='green'>checkvar</font> program needs to find only an
input format description. Output format descriptions will be ignored. If
conversion variables are included in input or output formats, no
conversion is performed when you run
<font color='green'>checkvar</font>, since it ignores output formats.

For descriptions of the standard arguments (first eleven arguments
above), see [Conventions](Wiki_Testing/convs "wikilink").

> -p precision:
>
> Option flag followed by the number of decimal places. The number
> represents the power of 10 that data is multiplied by prior to
> binning. A value of 0 bins on one's, 1 on tenth's, and so on. This
> option allows an adjustment of the resolution of the
> <font color='green'>checkvar</font> output. The default is 0; maximum
> is 5.
>
> > NOTE:If you use the <font color='green'>-p</font> option on the
> > command line, the precision set in the relevant format file is
> > overridden. The precision in the format file serves as the default.
>
> -m maxbins:
>
> Option flag followed by the approximate maximum number of bins desired
> in <font color='green'>checkvar</font> output. The
> <font color='green'>checkvar</font> program keeps track of the number
> of bins filled as the data is processed. The smaller the number of
> bins, the faster <font color='green'>checkvar</font> runs. By keeping
> the number of bins small, you can check the gross aspects of data
> distribution rather than the details. The number of bins is adjusted
> dynamically as <font color='green'>checkvar</font> runs depending on
> the distribution of data in the input file. If the number of filled
> bins becomes \> 1.5 \* maxbins, the width of the bins is doubled to
> keep the total number near the desired maximum. The default is 100
> bins; minimum is 6. Must be \< 10,000.
>
> > NOTE: The precision (-p) and maxbins (-m) options have no effect on
> > character variables.
>
> -md missing_data_flag:
>
> Option flag followed by a flag value that
> <font color='green'>checkvar</font> should ignore across all variables
> in creating histogram data. Missing data flags are used in a data file
> to indicate missing or meaningless data. If you want
> <font color='green'>checkvar</font> to ignore more than one value, use
> the query (<font color='green'>-q</font>) option in conjunction with
> the variable file (<font color='green'>-v</font>) option.
>
> -mm :
>
> Option flag indicating that only the maximum and minimum values of
> variables are calculated and displayed in the processing summary.
> Variable summary files are not created.
>
> -o *processing_summary* :
>
> Option flag followed by the name of the file in which summary
> information displayed during processing is stored.

## Example

You will use <font color='green'>checkvar</font> with a precision of 3
to create a processing summary file and summary files for the two
variables latitude and longitude in the file
<font color='green'>latlon.dat</font>.

Here is <font color='green'>latlon.dat</font>:

    -47.303545 -176.161101
    -0.928001    0.777265
    -28.286662   35.591879
    12.588231  149.408117
    -83.223548   55.319598
    54.118314 -136.940570
    38.818812   91.411330
    -34.577065   30.172129
    27.331551 -155.233735
    11.624981 -113.660611
    77.652742  -79.177679
    77.883119  -77.505502
    -65.864879  -55.441896
    -63.211962  134.124014
    35.130219 -153.543091
    29.918847  144.804390
    -69.273601   38.875778
    -63.002874   36.356024
    35.086084  -21.643402
    -12.966961   62.152266

To create the summary files, enter the following command:

    checkvar latlon.dat -p 3 -o latlon.sum

A summary of processing information and the maximum and minimum for each
variable are displayed on the screen. The following three files are
created:

- <font color='green'>latlon.sum</font> recaps processing activity,
  maximums and minimums
- <font color='green'>latitude.lst</font> shows distribution of the
  latitude values in <font color='green'>latlon.dat</font>
- <font color='green'>longitude.lst</font> shows distribution of the
  longitude values in <font color='green'>latlon.dat</font>.

## Interpreting the Summaries

The processing and variable summary files output by
<font color='green'>checkvar</font> from the example in the previous
section are shown and discussed below.

### Processing Summary

If you specify an output file on the command line, it stores the
information that is displayed on the screen during processing. The file
<font color='green'>latlon.sum</font> was specified as the output file
in the example above.

Here is <font color='green'>latlon.sum</font>:

    Input file : latlon.dat
    Requested precision = 3, Approximate number of sorting bins = 100

    Input data format       (latlon.fmt)
    ASCII_input_data       "ASCII format"
    The format contains 2 variables; length is 24.

    Output data format       (latlon.fmt)
    binary_output_data       "binary format"
    The format contains 2 variables; length is 16.

    Histogram data precision: 3, Number of sorting bins: 20
    latitude: 20 values read
    minimum: -83.223548 found at record  5
    maximum:  77.883119 found at record 12
    Summary file: latitude.lst

    Histogram data precision: 3, Number of sorting bins: 20
    longitude: 20 values read
    minimum: -176.161101 found at record 1
    maximum:  149.408117 found at record 4
    Summary file: longitude.lst.

The processing summary file <font color='green'>latlon.sum</font> first
shows the name of the input data file
(<font color='green'>latlon.dat</font>). If you specified precision and
a maximum number of bins on the command line, those values are given as
Requested precision, in this case 3, and Approximate number of sorting
bins, in this case the default value of 100. If precision is not
specified, No requested precision is shown.

A summary of each format shows the type of format (in this case, Input
data format and Output data format) and the name of the format file
containing the format descriptions
(<font color='green'>latlon.fmt</font>), whether specified on the
command line or located through the default search sequence. In this
case, it was located by default. Since
<font color='green'>checkvar</font> only needs an input format
description, it ignores output format descriptions. Next, you see the
format descriptor as resolved by FreeForm ND (e.g.,
<font color='green'>ASCII_input_data</font>) and the format title (e.g.,
"ASCII format"). Then the number of variables in a record and total
record length are given; for ASCII, record length includes the
end-of-line character (1 byte for Unix).

A section for each variable processed by
<font color='green'>checkvar</font> indicates the histogram precision
and actual number of sorting bins. Under some circumstances, the
precision of values in the histogram file may be different than the
precision you specified on the command line. The default value for
precision, if none is specified on the command line, is the precision
specified in the relevant format description file or 5, whichever is
smaller. The second line shows the name of the variable (latitude,
longitude) and the number of values in the data file for the variable
(20 for both latitude and longitude).

The minimum and maximum values for the variable are shown next
(-83.223548 is the minimum and 77.883119 is the maximum value for
latitude). The maximum and minimum values are given here with a
precision of 6, which is the precision specified in the format
description file. The locations of the maximum and minimum values in the
input file are indicated. (-83.223548 is the fifth latitude value in
<font color='green'>latlon.dat</font> and 77.883119 is the twelfth).
Finally, the name of the histogram data (or variable summary) file
generated for each variable is given
(<font color='green'>latitude.lst</font> and
<font color='green'>longitude.lst</font>).

### Variable Summaries

The name of each variable summary file (list file) output by
<font color='green'>checkvar</font> is of the form
<font color='green'>variable.lst</font> for numeric variables and
<font color='green'>variable.cst</font> for character variables. The
data in \*<font color='green'>.lst</font>, and
\*<font color='green'>.cst</font> files can be loaded into histogram
plot programs for graphical representation. (You must be familiar enough
with your program of choice to manipulate the data as necessary in order
to achieve the desired result.) In Unix, there is no need to abbreviate
the base file name.

> NOTE: If you use the -v option, the order of variables in var_file has
> no effect on the numbering of base file names of the variable summary
> files.

The two example variable summary files,
<font color='green'>latitude.lst</font> and
<font color='green'>longitude.lst</font>, are shown next.

<center>

<table>
<tbody>
<tr class="odd">
<td><table>
<tbody>
<tr class="odd">
<td><p><font color='green'>latitude.lst</font></p></td>
</tr>
<tr class="even">
<td><p>-83.224 1</p></td>
</tr>
<tr class="odd">
<td><p>-69.274 1</p></td>
</tr>
<tr class="even">
<td><p>-65.865 1</p></td>
</tr>
<tr class="odd">
<td><p>-63.212 1</p></td>
</tr>
<tr class="even">
<td><p>-63.003 1</p></td>
</tr>
<tr class="odd">
<td><p>-47.304 1</p></td>
</tr>
<tr class="even">
<td><p>-34.578 1</p></td>
</tr>
<tr class="odd">
<td><p>-28.287 1</p></td>
</tr>
<tr class="even">
<td><p>-12.967 1</p></td>
</tr>
<tr class="odd">
<td><p>-0.929 1</p></td>
</tr>
<tr class="even">
<td><p>11.624 1</p></td>
</tr>
<tr class="odd">
<td><p>12.588 1</p></td>
</tr>
<tr class="even">
<td><p>27.331 1</p></td>
</tr>
<tr class="odd">
<td><p>29.918 1</p></td>
</tr>
<tr class="even">
<td><p>35.086 1</p></td>
</tr>
<tr class="odd">
<td><p>35.130 1</p></td>
</tr>
<tr class="even">
<td><p>38.818 1</p></td>
</tr>
<tr class="odd">
<td><p>54.118 1</p></td>
</tr>
<tr class="even">
<td><p>77.652 1</p></td>
</tr>
<tr class="odd">
<td><p>77.883 1</p></td>
</tr>
</tbody>
</table></td>
<td><table>
<tbody>
<tr class="odd">
<td><p><font color='green'>longitude.lst</font></p></td>
</tr>
<tr class="even">
<td><p>-176.162 1</p></td>
</tr>
<tr class="odd">
<td><p>-155.234 1</p></td>
</tr>
<tr class="even">
<td><p>-153.544 1</p></td>
</tr>
<tr class="odd">
<td><p>-136.941 1</p></td>
</tr>
<tr class="even">
<td><p>-113.661 1</p></td>
</tr>
<tr class="odd">
<td><p>-79.178 1</p></td>
</tr>
<tr class="even">
<td><p>-77.506 1</p></td>
</tr>
<tr class="odd">
<td><p>-55.442 1</p></td>
</tr>
<tr class="even">
<td><p>-21.644 1</p></td>
</tr>
<tr class="odd">
<td><p>0.777 1</p></td>
</tr>
<tr class="even">
<td><p>30.172 1</p></td>
</tr>
<tr class="odd">
<td><p>35.591 1</p></td>
</tr>
<tr class="even">
<td><p>36.356 1</p></td>
</tr>
<tr class="odd">
<td><p>38.875 1</p></td>
</tr>
<tr class="even">
<td><p>55.319 1</p></td>
</tr>
<tr class="odd">
<td><p>62.152 1</p></td>
</tr>
<tr class="even">
<td><p>91.411 1</p></td>
</tr>
<tr class="odd">
<td><p>134.124 1</p></td>
</tr>
<tr class="even">
<td><p>144.804 1</p></td>
</tr>
<tr class="odd">
<td><p>149.408 1</p></td>
</tr>
</tbody>
</table></td>
</tr>
</tbody>
</table>

</center>

The variable summary files consist of two columns. The first indicates
boundary values for data bins and the second gives the number of data
points in each bin. Because a precision of 3 was specified in the
example, each boundary value has three decimal places. The boundary
values are determined dynamically by <font color='green'>checkvar</font>
and often do not correspond to data values in the input file, even if
the <font color='green'>checkvar</font> and data file precisions are the
same.

The first data bin in <font color='green'>latitude.lst</font> contains
data points in the range -83.224 (inclusive) to -69.274 (exclusive);
neither boundary number exists in <font color='green'>latlon.dat</font>.
The first bin has one data point, -83.223548. The fourth data bin
contains latitude values from -63.212 (inclusive) to -63.003
(exclusive), again with neither boundary value occurring in the data
file. The data point in the fourth bin is -63.211962.