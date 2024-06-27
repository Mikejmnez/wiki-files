**This is a placeholder; please complete**

Response handles are different from format handlers. A format handler
reads a new format (or from a new data source like another kind of
server) and returns the standard DAP response objects (DAS, DDS and
DataDDS for DAP2; DDX and DataDDX for DAP4). A new Response Handler,
however, returns something other than those standard response objects.
For example, the [HTML
form](https://scm.opendap.org/trac/browser/trunk/dap-server/www-interface)
is built using a Response Handler.

A Response Handler is loaded at run-time by the BES and is passed a data
source's DDS and DAS objects. The call chain looks like:

1.  Determine the Format Handler for the data source
2.  Use that Format Handler to get the DAS and DDS response objects
3.  Pass those to the correct Response Handler
4.  Redirect standard output of the Response Handler to the OLFS

One trick that we have used in out Response Handlers is to build the
generation of the new response into the print() methods of the Byte,
..., Grid classes, creating specializations that build the new response.
For this to work the DDS passed from the Format Handler must be
modified. Since each of the types in a Format Handler have been
specialized to read a given format, the instances of those types must be
'cast' to the specializations in the Response Handler. However, a simple
C++ cast won't work; for this to work the software must build new
instances of the variables using a factory class. To see how this work
in the HTML form Response Handler, see
[get_html_form.cc](https://scm.opendap.org/trac/browser/trunk/dap-server/www-interface/get_html_form.cc)
and look at the function basetype_to_wwwtype().