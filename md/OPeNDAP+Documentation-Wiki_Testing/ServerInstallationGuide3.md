# Installing Your Data

After installing the server, the data to be provided must be put in some
location where it may be served to clients. The server navigates a
directory tree rooted in the web server's document root directory. This
is the directory (often called <font color='green'>htdocs</font> or the
<font color='green'>DocumentRoot</font>) in which the web server first
looks for web pages to serve. If you send your web server this URL:

    http://yourmachine/page.html

It will look for the file <font color='green'>page.html</font> in the
document root directory. Again, the location of this directory depends
entirely on the web server you use and its configuration data. A server
may also be enabled to follow links from the root directory tree, which
means that you can store your data somewhere else, and make symbolic
links to it from the root directory tree. By default, this is disabled
for most servers, since it can become a security problem, but it is
relatively easy to enable it. (The option is called
<font color='green'>FollowSymLinks</font> for Apache users.) Further,
there may be other options provided by the specific server used in a
particular installation. In other words, there is really no way to avoid
consulting the configuration instructions of the web server you use.

> NOTE: You can also use the server's
> <font color='green'>data_root</font> configuration
>
> ` parameter to tell the server where to look for data. The value of`
> ` `<font color='green'>`data_root`</font>` is used as a prefix for the location of data. Using`
> ` this you can configure a server for data that cannot also accessed`
> ` using the web. The only drawback to serving data this way is that`
> ` the directory browsing functions of the OPeNDAP server will not`
> ` work.`

As noted, the location of the data depends not only on the configuration
of the Web server, but also on the API used to access the data
requested. For example, the netCDF server simply stores data in files
with a pathname relative to the document root directory,
<font color='green'>htdocs</font>, while the JGOFS server uses its data
dictionary to specify the location of its data. Refer to the specific
installation notes for each API for more information about the location
of the data.

## Special Instructions for the NetCDF, HDF, and DSP handlers

To install data for the NetCDF, HDF, or DSP formats, follow these steps:

> NOTE: The <font color='green'>data_root</font> parameter can be
> substituted for the web
>
> ` server's `<font color='green'>`DocumentRoot`</font>` in the discussion that follows.`

1.  Make a data directory somewhere in the document root tree. For
    Apache, this is the <font color='green'>htdocs</font> directory and
    all its subdirectories. If your web browser is configured to follow
    symbolic links (Apache: <font color='green'>FollowSymLinks</font> is
    enabled), you can just put links to the data files or to their
    directories in the document root tree. Do be aware that the reason
    symbolic links are not enabled by default is that there are
    potential security holes in its use. Consult your web server's
    documentation for more information.
2.  Put the data files into the data directory you've just made.
3.  Test the URL with a web browser. See
    \[<http://docs.opendap.org/index.php/QuickStart>\| Quick Start
    Guide\] for information about how to construct a URL if you know the
    machine name.

That's all.

## Special Instructions for the OPeNDAP/JGOFS Server

The JGOFS data system is a distributed, object-based data management
system for multidisciplinary, multi-institutional programs. It provides
the capability for all JGOFS scientists to work with the data without
regard for the storage format or for the actual location where the data
resides.

A full description of the JGOFS data system and U.S JGOFS program can be
found at [\| <http://www1.whoi.edu/>](http://www1.whoi.edu/).

### About JGOFS Servers

In the JGOFS data system, scientific datasets are encapsulated in
\new{data objects}. Data objects are simply names defined in the JGOFS
\new{objects} dictionary which associate specific scientific datasets
with a computer program, known as an access \new{method}, which can
\subj{The JGOFS API reads "objects", not files.} read them. This
mechanism provides a single, uniform access methodology for all
scientific datasets served by this data server, regardless of whether
they are simple single file or complex multi-file datasets.

Here is an example of a JGOFS object dictionary entry:

    test0=def(/usr/local/apache/htdocs/data/t0)

In this example,

> <font color='green'>test0</font> : defines the data object name.
> <font color='green'>def</font> : specifies the access 'method' or program used to read the scientific dataset.
> <font color='green'>/usr/local/apache/htdocs/data/t0</font> : specifies the scientific dataset to read.

The OPeNDAP/JGOFS server requires only two components of a complete
JGOFS installation to function, the \`objects' dictionary and the access
\`methods' used to read local data files.

For those sites who wish to use the OPeNDAP/JGOFS server without
installing a complete JGOFS data system we provide these required
components. For sites which currently have a JGOFS data system installed
the OPeNDAP/JGOFS servers can be configured to use the current
installation.

Included with the OPeNDAP/JGOFS server is an example object dictionary,
named <font color='green'>.objects</font>, and a binary version of the
JGOFS default (<font color='green'>def</font>) method. The
<font color='green'>def</font> method can read single or multi-file flat
ASCII datasets. An example ASCII dataset is also provided to test the
server installation.

> NOTE:The OPeNDAP/JGOFS server currently *requires* the object
> dictionary to be named <font color='green'>.objects</font>.

The server uses two mechanisms for determining the location of the data
objects dictionary and access method directory:

1.  The CGI dispatch script <font color='green'>nph-dods</font> defines
    two shell variables, <font color='green'>JGOFS_OBJECT</font> and
    <font color='green'>JGOFS_METHOD</font>. These are used by the
    server to determine the location of the objects dictionary and
    access method directory. Initially, the definition of these
    variables is set to the OPeNDAP server's directory (i.e., the
    directory that contains <font color='green'>nph-dods</font>).
2.  If the shell variables <font color='green'>JGOFS_OBJECT</font> and
    <font color='green'>JGOFS_METHOD</font> are not defined, the
    OPeNDAP/JGOFS server checks for the existence of a
    <font color='green'>jgofs</font> user in the
    <font color='green'>/etc/passwd</font> file. If a
    <font color='green'>jgofs</font> user exists it will set the
    <font color='green'>JGOFS_METHOD</font> location to
    <font color="#cd4b19">~jgofs/methods/</font>, and the
    <font color='green'>JGOFS_OBJECT</font> location to
    <font color="#cd4b19">~jgofs/objects/</font>.
3.  If the server cannot locate the objects directory using either of
    the above two mechanisms it will search the current working
    directory.

If the OPeNDAP/JGOFS server cannot resolve the filesystem paths for the
objects dictionary or methods directory using any of these mechanisms it
will report an error to the client and exit.

> \[Tip:\] If you have an existing <font color='green'>jgofs</font> user
> but would like to use a different objects dictionary for the OPeNDAP
> server, then specifying the <font color='green'>JGOFS_OBJECT</font>
> and <font color='green'>JGOFS_METHOD</font> variables in the
> <font color='green'>nph-dods</font> script will override your existing
> JGOFS setup.

To use the <font color='green'>.objects</font> dictionary and
<font color='green'>def</font> access method supplied with the
OPeNDAP/JGOFS server, edit the <font color='green'>nph-dods</font>
script. In the <font color='green'>nph-dods</font> script, update the
lines specifying the <font color='green'>JGOFS_OBJECT</font> and
<font color='green'>JGOFS_METHOD</font> variables. These variables must
contain the directory name containing the
<font color='green'>.objects</font> dictionary and
<font color='green'>def</font> access method. Don't forget to remove the
comment specifiers at the beginning of these lines.

> \[Tip:\]{Though not required, copying these two files to the cgi-bin
>
> ` location will keep all the OPeNDAP/JGOFS files in one convenient`
> ` location.`

To make data accessible via the server, the entries in the objects
dictionary must define an object name, and associate that name with an
access method and a dataset to serve. Datasets served as JGOFS objects
can be located anywhere within your computer's filesystem. However, the
user and group used to run the WWW daemon must have permission to read
these files.

> \[Another tip:\]To find out which user/group httpd is running as, look
> in the <font color='green'>httpd.conf</font> file for lines like:
> <font color='green'>User nobody</font> <font color='green'>Group
> nobody</font>

To test your server installation using the provided dictionary, and test
data, edit the <font color='green'>.objects</font> dictionary so that
the <font color='green'>test0</font> object points to the files located
in the <font color='green'>\$prefix/share/dap-server/jgofs_data</font>
directory.[9](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink")
You are free to move these files to any location on your computer so
long as the WWW daemon has permission to read these files, and the
objects dictionary points to their location.

Using the supplied test datasets, try issuing the following URL:

    http://yourmachine/cgi-bin/nph-dods/test0.dds

You will see something like this:

    Dataset {
        Sequence {
            String leg;
            String year;
            String month;
            Sequence {
                String station;
                String lat;
                String lon;
                Sequence {
                    String press;
                    String temp;
                    String sal;
                    String o2;
                    String sigth;
                } Level_2;
            } Level_1;
        } Level_0;
    } test0;

You can go further and ask for some data:

    http://yourmachine/cgi-bin/nph-dods/test0.asc

You should see a comma-separated list of data values.

### Attribute Data for OPeNDAP/JGOFS

The JGOFS data access API does not support a wide variety of attribute
data. The OPeNDAP server willlook for a file that contains
\new{ancillary data} file named
\var{object}<font color='green'>.das</font>, in the same directory which
contains the JGOFS methods (i.e., the directory referenced by
\$JGOFS_METHODS).

> NOTE:In the past the server looked in the same directory as the
>
> ` data.`

For example, suppose that you have installed the OPeNDAP/JGOFS server,
as described above, and a partial listing of the data objects dictionary
contained the following definitions:

    test0=def(/home/httpd/htdocs/data/test0.data)
    s87_xbt=def(/home/httpd/htdocs/data/xbt.catalog)

To find attribute data for the JGOFS object \`test0', the server will
search for the file:

    $JGOFS_METHODS/test0.das

The file itself should simply be a text version of the DAS you wish the
server to supply to clients. For example:

    Attributes {
        leg {
           String Description "The number of the voyage leg.";
        }
        year {
           String Range "Data between 1982 and 1992";
        }
    }

### Data Types for OPeNDAP/JGOFS Data

The JGOFS data access system returns string data when using the default
(<font color='green'>def</font>) method. That is, all data is returned
to the client as character strings. The OPeNDAP/JGOFS server supports a
variety of other data types, allowing the data provider (i.e., you) to
specify the variable type to use when returning data to the remote
client. To accomplish this, the server will look for an ancillary DAS
file (described
in([Section3.2.2](Wiki_Testing/ServerInstallationGuide3 "wikilink")))
for the object, and search that file for attribute containers for each
variable. If the variable's container exists, it will then look for the
<font color='green'>DODS_Type</font>, and
<font color='green'>missing_value</font> attributes in that container.

If <font color='green'>DODS_Type</font> exists it will set the output
type of the variable. If the <font color='green'>missing_value</font>
attribute is set, it well replace any <font color='green'>nd</font>, or
missing data values in the output stream for that variable to the value
specified in the <font color='green'>missing_value</font> attribute. If
the <font color='green'>missing_value</font> attribute is not specified,
the server will set the missing value field to the largest value
possible for specified type. The server assumes that the data provider
knows the proper data type to use to contain the full range of possible
data values for the object. If the JGOFS variable values cannot be
converted to the specified data type, then a
<font color='green'>missing_value</font> value will be returned.

Suppose that you have installed the OPeNDAP/JGOFS server, as described
above, and a partial listing of the data objects dictionary contained
the following definitions:

    test0=def(/home/httpd/htdocs/data/test0.data)

The ancillary DAS file for this dataset could contain these new
attributes:

    Attributes {
        leg {
            String DODS_Type "Int32";
            Int32 missing_value -99;
        }
        press {
            String DODS_Type "Float32";
            Float32 missing_value -1.99.;
        }
        temp {
            String DODS_Type "Float64";
        }
    }

The server will use the values specified in the
<font color='green'>DODS_Type</font> attribute for any variable
container provided, to define the output variable type. In this example,
the variable named <font color='green'>leg</font> will be returned as a
32-bit integer (Int32), with missing values set to -99. Similarly,
variable <font color='green'>press</font> will be returned as a 32-bit
floating point number, with missing values set to -1.99. The variable
<font color='green'>temp</font> will be returned as a 64-bit floating
point number, but missing values will be returned as the largest
possible 64-bit floating point number (1.797693e+308).

### Limiting Access to a OPeNDAP/JGOFS Server

Generally speaking, it is straightforward to configure OPeNDAP servers
with limited access. However, the data dictionary structure of the JGOFS
data access API provides an additional way to differentiate between
public and private datasets. To do this, you would maintain different
data object dictionaries which encapsulate different scientific
datasets. One dictionary would define data objects for general public
access, and other dictionaries could define data objects for specific
user communities, where access is restricted.

To accomplish this the site would maintain different versions of the
<font color='green'>nph-dods</font> dispatch script. For example, using
<font color='green'>nph-dods</font> for public access, this file would
set the <font color='green'>JGOFS_OBJECT</font> variable to the
public-access dictionary, and another dispatch script, say
<font color='green'>nph-dods-globec</font> would set the
<font color='green'>JGOFS_OBJECT</font> variable to a restricted-access
dictionary. The WWW daemon is then configured to permit general access
to <font color='green'>nph-dods</font> and restricted access to
<font color='green'>nph-dods-globec</font>.

## Special Instructions for the FreeForm Server

The OPeNDAP FreeForm data handler can serve data stored in a wide
variety of formats. To read data, it uses a \new{format file} that
describes the structure of the data file to be read. Armed with a
knowledge of the file structure, the server can read a data file, and
send it along to the client.

The FreeForm server has its own documentation: see [OPeNDAP FreeForm ND
Server Manual](http://opendap.org/user/servers/dff-html/%7CThe) . You
will find a complete description of the server, as well as instructions
on how to write a format file for your data.