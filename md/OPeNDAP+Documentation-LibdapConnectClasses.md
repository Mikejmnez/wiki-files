[return to Libdap Overview](Libdap_Overview "wikilink")

# Managing Connections

Because OPeNDAP uses a communication protocol (http) that does not
maintain state information about the link between two processes, these
connections are virtual, and all the information about them is
maintained by the client. Libdap contains two classes to help clients
manage connections to one or more OPeNDAP servers. One class, *Connect*,
stores information used when a connection is established. It also allows
a program to provide local access to data (without a OPeNDAP data
server).

The second class, *Connections*, manages instances of the Connect class.
It provides a mechanism for the client libraries to pass back to user
programs the type of object (such as <font color='green'>int</font>,
opaque pointer, dots) they expect and then to use one of those objects
to access the correct instance of Connect . Connections is a template
class. That is, a client library uses the Connections class to make an
array of some sub-class of Connect.

## Connect

The Connect class manages a connection to either a remote data set, via
an OPeNDAP data server, or a local access. For each data set or file
that the user program opens, there must be exactly one instance of
Connect. Because information needed for local access is stored in an
instance of Connect, this class may also be used (sub-classed) for each
client API that needs to maintain additional information about the
connection.

<center>

<figure>
<img src="Connect.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Connect Class

</center>

The Connect class is illustrated in
[above](:Image:Connect.gif "wikilink"). These objects contain the
following information:

URL : The URL with which the client library opens the connection with the OPeNDAP server.

<!-- -->

DDS : The DDS of the opened dataset. This must be retrieved from the dataset.

<!-- -->

DAS : The DAS of the opened dataset. This must be retrieved from the dataset.

<!-- -->

Gui : The Gui object indicates a graphical widget on the client platform that displays information about the data transfer in progress.

<!-- -->

Error : The Error object contains information used to help the client process an error message from the server.

The Connect class provides member functions to get the dataset's DAS ,
DDS and data. The instance of Connect (or a subclass of Connect) stores
the URL as the user provides it.

When the client library receives a URL via its "open" call, it passes
that URL to the Connect member functions like <font
color='green'>request_das</font> and <font
color='green'>request_dds</font>. These member functions append an
appropriate extension (<font color='green'>.das</font> and <font
color='green'>.dds</font>, for example) onto the URL and retrieve the
resulting information from the server, the DAS or DDS for the dataset.

If you are re-implementing an API and must support function calls that
modify how data is accessed (e.g., by creating array slices or by
choosing one of a set of variables), then you will need to translate
those requests into a OPeNDAP constraint expression. You would then pass
these synthesized constraint expressions to the
<font color='green'>Connect::request_data</font> member function.

The specialized version of Connect is the place to put state information
needed by a recoded API or other client. This can be used, for example,
to emulate an API that maintains a list of open files.

## Connections

The class Connections is used to manage a set of instances of the class
Connect by providing a means to map an index or opaque pointer to an
instance of Connect.

When a new instance of Connect (or a descendent) is created, it is added
to the Connections object using the <font
color='green'>add_connect</font> member function. <font
color='green'>add_connect</font> returns an integer that can be used to
access that instance of Connect at any time. Similarly, when an instance
of Connect is to be deleted, the object can be referred to via the
Connections object and this index.