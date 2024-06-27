There are already many response types that come with the out-of-the-box
BES. For example, the response to a 'show help;' request, or the
response to a 'show version;' request. Both of these responses are
inforamtional responses.

With a Dap-enabled BES you get respons types DAS, DDS, DataDDS, and DDX.

All of these response types are handled by a BESResponseHandler object.
In the case of the 'show help;' request, there is a
BESHelpResponseHandler object. In the case of the 'show version;'
response, there is a BESVersionResponseHandler object. And, in the case
of the more complex responses like DAS, there is a BESDASResponseHandler
object.

The response handlers know how to create the response object, an
informational response object BESInfo or the more complex type of
response object BESDASResponse which points to a DAS object. They also
know 'how' to populate the response objects, but they don't necessarily
populate them. The response handlers also know how to transmit this
response object by calling the appropriate transmit method on the
[BESTransmitter objects](New_Transmitters "wikilink").

Other projects have added new response types to the BES. These response
types present the data in different ways. For example, in the [CEDARWeb
project](http://cedarweb.hao.ucar.edu) new responses were added to
present the user with a flat view of the data (flat), a tabbed view of
the data (tab), and a cedar informational response (info). How would you
like your data to be viewed?

If you specified new response types in the last question to the
[besCreateModule script](Create_BES_Module "wikilink") then for each one
of those new response types you will need to modify the source file
<response_type>ResponseHandler.cc. This is where you will tell the
system how to create the response object and how it will be transmitted.

Let me make sure that you understand the difference here. The [request
handlers](New_Request_Handlers "wikilink") will populate the response
object whereas the response handler knows how to create the response
object (create only, not populate) and 'how' the response object will be
populated, such as calling a specific [request/data
handler](New_Request_Handlers "wikilink"), or all of the [request
handlers](New_Request_Handlers "wikilink"), to populate the response
object, or handling it itself (it can do some populating).

For example, for a DAS response, the response handler creates a DAS
response object and then for each container specified in the request the
response handler calls the appropriate build function in the request
handler that handles that type of container. A different example is the
help response. In this case, for each type of data handled by this
server (for each request handler), the response handler will call the
help build function for that request handler. By default, your new
module handles help and version requests.

The response handler also knows how to transmit the response object. For
example, the DAS response handler knows to call the send_das method on
the [transmitter object](New_Transmitters "wikilink") that is passed to
the transmit method. This way we can create different ways to transmit
the data. For example, transmitting html data to a browser, or
transmitting binary data in xdr format, or transmitting plain text data.
You can create [new transmitter classes](New_Transmitters "wikilink")
with a unique name and specify that you want a response transmitted
using your transmitter using that unique name. In your request you would
say 'return as <unique_name>'.