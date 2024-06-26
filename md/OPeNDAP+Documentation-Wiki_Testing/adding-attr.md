# Adding Data Attributes

One of the original design goals of DODS was to permit data providers to
use DODS with as little work as possible. A corollary to this
requirement is that if ancillary information about a dataset is
required, it must be very easy to provide. The DODS architecture allows
a data provider to add attribute information to a dataset without
modifying the data files at all. All that is required is a separate
file, stored in the same directory as the data.

This chapter shows how you can easily add attribute information to an
existing dataset to comply with the DODS data standard. We start by
creating a small dataset and serving it with DODS FreeForm . A similar
example is presented in
\[<http://docs.opendap.org/index.php/Wiki_Testing/dpref><cite>FreeForm
Guide</cite>\] , highlighting different aspects.

All the files used in this chapter are available on the
\[<http://www.opendap.org/examples/ffsimple.dat><cite>DODS
examples</cite>\] .

## A Worked Example

Consider the following table of numbers:

    -47.303545 -176.161101  1.17125
    -25.928001   -0.777265  2.07288
    -28.286662   35.591879  2.36377

    12.588231  149.408117 -100.000
    -63.223548   55.319598  0.04503

    54.118314 -136.940570  1.04085
    -38.818812   91.411330  1.39978
    -34.577065   30.172129  2.09096

    27.331551 -155.233735  2.30917

    11.624981 -113.660611  2.75036

This table represents a series of temperature measurements at ten
different geographical locations. The latitudes and longitudes are given
in decimal degrees, and the temperatures are given in units of ten
degrees. That is, the temperature as recorded, times ten, equals the
temperature in Celsius. There is one temperature measurement missing,
and its place is marked with a value of
<font color='green'>-100.0</font>.

(This dataset, and the ancillary files discussed in this chapter, are in
the DODS examples directory, and is available at the DODS examples ,
under <font color='green'>dasex.\*</font>.)

The dataset shown here can be served as is, with the FreeForm NDs ,
using the following format file (<font color='green'>dasex.fmt</font>):

    ASCII_data "lat/lon"
    latitude 1 10 double 6
    longitude 12 22 double 6
    t 24 31 double 4

Put both of these files in your <font color='green'>htdocs</font>
directory (refer to the documentation for your web server to locate this
directory). After installing the FreeForm ND , you can issue data
requests on this dataset. Use a web browser like netscape, and enter the
following URL (Use your own server's name instead of
<font color='green'>machine.edu</font>.):

    http://machine.edu/cgi-bin/nph-ff/dasex.dat.dds

You should see something like this:

    Dataset {

    Sequence {

    Float64 latitude;

    Float64 longitude;

    Float64 t;

    } lat/lon;
    } dasex;

The is the DDS (Data Description Structure) of the dasex dataset. (See
\[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>DODS
User Guide</cite>\] for more information about the DDS and what it
means, as well as a description of what a Sequence is.) Instead of
<font color='green'>.dds</font> at the end of the URL, you can use
<font color='green'>.asc</font> to see the data itself. Try that.

To see data attributes, try the same URL, ending with
<font color='green'>.das</font>:

    http://machine.edu/cgi-bin/nph-ff/dasex.dat.das

You should see something like this:

    Attributes {

    FF_GLOBAL {

    String Server "DODS FFND release 4.2.3";

    String Native_file " ...";

    }
    }

The DAS is a list of the dataset's attributes. This dataset has two, and
they are global ones. That is, they describe the entire dataset, and
don't correspond to any of the data variables listed in the DDS. They
are also in a container called <font color='green'>FF_GLOBAL</font>,
which is another hint that they are global attributes. The
<font color='green'>Server</font> string describes the actual DODS
server that sent the data, and the
<font color='green'>Native_file</font> string is a long one, containing
information about the original file, and what it contained.

This is all very well, but what if someone wanted to know what units the
<font color='green'>t</font> variable was in, or where the data came
from, or what that value of -100 is supposed to imply, or even whether
the variable represents temperature or time. The data themselves are
silent on those issues, so either we have to provide additional
documentation, or we have to introduce attributes for the data
variables, to make the dataset self-documenting. We do that with an
ancillary file of data attributes. Call this file
<font color='green'>dasex.das</font>:

    Attributes {

    t {

    String short_name "Temperature";

    String units "DegreesC";

    Float64 missing_data -100.0;

    Float64 scale_factor 10.0;

    }

    latitude {

    String short_name "Latitude";

    String units "degree_north";

    }

    longitude {

    String short_name "Longitude";

    String units "degree_east";

    }
    }

Now the DAS that is returned from the dataset looks like this:

    Attributes {

    FF_GLOBAL {

    String Server "DODS FFND release 4.2.3";

    String Native_file "...";

    }

    temp {

    String short_name "Temperature";

    String units "DegreesC";

    Float64 missing_data -100.0;

    Float64 scale_factor 10.0;

    }

    latitude {

    String short_name "Latitude";

    String units "degree_north";

    }

    longitude {

    String short_name "Longitude";

    String units "degree_east";

    }
    }

Now you can see that <font color='green'>t</font> stands for
"Temperature" and that the <font color='green'>-100</font> is missing
data, and that all the measurements have to multiplied by 10 before they
are in degrees Celsius.

Now that the data have these additional attributes, the dataset is
compliant with level 1 of the DODS data standard. This can also be noted
in the DAS, with the addition of another global attribute container
called <font color='green'>DODS</font>. A file called
<font color='green'>dasex2.das</font> contains the following:

    DODS {

    String Conventions "DODS";

    String Acknowledge "Example dataset from the DODS documentation.";

    String history "Created for DODS standards book.";
    }

Add this information to the bottom of the
<font color='green'>dasex.das</font> file (*before* the last closing
bracket), and now the dataset is compliant with level 2 of the DODS data
standard.