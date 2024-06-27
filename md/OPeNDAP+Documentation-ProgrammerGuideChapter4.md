# Using the Toolkit

This chapter describes how to use the toolkit software to build new
client libraries and data servers. Before beginning to build either part
of a new OPeNDAP application, it is very important to be intimate with
the details of the API to be replaced.

To create a client library that can *replace* the original API
implementation at *link time* means that the client library must present
exactly the same interface as the original library. This includes, to
the extent that they are widely used, any undocumented features of the
original implementation that manifest themselves as symbols that require
link-time resolution. Building a client-library requires great
understanding of the existing implementation as well as current use of
the target API.

To build a good data server for files or data sets encoded using an API
it is important to understand the data model(s) the API supports and how
they relate to the OPeNDAP data models. Each of the various data types
that the API supports must be translated into a OPeNDAP data type (i.e.,
one of the OPeNDAP classes that descend from BaseType). However, there
is often not a one-to-one match between the API's types and the OPeNDAP
types. Thus, the data server author must decide how to best translate
the API's types into OPeNDAP types so as to preserve as much of the data
set author's intent. This is exacerbated by the use of various
conventions that (implicitly) bind several variables together with a
data set. When this pattern shows up (as it does with NetCDF) you must
decide whether to lump all variables together that *appear* to use the
convention (and thus falsely group some variables) or to group only
those which actually are explicitly grouped using whatever the API
provides. If you choose the latter then any data sets which follow the
convention will lose information. When building the data server it is
important to keep such tradeoffs in mind.

The following sections discuss the specifics of building a data server
and a client library. The existing NetCDF server and client library are
used as examples. Many APIs are very similar in their overall
organization. The source code used for these examples can be found in
<font color='green'>\$(OPeNDAP_ROOT)/src/nc-dods/</font>. Much of the
NetCDF example will be relevant to your task, even if your target API is
significantly different. The
<font color='green'>\$(OPeNDAP_ROOT)/src/jg-dods/</font> directory
contains both a data server and client library for the
\[<http://www1.whoi.edu/jgofs.html><cite>Joint Geoghsical Ocean Flux
Study</cite>\] relational data system.

## Data Servers

The OPeNDAP data server consists of a dispatch program and a set of
filter programs. The dispatch program reads the incoming URL and decides
which of the filter programs to run based on the URL suffix.

A typical OPeNDAP data request uses three filters: one to return the DAS
(<font color='green'>.das</font>), one for the DDS
(<font color='green'>.dds</font>), and the third for the data
(<font color='green'>.dods</font>). A client can also request ASCII data
(<font color='green'>.asc</font> or <font color='green'>.ascii</font>),
usage information about the server (<font color='green'>.info</font>),
or version information about the server and the data
(<font color='green'>.ver</font>).

The task of building a OPeNDAP server can then be separated into the
following steps:

1.  Create concrete classes of the entire BaseType hierarchy, with
    <font color='green'>read</font> functions for each data type.
    Certain APIs cannot handle certain OPeNDAP types. For these types,
    there must still be a concrete class, but it can have a
    <font color='green'>read</font> method with a null body.
2.  Write functions that use the native API to extract from the dataset
    the information needed to build the OPeNDAP DAS and DDS objects, and
    then build them with the methods those classes provide.
    > NOTE: This step has nothing at all to do with OPeNDAP. This is
    > between you and your data. OPeNDAP makes no demands on how these
    > structures are created. That is, for example, if all the data to
    > be served has the same DDS, feel free to cheat. The only thing
    > that is important is that the structures accurately reflect the
    > relationships of the data.
3.  Create filter programs to return the DAS , DDS, data, and server
    usage and version information.
4.  Create a dispatch program to parse an incoming URL and invoke the
    correct filter program.

To install the finished server, put the filter programs into a web
server's CGI directory, and put the datasets to be served somewhere they
can be seen by those filter programs. Refer to the
\[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The
OPeNDAP User Guide</cite>\] for more details about installing a server.

### The Dispatch CGI

The OPeNDAP dispatch CGI program receives a data request from the
OPeNDAP client, and dispatches the request to one of several filter
programs. The dispatch CGI is stored in a CGI directory on the host
machine. Its name is an important detail of its operation. The name
should begin with <font color='green'>nph-</font>, and end with the
letters that distinguish data files containing data formatted with that
API from other files.[(8)](ProgrammerGuideFootNotes "wikilink") So, for
example,
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
data files are called \var{foo}<font color='green'>.nc</font>, so the
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
dispatch CGI is called <font color='green'>nph-nc</font>.

The dispatch CGI's job is to parse the incoming URL and execute the
appropriate filter programs with the arguments enclosed in the URL. The
dispatch CGI is also be responsible for the first level of error
information that must be returned to the user. These tasks are easily
accomplished in any scripting language. On the off chance you wish to
use Perl, OPeNDAP provides a Perl class designed to make writing the CGI
a simple task.

The file <font color='green'>OPeNDAP_Dispatch.pm</font> contains the
definitions of the OPeNDAP_Dispatch class. This class provides several
methods used to parse the incoming URL, and one method for delivering
error messages to the client. The OPeNDAP_Dispatch provides the
following methods:

<font color='green'>command()</font> : Returns the command string implied by the input URL. The command string looks like:

<!-- -->

: command filename <font color='green'>-e</font> query-string.

<!-- -->

: Where command is the OPeNDAP filter program to be run, filename is the absolute filename of the dataset on which to run it, and query-string is the constraint expression that was enclosed in the URL. Of the OPeNDAP_dispatch methods, many dispatch CGI scripts may only need to use this one and <font color='green'>print_error_msg</font>. See Figure 4.1.1

<!-- -->

<font color='green'>query()</font> : Returns the query string from the URL. This is the OPeNDAP constraint expression.

<!-- -->

<font color='green'>filename()</font> : Returns the absolute filename corresponding to the requested dataset.

<!-- -->

<font color='green'>extension()</font> : Returns the extension on the end of the URL. For OPeNDAP, this will be <font color='green'>das</font>, <font color='green'>dds</font>, <font color='green'>dods</font>, <font color='green'>info</font>, or <font color='green'>ver</font>.

<!-- -->

<font color='green'>cgi-dir()</font> : Returns the absolute pathname of the directory in which the dispatch CGI is stored. This is generally the same as

the directory in which the OPeNDAP filter programs are stored.

<font color='green'>script()</font> : Returns the name of the dispatch CGI, minus the <font color='green'>nph-</font>, and any suffixes used for a secure server.

<!-- -->

<font color='green'>print_error_message(</font>ver<font color='green'>)</font> : This returns an error message to the client, explaining how to use the server. The ver argument should be a string containing the version of the server software. The error message returned is encoded in the <font color='green'>OPeNDAP_Dispatch.pm</font> file.

<!-- -->

<font color='green'>print_help_message()</font> : This returns a help message to the client. This can be issued in response to a confusing or inadequate URL. The help message returned is encoded in the <font color='green'>OPeNDAP_Dispatch.pm</font> file.

A sample (simple) OPeNDAP dispatch CGI is shown in Figure 4.1.1. This is
a Perl script using the OPeNDAP_Dispatch methods. This script assumes
that all data is rooted in the http document directory
subtree.[(9)](ProgrammerGuideFootNotes "wikilink")

    #!/usr/local/bin/perl

    use Env;
    use OPeNDAP_Dispatch;

    $dispatch = new OPeNDAP_Dispatch;

    <math>command = </math>dispatch->command();

    if ($command ne "") {           # if no error...
        exec($command);
    } else {
        my <math>script_rev = '</math>Revision: 11906 $ ';

    <math>script_rev =~ s@$([A-z]*): (.*) $@</math>2@;

        <math>dispatch->print_error_msg(</math>script_rev);
    }

<center>

A simple OPeNDAP data server dispatch CGI.

</center>

### The DAS and DDS filter programs

The simplest way to learn about creating a new filter program to return
a dataset's DAS or DDS is to examine the existing filter programs. In
this section, we will examine the
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
servers.

The source code for the DAS filter program distributed with the
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
server software is shown in Figure 4.1.2. The DAS and DDS filters are
very similar, so only the DAS filter will be discussed here. The
important differences between the two will be pointed out.

The CGI dispatch program makes heavy use of commonly used functions
collected in the OPeNDAP_Dispatch class. In the same way, the
OPeNDAPFilter class collects several commonly used functions for the
construction of filter programs. The example program uses several
methods of that class. Other useful utility functions are in the
<font color='green'>cgi-util</font> collection.

The filter program in Figure 4.1.2 can be separated into the following
steps:

line 16 : Step 1: The OPeNDAPFilter class provides a constructor that parses the argument list to create the data. You can use the <font color='green'>OK</font> method to check that the list was parsed properly. Any errors here indicate a mistake in the dispatch CGI itself. This is why the <font color='green'>print_usage</font> function prints its message to the WWW server log file when it returns an error object to the client.

<!-- -->

line 21 : Step 2: If the user has only requested version information from the server, it is provided here.

<!-- -->

line 26 : Step 3: The <font color='green'>read_variables</font> function performs the real work of this program. This involves scanning the dataset itself for data variable attributes and using the DAS method functions to assemble the corresponding DAS. This operation is specific to the data access API in use, so does not make a good example.

<!-- -->

line 29 : Step 4: Each of the filter programs must create a MIME document to hold its return value. The DAS and DDS filters return a text MIME document; they set up the MIME headers using the utility function <font color='green'>set_mime_text</font>.

<!-- -->

line 34 : Step 5: Once the data set has been read and the attribute table built, the DAS ancillary file is loaded. The example filter looks for a file with the same root name as the data set and an extension of <font color='green'>.das</font>. If such a file exists, it is read in using the DAS member function <font color='green'>DAS::parse</font> and the information it contains is merged with the DAS built from the dataset.

<!-- -->

line 37 : Step 6: Finally the DAS member function <font color='green'>print</font> is used to send the textual representation of the DAS to the client. When it is invoked by the <font color='green'>httpd</font> daemon, the dispatch CGI's standard input and output are a socket connected to the remote client process. This means that since the filter is invoked by the dispatch script, its output goes directly to the client. The OPeNDAPFilter <font color='green'>send_das</font> method looks something like this:

<!-- -->


     DODSFilter::send_das(DAS &das)
    {
        set_mime_text(dods_das);
        das.print();

        return true;
    }

    #include <iostream.h>

    #include "DAS.h"
    #include "cgi_util.h"
    #include "DODSFilter.h"

    extern bool read_variables(DAS &das,
            const char *filename, String *error);

    int
    main(int argc, char *argv[])
    {
        DAS das;
        DODSFilter df(argc, argv);

        if (!df.OK()) {
            df.print_usage();
            return 1;
        }

        if (df.version()) {
            df.send_version_info();
            return 0;
        }

        String errMsg;
        if(!read_variables(das, df.get_dataset_name(), &errMsg)){
          Error e(no_such_file, errMsg);
          set_mime_text(dods_error);
          e.print();
          return 1;
        }

        if (!df.read_ancillary_das(das))
            return 1;

        if (!df.send_das(das))
            return 1;

        return 0;
    }

<center>

`Figure 4.1.2 The DAS filter program.`

</center>

Note that the example filter in Figure 4.1.2 does not use any caching.
It is possible to build a more sophisticated filter program that saves
the generated DAS to a text file and then uses that file without first
interrogating the data set, thus saving on access. It is also possible
to write a DAS by hand and *always* use that if the data set does not
contain any of the type of information that the DAS has.

#### Caching DAS and DDS Objects

Because the construction of the DAS and DDS objects requires that an
entire data set be scanned, it can become very inefficient to
continually rebuild these objects. Because the DAS and DDS filter
programs use a text representation for transmission from the server to
the client, it is simple to store both the DAS and DDS objects once they
have been created. Subsequent accesses to these objects can be
accomplished by reading and transmitting the textual representation
without actually building the binary data object.

When taking advantage of this optimization, it is important that the
server check the date stamp of the DAS / DDS text objects and compare it
to the latest modification date of the data set. For any dataset to
which new data is periodically added, the DAS / DDS text object must
clearly be updated so that the cached text object matches exactly the
object that would be created if the object were built by querying the
data set.

The update of the DAS / DDS text object can itself be optimized
significantly. It is not actually necessary to completely re-read the
entire data set. Because the software used to build both the DAS and the
DDS binary objects work incrementally, it is possible to read text
version of the DAS / DDS object, and then read only the new parts of the
data set. The binary object will be added to as needed.

> NOTE: The DAS / DDS software may not properly update *changed* data
> (data that was present in a previous version of the data set, but is
> now different) nor is it straightforward to remove data which is no
> longer present in the data set. In these cases it is usually better to
> regenerate the DAS / DDS from scratch.

### The Data filter

The data filter program is structured similarly to both the DAS and DDS
filters except that it returns a binary MIME document rather than text
and that it takes two arguments instead of just one. In addition to the
data set or file name (argument 1) it also takes the OPeNDAP constraint
expression (argument 2, which was enclosed in the URL's *query* ).

The
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
data filter is all but identical to the DDS filter. The only difference
is that it calls the <font color='green'>send_data</font> method of
OPeNDAPFilter to send the binary data over the network. This function
calls the DDS <font color='green'>send</font> method.

If for some reason you cannot use the <font color='green'>send</font>
member function of DDS, then you must ensure that the the *read* , CE
evaluation and the *serialize* operations are all carried out in the
correct order. Furthermore, you must ensure that the return value of the
data filter is a binary MIME document with a text prefix (currently,
OPeNDAP does not use the multi-part MIME standard); that is a regular
binary MIME document with a section at the start that is text. This text
is the DDS generated after evaluating the projection clauses of the
constraint expression. The text part is separated from the data by the
keyword "Data:" at the start of the
line.[(10)](ProgrammerGuideFootNotes "wikilink").

#### The ASCII Data Filter

OPeNDAP is packaged with a filter to translate a OPeNDAP data stream
into an ASCII data file. Clients can request ASCII data by appending
<font color='green'>.asc</font> or <font color='green'>.ascii</font> to
their URL instead of <font color='green'>.dods</font>. The
<font color='green'>asciival</font> program is useful as a standalone
client (see
\[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The
OPeNDAP User Guide</cite>\]), but may also be used by a server to
provide ASCII data.

A request for ASCII data is processed as any other request for data, but
the final output of the data filter is piped into the
<font color='green'>asciival</font> program and the result returned to
the client:

    nc_dods Data.nc | asciival -m -- -

The <font color='green'>OPeNDAP_Dispatch</font> class takes care of this
step automatically, when it encounters a request using
<font color='green'>.asc</font> or <font color='green'>.ascii</font>.

### The Usage Filter

Client requests containing a <font color='green'>.info</font> suffix
should return to the client HTML text containing documentation of both
the server usage and the dataset named in the query. OPeNDAP provides a
<font color='green'>usage</font> filter that can be used for this
purpose. The <font color='green'>OPeNDAP_Dispatch</font> class invokes
this filter.

The OPeNDAP-provided <font color='green'>usage</font> filter accepts two
arguments, the data file name requested and the name of the CGI script
(the dispatch CGI) in use:

    usage filename CGI-name

The <font color='green'>usage</font> filter looks in the dataset
directory for a file called filename<font color='green'>.html</font>,
and in the directory specified in the CGI-name argument for a file
called CGI-name<font color='green'>.html</font>. These two files must
contain HTML, but without the <font color='green'>html</font>,
<font color='green'>head</font> or <font color='green'>body</font> tags.

For example, suppose a dispatch CGI using the
<font color='green'>OPeNDAP_Dispatch</font> class receives a URL like
this:

    http://dods/cgi-bin/nph-nc/data.info

In this case, the <font color='green'>usage</font> filter looks for two
files: <font color='green'>cgi-bin/nph-nc.html</font> and
<font color='green'>data.html</font> (the htdocs directory is assumed in
the second case). The contents of these two files are concatenated with
an HTML representation of the DAS and DDS for the
<font color='green'>data.nc</font> file, and the whole thing is returned
to the client. If the HTML files are not found, the returned document
contains only the DAS and DDS.

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
  be controlled by the user[(11)](ProgrammerGuideFootNotes "wikilink")
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

## Client Libraries

The goal of building a client library is to provide a drop-in
replacement for an existing API so that user programs written for that
API can switch to the OPeNDAP version and access remote OPeNDAP data.
The user programs should not require any modification to change over to
the OPeNDAP client library version of the API. However, the API will
clearly need substantial changes to its current implementation.

In order to build the OPeNDAP client library for a particular API, it is
useful to divide the API to be re-implemented into five categories of
functions:

- Open or connect
- Variable information read
- Data read
- Write
- Close or disconnect

### Rewriting the Open and Close Functions

The functions that perform the dataset "open" and "close" operations
must be implemented so that information about the data set can be
retrieved from the data server. These functions must store the necessary
state information so that subsequent accesses for variable information
or data reads can be satisfied. This state information will, in almost
every case, be the dataset's DAS and DDS.

The open function for a OPeNDAP client library version of a given API
must first determine if the data object (typically a file) is local to
the user program making the open call or is a remote data object to be
accessed through OPeNDAP. It is possible to access OPeNDAP objects which
are local to a user program, but there is little reason to do so if the
data object can also be accessed through the original API. In any case,
the distinction of local or remote is made on the basis whether a URL is
used to reference the data object, or a local filename.

If the data object is remote, then the open function must build a
structure which can hold the DAS and DDS objects which describe the
named data set. This is the Connect class object. Once this object is
built, the open function must map this structure to a file identifier or
pointer which can be passed back to the user program as the return value
of the open function. You add this data to the Connect objects when you
sub-class them for a particular API. Subsequent accesses to the data set
will include this identifier (or pointer), and each function that is a
member of the API can be modified to use it to gain access to the state
information stored by the open function.

The close function should use the state information accessible with the
file identifier or pointer returned by the open function to determine if
the dataset is local or remote. In the case of a local data set, the
original implementation's close function must be called. In the case of
a remote data set, the locally stored state information must be freed.
You can do this by destroying the Connect object.

See [Section 3.2](ProgrammerGuideChapter3 "wikilink") for an example of
a recoded open function and a description of its use. (The example uses
the
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
API.)

### Getting Information about Variables

Most APIs for self-describing data sets include functions which return
information about the variables that comprise a data set. These
functions return information about the type and shape of variables in a
form that can be used by a program as well as attribute information
about the variables that is more often than not intended for use by
humans. Each of these functions must be rewritten so that to the extent
possible, information present in the DAS and DDS is used to satisfy
them.

While many \`self-describing' APIs may have dozens of these functions,
the basic structure of the re-implemented code is the same for each one.
If the data set is local, use the original implementation, otherwise use
the locally stored state information (DAS and DDS) to answer the request
for data.

Rewriting these functions can be the most labor intensive part of
re-imple\\menting a given API. This is typically the largest group of
functions in the API and the information stored in the DAS and DDS must
often be \`massaged' before it fulfills the specifications of the API.
Thus the rewritten functions must not only get the necessary information
from the DAS and DDS objects, but they must also transform the types of
the objects used to return that information to the user program into the
data types the program expects.

### Reading the Values of Variables from a Dataset

To read data values from a dataset using a typical data access API, a
user would submit to some API function the name of the variable to be
read. The OPeNDAP client library version of this same function must take
that variable name and use it to construct a constraint expression. (See
[Section 4.3.2](ProgrammerGuideChapter4 "wikilink") for more information
on using constraint expressions to access data.) The constraint
expression must then be appended to the dataset URL (with the suffix
<font color='green'>.dods</font>), and the resulting URL sent out into
the internet.

For example, to get a variable called <font color='green'>var</font>
from a dataset at:

    http://blah/cgi-bin/nph-nc/weekly.nc.dods

you would use the URL:

    http://blah/cgi-bin/nph-nc/weekly.nc.dods?var

The Connect class contains a member function,
<font color='green'>request_data</font> that performs this task. It
takes the constraint expression and the suffix to use for requesting
data, appends them to the Connect URL, and sends the entire string off
to retrieve its corresponding data.

The <font color='green'>request_data</font> function returns a pointer
to a DDS object, which contains the data as well as the structure
description corresponding to the data request.

Once the <font color='green'>request_data</font> member function has
returned, the client library must still call the
<font color='green'>deserialize</font> member function (which is part of
the OPeNDAP Type Classes) for each returned variable. The client library
should use the variable objects contained in the DDS object returned by
<font color='green'>request_data</font> to invoke the
<font color='green'>deserialize</font> member function. Once that is
done, the data values are stored in the internal buffers of the variable
objects in the new DDS \footnote{For the Sequence data type, the DDS
contains only the current instance of the data. Repeated calls to the
Sequence's <font color='green'>deserialize</font> function are required
to return successive instances of the sequence.}. The client library
should store this new DDS, along with the constraint expression passed
to <font color='green'>request_data</font> so that future requests by
the user program for the same information can be handled without
accessing the remote data server.

The data values of variables in a DDS are accessed using the
<font color='green'>buf2val</font> member function for the cardinal and
vector types and by accessing the values of fields for constructor
types.

#### Translation

For a OPeNDAP client library to be robust, it may have to be equipped to
deal with data types it was not designed to use. For example, the
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
software cannot manipulate a OPeNDAP Sequence. But a user can use the
OPeNDAP version of the
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
library to request data from a server that provides Sequence data. When
cases like this arise (and they arise farily often), the author of the
client library must choose an appropriate data type into which the
served data is to be translated, and implement functions to do that
translation.

Often, translation from one data type to another is a simple task.
Translating an Array into Sequence format is fairly straightforward,
although there are several ways to do it. (The author of the client
library should choose one, and document that choice in a README file.)

Other translations are more complex, and may even require that the
client library violate the semantics of the original API, or of one of
the OPeNDAP data types. For example, translating a Sequence to an Array
in
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
requires that the client know in advance the length of the Sequence,
which is not necessarily known.

### Functions that Write to Data Sets

OPeNDAP is a read-only data system. While it is not technically
inconceivable, a system which allows modification of remote data sets
would be operationally much more complex than OPeNDAP. Thus, functions
that write data are rewritten so that they call the original
implementation in the case of a local access or return an error code in
the case of a remote access. The error code should indicate a
recoverable error so that programs which perform both reads and writes
can recover if their logic permits.

### Adding Local Access to a OPeNDAP Client Library

In order to ensure that programs, once they have been re-linked with
OPeNDAP client libraries, can still access local data files it is
necessary to add software to read those local data to the functions in
the re-implemented library. Typically in each function in the new
library the state information accessed by the identifier passed to the
function is used to determine if the call is to access local or remote
data. In the former case, the function must do exactly what the original
implementation of the API would have done to satisfy the function call.

It is wasteful to completely recode the entire API just to achieve local
access. However, it is also not possible to simply link the user program
with both the OPeNDAP client library and the original library. because
both libraries must define the same external symbols. Linking with both
libraries will produce link-time conflicts on most computers or result
in an incorrectly linked binary image.

In order to use the original implementation of the library, you must
rename all of its external symbols that will appear in user programs.
For example, if an API defines four functions
(<font color='green'>open</font>, <font color='green'>close</font>,
<font color='green'>read</font> and <font color='green'>write</font>)
and one global variable (<font color='green'>errno</font>), then each of
those must be renamed to some new symbol (e.g.,
<font color='green'>orig_open</font>,
<font color='green'>orig_close</font>, ....). These source modules can
then be added to the set of object modules used to build the OPeNDAP
client library. Of course the OPeNDAP client library must also include
the original external symbol names; one approach is to recode each of
the APIs external symbols as a function which either calls the
OPeNDAP-replacement or the original function (now renamed so that the
symbols do not conflict) depending on whether the access is local or
remote.

## Using Constraints

Constraint expressions are an important part of DODS, providing a
powerful way to control how data is accessed without forcing the Data
Access Protocol to support a lot of different messages. Constraint
expressions are used to select which variables will be extracted from a
data set by both the user and by the client library. The constraint
expression syntax is described in detail in the [<cite>The OPeNDAP User
Guide</cite>](http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide).

### How Constraint Expressions are Evaluated

The server-side constraint expressions are evaluated using a two step
process. Every constraint expression has two parts, the projection and
the selection subexpressions. The projection part of a constraint
expression tells which variables to include in any return document
describing the data set and the selection subexpression limits the
returned data to variables with values that satisfy a set of relational
expressions. The projection subexpression is evaluated when the entire
constraint expression is parsed; at parse-time the server's copy of the
data set's DDS is marked with the variables included in the projection.
The selection subexpression, however, is not evaluated until values are
read from the data set. One way to classify the projection and selection
subexpressions is that projections depend solely on the logical
structure of a data set, while selections depend on the values of
particular variables within that data set.

### Different Ways of Using Constraint Expressions

There are two different ways that constraint expressions can be used.
One is by the client library and the other is by the user. When writing
a client library that has features for selecting variables or parts of
variables, try to code the replacements to those calls so that they
build up DODS constraint expressions that will request only the data the
user wants. Then read the data from the returned DDS and store it in the
variable(s) passed to the API call by the user. This is a much better
solution than requesting the entire variable from the data set and then
throwing away parts of it.

Suppose that the user program (via the APIs functional interface) asks
for the data in variable X. The constraint expression that will retrieve
X is simply \`X'. Suppose, given the following DDSthat the user program
requests the two variables u and v from the embedded structure.

    Dataset {
        Int32 u[time_a = 16][lat = 17][lon = 21];
        Int32 v[time_a = 16][lat = 17][lon = 21];
        Float64 lat[lat = 17];
        Float64 lon[lon = 21];
        Float64 time[time = 16];
    } fnoc1;

A constraint expression that would project just those variables would be
fnoc1.u,fnoc1.v. To restrict the arrays u and v to only the first two
dimensions (time and lat), the projection subexpression would be:

    fnoc1.u[0:15][0:16],fnoc1.v[0:15][0:16]

Both of these constraint expressions have null selection subexpressions.
Note that the comma operator separates the two clauses of the projection
subexpression. Also note that whitespace is ignored by the constraint
expression parser. See the grammar for CEs in [<cite>The OPeNDAP User
Guide</cite>](http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide)
for more information about constraint expression grammar and the kind of
things that can be done with the projection subexpression.

The user program may have an interface that provides the user with a way
to request only certain values be returned. This is particularly true
for APIs such as JGOFS that support access to relational data sets.
Suppose the following DDS describes a relational data set:

    Dataset {
        Sequence {
            Int32 id;
            Float64 lat;
            Float64 lon;
            Sequence {
                Float64 depth;
                Float64 temperature;
            } xbt;
        } site;
    } cruise;

To request data with a certain range of latitude and longitude values,
you can use a selection subexpression like this:

    & lat>=10.0 & lat<=20.0 & long>=5.5 & long<=7.5

Note that each clause of the selection subexpression begins with a & and
that the clauses are combined using a boolean and. Finally, using the
previous DDS, if a user requested only depth and temperature given the
above latitude and longitude range (i.e., the user program requests that
only the depth and temperature values be returned given a certain
latitude and longitude range) the client library would use the following
constraint expression:

    site.xbt.depth, site.xbt.temp & lat>=10.0 & lat<=20.0 &
       long>=5.5 & long<=7.5

A second way that constraint expressions can be used is that users may
specify an initial URL with a constraint expression already attached. In
this case the request_data member function will append the constraint
expression built by the client library to the one supplied by the user
and request data constrained by both expressions. From the standpoint of
a client library (or a data server, for that matter) there is no
difference between a URL supplied with an initial constraint and one
supplied without one.