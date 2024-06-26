# Building OPeNDAP Data Handler

Though several different handlers are included in the OPeNDAP software,
some users may wish to write their own data handlers. The architecture
of the <font color='green'>httpd</font> server and the OPeNDAP software
make this a relatively simple task.

A user may wish to write his or her own data handler for any or all of
the following reasons:

- The data to be served may be stored in a format not compatible with
  one of the existing handlers.
- The data may be arranged in a fashion that allows a user to optimize
  the access of those data by rewriting one of the handlers.
- The user may wish to provide ancillary data to clients not anticipated
  by the writers of the servers available.

The design of the OPeNDAP libdap library makes the task a relatively
simple one for a programmer already familiar with the data access API to
be used. The library provides a complete set of classes with most of the
hard parts done already: evaluation of constraint expressions, network
connections, and so on. You can create working sub-classes simply by
adding the read methods which read data from the local disk.

Also, though the handlers provided with the server are written in C++,
they may be written in any language from which the OPeNDAP libraries may
be called. There is also a Java version of the class library.

Once it is invoked, a CGI program scoops up whatever input is going to
the standard input stream of the Web server
(<font color='green'>httpd</font>) that invoked it. Further, the
standard output of the CGI is sent directly back to the requesting
client. This means that the CGI program itself need only read its input
from standard input and write its output to standard output.

Most of the task of writing a server, then, consists of reading the data
with the data access API and loading it into the libdap classes. Methods
defined for each class make it simple to output the data so that it may
be sent back to the requesting client.

Refer to \[<http://docs.opendap.org/index.php/ProgrammerGuide><cite>The
OPeNDAP Programmer's Guide</cite>\] for specific information about the
classes and the facilities of the libdap software, and instructions
about how to write a new server.