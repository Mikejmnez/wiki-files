There are a bunch of issues to be discussed and resolved in Web
Services. I don't really know where to start, so I'll just launch into
it and maybe we can organize as we go:

## General SOAP Discussion

Skip to "On The Other Hand" for the design recommendation...
*JamesGallagher - 29 Jan 2004*

Since WS are based on SOAP we need to decide what kind of SOAP content
to deliver. SOAP is used in two basic ways, as a tool for doing RPC on
remote systems, and as a messaging service.

The SOAP data model is relatively simplistic, it covers for for a
collection of atomic types, arrays, and structures. In the SOAP data
model values must be associated with each atomic type.

Given the type constraints and the embedded value requirements of the
SOAP data model I don't feel it's practical to try to shoe-horn our work
directly into the SOAP model.

To pass our DAP objects via SOAP methods we have 2 choices: Extend the
SOAP data model to cover our custom data types (so that our types can be
used in an RPC request) or rely on SOAP messaging.

Custom data types require the use of custom serialization and
de-serialization classes that must be integrated into the SOAP server
(and client). These classes are tightly coupled to the particular
implementation of the SOAP engine (such as Apache AXIs, GLUE, etc.)

Messaging allows us to pass W3C DOM trees back and forth between server
and client. Each side can then act upon them as necessary (parse them
into a java/C++ class in memory, extract pertinent information directly
from the DOM elements, what have you)

Unfortunately, using messaging basically cuts us off from the automatic
WSDL generation tools that have been developed for the SOAP RPC model.
This may not be such a lrge issue. Ultimately, having a custom data type
in an RPC call only buys you the information (at the WSDL level) that
this method requires (for example) a "DDS" or a "ConstraintExpression"
but probably doesn't lead you to an implementation of such a beast.

I am thinking that for our "foundation" level of WS that we use SOAP
messaging to pass our XML content around.

As we develop more higher level services, such as directories and
catalogs, we will want to implement them using SOAP RPC. These higher
level services are the kind of functionality that will allow our users
to use UDDI as a discovery mechanism, and should be kept in the SOAP RPC
domain.

### Foundation Services

How do we want to expose data sets to the world?

Idea 1:

The URL for a service would look like

[`http://hostname/axis/services/getDDS`](http://hostname/axis/services/getDDS)

Where getDDS would expect a SOAP message containing a constraint
expression that names the dataset whose DDS is to be returned.

Idea 2:

[`http://hostname/axis/services/foo/getDDS`](http://hostname/axis/services/foo/getDDS)

where foo is the dataset name and the getDDS service is tied, by the
server, to the dataset name.

I personally think Idea 1 is the way to go.

A straw-man list of foundation services:

- **getDDS** Expects the invoking message to provide one (or more? why
  not multiple CE's?) CE which names a data set whose DDS is to be
  returned and the constraints which are to applied to it (including
  both selection and projection) -- SOAP Messaging

<!-- -->

- **getDir** An parameterless call that returns an SOAP array of string
  containing the names of all the data sets at this server (probably at
  the granule level). These names are NOT URL/URI based, but simply the
  name that would be used in a CE passed to the getDDS service. -- SOAP
  RPC

<!-- -->

- **getBlob** Requires a single string parameter containing the BlobURL
  of a dataset. It should return a String containing the Base64 encoded
  binary serialized content that our DDS object expects to deserialize
  in order to get it's data values.

As I am writing this I am beginning to see problems with how we have
conceptualized this system. We, or at least I, am still thinking in a
URL centric world. Currently with our URL syntax, it is easy for nearly
identical URL's to initiate different Server side behaviors. By simply
changing the data set suffix from .dds to .dods (while leaving the
projection and selection clauses untouched) we can change the request
from one that returns just the structure to one that returns the
structure and the data. Similarly, in the prototype DDX work, changing
the suffix from .dds to .blob will cause the server to only return the
serialized binary data content with no structural description.

Great, but now consider the proposed system. I strongly believe that as
we move more and more in to a "PUT" style interface (as opposed to a
"GET") where the clients are sending XML messages (either through SOAP
or some other mechanism) to request things from the servers then we are
going to quickly come to a place where the CE will no longer be simply
representable in a URL. This is a problem for the "BlobURL" idea.

If the \*getBlob\* service takes a CE, just as \*getDDS\* does then we
can easily reconstruct what is being requested. That's good.

But then the DDS doesn't actually contain a reference to where it binary
data content may be found. That's bad.

Maybe the solution is that the \*getBlob\* service should take the
constrained DDS as a message, thus the DDS itself becomes the request...

## On The Other Hand

And now, I am starting to think that maybe all of the foundation
services should be wrapped into a single service. Say:

<http://hostname/axis/services/OPeNDAP>

This service would use SOAP messaging, and all of our various request
objects would be passed through it. In some cases possibly forwarded
(sans SOAP envelope) to a C++ engine, like the HDF or JGOFS server. Our
messaging system would then be based on XML DOM elements. For example a
request for a DDS might look like:

    <GetDDS name="data.set.name">
        <Constraint>
            .
            .
            .
        </Constraint>
    </GetDDS>

This would give us an interface that would be very simple to deploy in a
web services environment.

I suspect that even with custom (written by hand) WSDL such a service is
pretty opaque from the Web Services/UDDI point of view. However, I think
that the higher level WS (such as variable and dataset discovery) are
what the users actually want anyway, so building a somewhat opaque
foundation may not really be an issue in the long run.

I agree with this conclusion. *JamesGallagher - 29 Jan 2004*

This scheme allows us to write servers (and by this I don't mean SOAP
based servers) that listen to a single port and that can easily
understand the messages that are being sent to them. It allows clients
to form consistent message formats that allow them to talk directly to
our servers, or to a SOAP interface simply by wrapping the message in a
SOAP envelope.

This is an important point. *JamesGallagher - 29 Jan 2004*

So maybe a the Blob interface would be something like:

    <GetBlob name="data.set.name">
        <Constraint>
            .
            .
            .
        </Constraint>
    </GetBlob>

Which still doesn't get around the DDS not having an explicit reference
to the Blob content within it..

Nathan, you keep saying "DDS." Do you mean "DDX?" *JamesGallagher - 29
Jan 2004*

We could consider adding the constraint expression to the DDS as a form
of metadata... In the past we have basically pruned the DDS to remove
variables that aren't selected. This captures the selection activity in
the CE, but not the projection. We might add the projection information
to the pruned DDS as metadata, although that's a bit awkward as you can
project on variables that aren't selected... Hmmm...

You have 'projection' and 'selection' reversed, but your point is
correct, the <nop>DataDDS or DAP2 (the current DAP spec number is 2,
we're skipping number three so that the C++ code version will match the
spec...) *JamesGallagher - 29 Jan 2004*

I think we should consider replacing the blobURL with the "GetBlob"
message...

Consider:

    <Dataset ...>
        <Array name="foo">
            .
            .
            .
        </Array>
            .
            .
            .
        <BlobRequestMsg SrvcURL="http://hostname/axis/services/OPeNDAP">
            <GetBlob name="data.set.name">
                <Constraint>
                    .
                    .
                    .
                </Constraint>
            </GetBlob>
        </BlobRequestMsg>
    </Dataset>

The **BlobRequestMsg** element has an attribute indicating the server
that contains the pertinent data. The **GetBlob** element (which is
essentially the XML message the server needs to get the correct
serialized binary content for this particular DDS/CE pair) contains the
CE that produced the DDS in which all of this is contained.

This is actually good, in that it doesn't require much of the client,
doesn't tie the Blob retrieval to a URL based CE syntax, and doesn't
necessarily tie the response to a SOAP interface. And in a sense this is
the funcional equivalent (in the XML "PUT" service world) of the blobURL
(in the http "GET" service world from whence we come)

I agree here, too. Since the primary interface will be based on SOAP
messages and not URLs, it makes sense to 'reference the Blob' using a
message and not a URL.

Comments??

*NathanPotter - 28 Jan 2004*

I agree with the suggestion that we base the SOAP interface for DAP4 on
a single 'OPeNDAP' service. I've added a handful of more detailed
comment in line. *JamesGallagher - 29 Jan 2004*

I like the single 'OPeNDAP' service idea with the requested foundation
service encoded in the request message. I wonder if the DDX should
contain a more generic request message rather than one specific for the
Blob, e.g.,

    </Dataset>
        ...
        <RequestMsg SrvcURL="http://hostname/axis/services/OPeNDAP"
                    dataSetName="data.set.name">
                <Constraint>
                    ...
                </Constraint>
        </RequestMsg>
    </Dataset>

That way it doesn't limit the type of request a user can build from the
DDX info (or rather, it doesn't give the appearance of limiting the
users request).

*EthanDavis - 31 Jan 2004*

## More Thoughts On Services

I want to explore another services alternative, and try to illuminate
the alternative in light of the idea that James and I have been kicking
around where core OPeNDAP servers written in languages other than Java
(C++, Fortran) can utilize a consistent Java servlet based front end.

For foundation services, we could provide the above described interface
through a single service such as:

[`http://hostname/axis/services/OPeNDAP`](http://hostname/axis/services/OPeNDAP)

using a collection of commands like

    <GetDDS> . . . </GetDDS>
    <GetBlob> . . . </GetBlob >

which would be removed from the SOAP envelope and acted upon (or
potentially forwarded - unchanged - to a core server implementation like
the HDF or JGOFFS server)

Another possible strategy for foundation services would be to expose
each command as it's own service:

[`http://hostname/axis/services/GetDDS`](http://hostname/axis/services/GetDDS)

[`http://hostname/axis/services/GetBlob`](http://hostname/axis/services/GetBlob)

where the content of the SOAP body element is simply the content of the
<GetBlob> or <GetDDS> message elements as describe above. If the SOAP
server's action is to forward the request to a core OPeNDAP server, then
it would wrap the message content in the appropriate command element (in
this example, <GetBlob> or <GetDDS>) and forward the message to the core
server. This would serve to expose more of the OPeNDAP command/messaging
API through the SOAP interface.

The drawback that I see is that the SOAP server has to manipulate the
message content before sending it to the core OPeNDAP server and the
requesting client has to know that for a SOAP based server it has to
strip those command element tags off...

It also introduces additional configuration work. Each service (in AXIS)
is deployed using a WSDD file. The deployment file for the OPeNDAP
service might look like:

    <deployment name="test" ns="http://xml.apache.org/axis/wsdd/"
        xmlns:java="http://xml.apache.org/axis/wsdd/providers/java"
        xmlns:xsi="http://www.w3.org/2000/10/XMLSchema-instance">
        <!-- note that either style="message" OR provider="java:MSG" both work -->

        <service name="OPeNDAP" style="message">
            <parameter name="className" value="opendap.soap.OPeNDAPService"/>
            <parameter name="allowedMethods" value="opendapService"/>
        </service>

    </deployment>

Whereas the WSDD for a multiple message based services would look more
like:

    <deployment name="test" ns="http://xml.apache.org/axis/wsdd/"
        xmlns:java="http://xml.apache.org/axis/wsdd/providers/java"
        xmlns:xsi="http://www.w3.org/2000/10/XMLSchema-instance">
        <!-- note that either style="message" OR provider="java:MSG" both work -->

        <service name="GetBlob" style="message">
            <parameter name="className" value="opendap.soap.Services"/>
            <parameter name="allowedMethods" value="GetBlob"/>
        </service>

        <service name="GetDDS" style="message">
            <parameter name="className" value="opendap.soap.Services"/>
            <parameter name="allowedMethods" value="GetDDS"/>
        </service>

    </deployment>

As an aside, an RPC based service deployment isn't significantly more
complex, just for grins I'm including the following WSDD example in
which we deploy both the foundation service OPeNDAP (using SOAP
messaging) and a discovery service, GetDir, which uses RPC.

    <deployment ns="http://xml.apache.org/axis/wsdd/"
                xmlns:java="http://xml.apache.org/axis/wsdd/providers/java">

        <service name="OPeNDAP" style="message">
            <parameter name="className" value="opendap.soap.OPeNDAPService"/>
            <parameter name="allowedMethods" value="opendapService"/>
        </service>

        <service name="GetDir" provider="java:RPC">
            <parameter name="className" value="opendap.soap.Services"/>
            <parameter name="allowedMethods" value="GetDir"/>
        </service>

    </deployment>

Anyway, the result of this little discussion is that I think that for
the sake of simplicity with respect to client software, complexity of
servers, and especially with regards to long term configuration
stability I think we should use a single foundation service and as
higher level services (directory, catalog, and discovery) are added they
should be implemented using SOAP RPC.

This allows clients to use the same messaging scheme, whether it's
talking directly to a core server (listening on a TCP/IP socket or what
have you) or to a SOAP service.

*NathanPotter - 29 Jan 2004*