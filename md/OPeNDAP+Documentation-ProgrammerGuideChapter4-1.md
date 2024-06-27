### Documenting Your Work

If you do write a server, and intend to circulate it beyond your own
site, here are some guidelines for documenting that server that will
help others use it.

Since there are two sets of "users" for a data server program, there are
two sets of instructions that need to be prepared for a given server.
One set will be read by the person who installs and maintains the server
on the host platform. The other set is designed to be read by people who
intend to request data from that server. These users will get this
documentation by submitting queries to the Info Service, in rather the
same way that many UNIX commands have a
<font color='green'>-usage</font> option.

In addition to these two documents, all servers should include a set of
text files in their distribution directory.

#### The README File

The <font color='green'>README</font> file should contain the following
information:

- A brief overview that describes the purpose and method of operation of
  the server.

<!-- -->

- The revision level of the server.

<!-- -->

- Any features the local <font color='green'>httpd</font> daemon must
  support to use this server.

<!-- -->

- Any data translations that this data server can do. If any are done,
  they should be described in detail, so that users can know what data
  they get.

#### The ERRORS File

The <font color='green'>ERRORS</font> file should contain a complete
list of the error messages and explanations that might ever be issued by
the server.

#### Installation Notes

These instructions should be included in a file called
<font color='green'>INSTALL</font> which is to be included with the
server distribution. At a minimum, they should cover the following
topics:

- Configuring and compiling the server code. Ideally, there should be a
  <font color='green'>configure</font> script included, but detailed
  instructions on editing the <font color='green'>Makefile</font> will
  often suffice. Remember to install the usage data file somewhere the
  server can find it.
- Are there any environment variables that must be defined in order to
  run the server? Are there other programs (e.g.
  <font color='green'>gzip</font> that must be installed on the host
  machine?
- What configuration options are there for the installed server? This
  covers issues like enabling data compression, ancillary data caching,
  and choosing the GUI manager program with which the server will
  communicate. If there are performance trade-offs associated with each
  option, note them here.
- Ancillary data files:
  - Must the installer prepare ancillary data files by hand, or are
    these created automatically and cached?
  - If they must be created, where ought they be put?
  - If they are cached, where are they kept?
  - Also, if the ancillary data files are cached, what implications are
    there for updating the data sets served by this server? (i.e. must
    the ancillary data files be updated also? Deleted and recreated?)


- What temporary files will be created by the server? Where will they be
  stored? Under what conditions may (or must they) they be erased?

#### Information Files

The information files contain the information that remote users of this
server will use to figure out how to use this server and its datasets
once it is installed somewhere. The files are used in constructing the
HTML page for the <font color='green'>info</font> server The
<font color='green'>.info</font> results can include information about
both the server and the current dataset. (In fact, the results will
usually include the DAS and DDS of the dataset named in the URL.)

When a user appends <font color='green'>.info</font> to a URL, the info
service is activated. This service collates information about the server
and the dataset (from the DAS, DDS, lists of global attributes, and
variable summaries), and assembles that information in an HTML document.
The server then looks for additional HTML files created by the server's
administrator, and appends them the original file, and returns the whole
document to the client.

Although it is possible merely to rely on the collated data to describe
a server, we hope that server writers will provide rich, human-friendly
descriptions of the server's usage and the accompanying datasets. These
files can be thought of as "usage" or "README" files. At a minimum, they
should cover:

- Any special data functions defined by the server that can be used in a
  constraint expression, and
- Any data model translations the server supports, and how they are to
  be controlled by the user\footnote{Remember that the "how" is to be
  answered very specifically, and on the user's level (i.e. "Do
  such-and-such, spelled like *this* , to make the array returned be nx5
  instead of 5xn."), and not on the programmer's level (i.e. "You use
  the invert method to return an array of 5xn instead of nx5.")}
- A list of the programs a user should have to use certain features of
  the server. For example, note here that the server expects that the
  GUI manager is running a Tcl interpreter.
- A list of the error messages that the user is apt to see. Include
  explanations of the conditions that may have caused them, and any
  steps the user may take to recover from them.
- The answers to any questions you are frequently asked about this
  server or its usage.

The usage data file need not be any more elaborate than any man page.

To create information for a server, write an HTML fragment using the
format given below, and put the HTML file in the same directory as teh
server. Name it using the base name of the server; for example, the HTML
file that describes the netCDF server (made up of
<font color='green'>nph-nc</font>, and
<font color='green'>nc_das</font>, <font color='green'>nc_dods</font>
and so on) is called <font color='green'>nc.html</font>.

This example shows the correct HTML tagging for server information:

    <h3>
    Server Function:
    </h3>
    <dl>
    <dt>geolocate(variable, lat1, lat2, lon1, lon2)</dt>
    <dd>Returns the elements of <em>variable</em> that fall
    within the box created by (<em>lat1</em>,<em>lon1</em>)
    and (<em>lat2</em>,<em>lon2</em>).</dd>
    <p>
    <dt>time(variable, start_time, stop_time)</dt>
    <dd>Returns the elements of <em>variable</em> that fall
    within the time interval <em>start_time</em> and
    <em>stop_time</em>.</dd>
    </dl>
    <p>

For datasets, put the HTML file, tagged using the format given below, in
the same directory as the datasets. Name it using the base name of the
datasets; for example, the HTML file for
<font color='green'>fnoc1.nc</font>,
<font color='green'>fnoc2.nc</font>, and
<font color='green'>fnoc3.nc</font> might be called
<font color='green'>fnoc.html</font>. This example shows the correct
HTML for a dataset information file:

    <h3>
    About the dataset
    </h3>
    This is where the server administrator would supply
    information about the dataset.  And so on...
    <p>

You may prefer to override this method of creating documentation and
simply provide a single, complete HTML document that contains general
information for the server or for a group of datasets. For example, to
force the info server to return a particular HTML document for all its
datasets, you would create a complete HTML document and give it the name
\var{dataset}<font color='green'>.ovr</font>, where \var{dataset} is the
dataset name. The HTML file in this case would look like this:


    <\html>
    <\head>
    <title>Override document</title>
    <\/head>
    <\body>
    Test dataset

    This is where the server administrator would supply
    information about the dataset(s) and what-have-you.
    <\/body>
    <\/html>

Remember to ensure that the installation instructions cover installing
the usage data file in a place where the server can find it.