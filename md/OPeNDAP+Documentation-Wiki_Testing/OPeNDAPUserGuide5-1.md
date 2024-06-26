### Documenting Your Data

OPeNDAP contains provisions for supplying documentation to users about a
server, and also about the data that server provides. When a server
receives an information request (through the
<font color='green'>info</font> service that invokes the
<font color='green'>usage</font> program), it returns to the client an
HTML document created from the DAS and DDS of the referenced data. It
may also return information about the server, and more detail about the
dataset.

If you would like to provide more information about a dataset than is
contained in the DAS and DDS, simply create an HTML document (without
the <font color='green'>html</font> and <font color='green'>body</font>
tags, which are supplied by the <font color='green'>info</font>
service), and store it in the same directory as the dataset, with a name
corresponding to the dataset filename. For example, the datasets
<font color='green'>fnoc1.nc</font>,
<font color='green'>fnoc2.nc</font>, and
<font color='green'>fnoc3.nc</font> might be documented with a file
called <font color='green'>fnoc.html</font>.

You may prefer to override this method of creating documentation and
simply provide a single, complete HTML document that contains general
information for the server or for a group of datasets. For example, to
force the info server to return a particular HTML document for all its
datasets, you would create a complete HTML document and give it the name
dataset <font color='green'>.ovr</font>, where dataset is the dataset
name.

More information about providing user information, including sample HTML
files, and a complete description of the search procedure for finding
the dataset documentation, is to be found in
\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
OPeNDAP Programmer's Guide</cite>\].