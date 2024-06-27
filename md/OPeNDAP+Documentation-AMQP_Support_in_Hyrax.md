![](HyraxArchitecture.jpg "HyraxArchitecture.jpg") Support for
[AMQP](http://jira.amqp.org/confluence/display/AMQP/Advanced+Message+Queuing+Protocol)
in hyrax is best handled by adding a new front-end to the server that
can act as an AMQP client, reading information from an AMQP queue. Hyrax
has an overall architecture that already supports this. Figure one shows
a high-level view of the Hyrax architecture. The BES is the part f Hyrax
that builds the bodies of a DAP response. The front-end (the OLFS)
contains a set of handlers which respond to requests made using HTTP.
Based on the request, the OLFS sends commands over a stateful connection
to the BES asking it to make the correct response. Generally, the OLFS
will have to parse the request URL and pass information from that URL to
the BES. Even though the OLFS is designed to support several different
'protocols' like DAP or THREDDS, it is capable of responding to HTTP
only (it is a Java Servlet; see [Server Dispatch
Operations](ServerDispatchOperations "wikilink") for information about
the OLFS design, implementation and extension capabilities). Thus, it
makes the most sense to build a new front-end dedicated to AMQP.

While the architecture chosen to add support for AMQP is very important,
other considerations are also critical to the success of the overall
effort to run DAP over AMQP. One is the mapping between different DAP
versions to AMQP. Since DAP was designed with HTTP in mind, how DAP and
AMQP can best be matched merits serious consideration. This will entail
looking at the current DAP implementation along with the evolving DAP,
version 4, specification and its implementation.

## Proposed Architecture for AMQP Support in Hyrax

![](Hyrax_with_AMQP.png "Hyrax_with_AMQP.png") Figure 2. shows how a
AMQP module could be added to work with Hyrax. The diagram implies that
an actual installation could support both DAP/HTTP and DAP/AMQP
interactions using one BES. That might be true, or it might not,
depending on how the connection pooling is handled by the OLFS and new
AMQP front-end. The DAP/SOAP interface of the OLFS is really different
than the other three interfaces shown in the diagram because the SOAP
messaging software uses the request document body *instead of* the URL
while the other three interfaces/protocols all use the URL and ignore
the request document body. However, it's still part of the OLFS because
Hyrax uses SOAP messaging over HTTP.

One option we should consider is supporting DAP over AMQP using SOAP.

### Sharing one BES between two front-ends

Because there is no limit within the BES on the number of *beslistener*
processes created, any number of front ends can make connections to the
BES and holds those connection sin a pool without concern that the 'pool
will fill up'. This is a result of the Unix fork/exec model and the
architectural design of Hyrax that has placed limits on the number of
outstanding BES connections within the OLFS. A second front end could
establish its own set of connections to completely separate processes.
In the OLFS, the software that manages the connections to the BES can be
found in
[BES.java](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/bes/BES.java).

### Where's the OLFS' 'main'?

To see where the OLFS starts running, look at
[ServletDispatch](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/coreServlet/DispatchServlet.java).
This class' *init()* method is the first code run by the servlet engine
when it starts.

### How the OLFS connects to the BES

On start-up the OLFS makes a connection to the BES. When the OLFS is
started, the BES Daemon (*besdaemon*) has already started and bound a
well-known port (10022 by default; set in both the bes and olfs
configuration files). The OLFS starts when Tomcat starts or restarts the
servlet and initially makes a pool of connections. These are really TCP
socket connections to specific instances of the BES listener
(*beslistener*). When the OLFS gets a request it needs to process using
the BES, it checks this pool of connections and picks the next available
one. If no connections are available, then a new connection is made
unless the maximum number of allowed connections have already been made.
In the latter case the request for a the next available connection
blocks until there is an available connection. The maximum number of
connections to the BES, which is really the maximum number of BES
listeners (i.e., processes) to make is set in the OLFS configuration
file.

To see how the OLFS does this, look at
[BES.java](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/bes),
[OPeNDAPClient.java](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/bes/OPeNDAPClient.java),
and
[NewPPTClient.java](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/bes/NewPPTClient.java)

Important points:

1.  Because the OLFS would block indefinitely if all of the beslisteners
    get 'stuck', the OLFS uses and inactivity timeout to kill
    beslisteners. (This is not the case in Hyrax 1.5 but will be in
    Hyrax 1.6; 300 seconds, hard coded, but it could be a configuration
    parameter).
2.  The OLFS will dump connections from the pool after 2000 commands
    have been sent to a particular *beslistener*. This parameter is hard
    coded into the OLFS, but it could be read from the configuration
    file.
3.  The OLFS initially makes zero connections and it makes new
    connections only when a request for a connection indicates that no
    available connections are in the pool. Thus, even though the maximum
    number of connections is set at N, there will only be N instances of
    the beslistener and N entries in the pool of connections if there's
    a need for N simultaneous connections.
4.  The *besdaemon* and master *beslistener* do not know about the child
    processes that have been created.

### Abstracting the OLFS/BES connection logic

To see how to abstract the connection logic so that the OLFS' connection
pooling and configuration logic can be reused with a different transport
protocol, look at the
[NewPPTClient.java](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/bes/NewPPTClient.java)
class. This class effectively implements a simple interface with the
methods:

init
Make the object that holds state for the request

open
Connect to a new BES listener

send request
Given that a connection to a server exists, send a request

process response
Wait for, and then process, a response to a request

close
Deallocate resources associated with this connection

In the explanation above, I used the word *connection* but it is really
a *virtual* connection. In Java the InetAddress and Socket classes
abstract the operations of socket-based IPC. the actual transport can be
TCP, UDP, ...

Todo: Extrapolate from this class an interface, make this class
implement that interface and then write an second class that provides
for TCP tunneling over AMQP (for example) with a second implementation
of that interface. It may also be that RabbitMQ provides a tight enough
integration with Java's IPC classes that a more straightforward
implantation is possible.

### How the current OLFS dispatching works

![](OLFS_current.png "OLFS_current.png") The OLFS is a dispatch handler,
a giant switch statement that looks at each incoming request and shunts
it to the correct software for processing. In many cases the BES is not
actually involved in the processing or is involved in only a tangential
way. In fact, however, the OLFS' dispatch code is more sophisticated
than a switch statement. Instead it consists of two layers of processing
where the outer layer is made up of a set of *DispatchHandler* classes.
Each of these classes implements the
[DispatchHandler](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/coreServlet/DispatchHandler.java)
interface. This interface has five methods:

init
Initialize the handler; called when the OLFS starts

requestCanBeHandled
Called when a new request is presented to the OLFS and the dispatch
logic is looking for a handler to process it. Returns true or false.

handleRequest
Perform whatever is required to build a response and process it

getLastModified
Ask the BES for the last modified date of some resource that's central
to processing the request.

destroy
Remove the dispatch handlers

Three of these methods take the *request* (i.e.,
*[HttpServletRequest](http://docs.huihoo.com/javadoc/javaee/5/javax/servlet/http/HttpServletRequest.html)*)
object defined by the Servlet class (*requestCanBeHandled*,
*handleRequest* and *getLastModified*). One of the methods also takes
the *response* (i.e.,
*[HttpServletResponse](http://docs.huihoo.com/javadoc/javaee/5/javax/servlet/http/HttpServletResponse.html)*)
object from Servlet (*handleRequest*).

The second level of dispatch takes place within each of the classes that
implement the *DispatchHandler* interface. Here are three examples:

[DirectoryDispatchHandler](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/bes/DirectoryDispatchHandler.java)
Responds to requests for directory information

[BESThreddsDispatchHandler](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/bes/BESThreddsDispatchHandler.java)
Responds to requests for THREDDS catalogs

[DapDispatchHandler](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/bes/DapDispatchHandler.java)
Responds to requests for DAP responses

Take a look at the *DapDispatchHandler* to see how within that class the
requests for several different requests, including some, like the ASCII
response that are not strictly DAP responses, are handled.

### Abstracting the request-response logic of the OLFS

![](OLFS_new_servlet.png "OLFS_new_servlet.png")![](OLFS_new_AMQP.png "OLFS_new_AMQP.png")

The main obstacle we see to building a second front end for Hyrax is
that the *DispatchHandler* interface takes instances of the
*HttpServletRequest* and *...Response* classes and then passes those
along to the lowest levels of the software. This creates close coupling
between the
[ServletRequest](http://docs.huihoo.com/javadoc/javaee/5/javax/servlet/http/HttpServletRequest.html)
interfaces and the OLFS code.

However, the coupling between ServletRequest and the OLFS is not
actually needed. The software could be refactored so that
*DispatchHandler* would accept two objects more suitable to the
dispatching needs of the front end (see Figure 4). At the outermost
levels, in the software that calls the *requestCanBeHandled* and
*handleRequest* methods, those new parameters would be the formal
parameters. Instances of the proposed request and response
implementations could be built from a factory that accept instances of
*ServletRequest* and *ServletResponse* as well as other things such as a
set of hypothetical *AMQPRequest* and *AMQPResponse* objects. Instances
of the request and response implementations would be passed into the new
*DispatchHandler* implementations, allowing them to be used by both the
OLFS and the proposed AMQP front end.

#### How complex would this be to implement?

Most of the work in defining the set of values to be extracted from the
*HttpServletRequest* object has already been done and resides in the
[ReqInfo](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/coreServlet/ReqInfo.java)
class. This class has eleven (11) methods that return information needed
by one or more of the dispatch handlers. Nine (9) of the methods return
string values and two return boolean values. Searching within the OLFS
source code, there are 128 uses of the HttpServletRequest class
(excluding the *experiment* code and all the classes that provide
support for WCS). This corresponds to 23 classes in three packages.

Replacing the *HttpServletResponse* object may be more complicated,
depending on the capabilities of the AMQP Client software base. The
servlet response provides methods to write certain specific headers that
will preface the response body in HTTP's pseudo-MIME response document.
The response also contains a stream used to write the body of the
response, and it allows the OLFS to provide HTTP status responses. It
may be that the best approach is to modify the dispatch software so that
it builds two things: the response preface material and the response
body. Some implementations of the *HyraxResponse* class would ignore the
preface material (e.g., the ContentType header), others might format
that in different ways. To determine more about this class'
construction, we need to know what the AMQP client object expects and
the facilities it provides.

Additionally, components of the OLFS need access to resources held on
the local drive, both within the web applications distribution directory
and in the persistent content directory used by the OLFS to hold
configuration, logs, and as a pace for components to store state
information. The class
[ServletUtil](http://scm.opendap.org/trac/browser/trunk/olfs/src/opendap/coreServlet/ServletUtil.java)
currently provides those local resources paths. This information would
also need to be included in the data object that we develop to abstract
the HttpServletRequest information.

## How Best to Combine AMQP and HTTP

One approach to adapting DAP to a messaging architecture has already
been implemented in our [interface to Hyrax for
SOAP](http://rsg.opendap.org/server-4/templates/soapAPI.html) and this
might be the best starting point for an adaptation of DAP/HTTP to DAP
and AMQP. One difference, however, that is likely to play a role in DAP
over AMQP that doesn't show up in the SOAP interface is that DAP4 is now
much farther along than when that software was written. The feature of
DAP4 most important to this project is that DAP4 over HTTP no longer
relies on HTTP headers as the sole way to return certain information.
Instead, all information about a response is contained in the body of
the response and *some* information is also contained in HTTP response
headers to simplify writing HTTP clients and/or working with DAP2
clients. So, for example, the information about the version of DAP used
to build a particular response is now part of the response body (in the
*<Dataset>* element) *and* in the HTTP response header *XDAP*. This
means that HTTP clients can figure out the version before the response
document is parsed and other protocols (e.g., AMQP) can get it from the
response itself.

*I'll chime in here and say that as far as I can see the primary
obstacle in moving the protocol to AMQP is the use of HTTP headers as
the mechanism for version negotiation between the client and the server.
The client tells the server what it wants and the server hands back a
response that is requested version or lesser. If this was moved into the
request URL via a mandatory server side function, say something like
"version(x.y)" where "x" is the DAP major and "y" the DAP minor version,
then I think using AMQP would simplified.--[ndp](User:Ndp "wikilink")
12:05, 3 December 2009 (PST)*

## See Also

1.  [BES XML Commands](BES_XML_Commands "wikilink") and [Hyrax - BES
    Client commands](Hyrax_-_BES_Client_commands "wikilink")
2.  [How to build the DataDDX response in/with
    Hyrax](How_to_build_the_DataDDX_response_in/with_Hyrax "wikilink")
3.  [Hyrax SOAP
    API](http://rsg.opendap.org/server-4/templates/soapAPI.html)

## Use Cases

In order to move forward and define the most useful way to use DAP2
and/or DAP4 over AMQP, we need to make suer there's a clear
understanding of how the server is supposed to interact with the AMQP
broker and how Hyrax within the OOI system will be used.

<http://www.oceanobservatories.org/spaces/display/CIDev/Data+Exchange>

## TODO email from John

David,

May I explicitly ask if the report can please include the following? - a
summary of work (I'm sure it will have that); - a list of issues
addressed and issues identified; - a set of recommendations for going
forward with additional work. (Based on your results, what do you think
should be done to extend, finish, or improve these results?)

I think this shouldn't represent painful additional work, but it will be
valuable to put this in place now, in preliminary form, to make sure we
can have the big picture captured before the year mysteriously
disappears.

I think David may be the point person to take care of a lot of this, but
I wanted to surface the desired content for everyone to be aware of.
Thanks, I'll go away now!

John