Limiting the response sizes for different classes of users will need to
be a configuration parameter. This will be set in the OLFS. Users will
initially be divided into two groups: authenticated and unauthenticated.
Authenticated users will have unlimited access capability while
unauthenticated users will only be allowed to transfer up to *N* bytes
of data.

### OLFS and BES communication

Use a SetContext element in the GetData request. The name of the context
will be *response_size_limit*. The BES will not know how the value will
be determined; that will be controlled by the OLFS in its configuration
since that's where the log in information is kept.

### libdap implementation

In the class DDS add *set_* and *get_response_limit()* methods. The
*set_response_limit()* method will be called by the BES in the *void
BESDataResponseHandler::execute( BESDataHandlerInterface &dhi )* or
equivalent method.

In the *libdap:ResponseBuilder* class in the *send_data()* methods check
this value against the computed/estimated response size.

In DDS implement a *get_request_size()* method. When called, evaluates
the DDS and return the number of bytes if a response were to be
requested with the given constraint (which may be null). Use this method
in the *ResponseBuilder::send_data()* methods. To implement this, use
the BaseType::width() method for scalars and vectors (Arrays),
Structures and Grids. For Sequences there really is no way to tell in
advance, except that the *width()* method does provide the size of the
row and a 'row constraint' can be used. In this case, if the request is
issues without a row constraint, then return an error including
information about the row constraint.

Initial implementation: skip testing for Sequences since they are rare.

### If a response is not allowed

Return an Error. *ResponseBuilder::send_data()* can throw an exception
just as for other errors.