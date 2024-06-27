[\<-- back to OPULS Development](OPULS_Development "wikilink")

--[EthanDavis](User:EthanDavis "wikilink") [ndp](User:Ndp "wikilink")
15:55, 15 June 2012 (PDT) [Jimg](User:Jimg "wikilink") 12:42, 1 August
2012 (PDT)

## Attribution

Ethan, Nathan and James

## Background

Over the years, a number of users have requested support for an
asynchronous request/response in OPeNDAP. Most of the requests have been
from large data centers dealing with near-line data archives (e.g., tape
archives) where access to the data may take tens of minutes. Another use
case that has been discussed is server-side processing which, depending
on the processing, may take some time to calculate.

At least one ad hoc attempt to implement this functionality has been
made by an external group.

## Problem addressed

This proposal aims to support requests for data which are not readily
available, and it builds on some of the ideas in the [DAP4: Capabilities
and Versioning](DAP4:_Capabilities_and_Versioning "wikilink") document.

The intent is to support the following cases:

- When a client, that does not understand DAP4 asynchronous responses,
  makes a request that will return an asynchronous response, it should
  fail gracefully (or at least as gracefully as returning an error
  message can be).
- When a client does not know an asynchronous response will be returned
  in response to specific data request, it should be able to recover and
  use information in the response to decide either to
  - not continue with the request; or
  - make the request explicitly stating that it is willing to accept an
    asynchronous response.

In general, asynchronous response schemes all rely on some form of
*notification*. For the sake of simplicity, DAP4 asynchronous responses
will use the following notification scheme:

- Client polling, via URL. The initial response from the server
  indicates that the desired response will be provided asynchronously
  and provides a URL that can be used *at a later time* to retrieve the
  data.

The following notification schemes are used by other systems, but they
will not be supported by DAP4:

- Maintain connection and wait for response (see COMET, long poll,
  connection stayalive, etc.).
- Client-provided URL on which it can receive notification (see Facebook
  Graph API's
  [notification](http://developers.facebook.com/docs/reference/api/realtime/%7Creal-time)
  for how they support this).
- Email notification. This leverages two basic things about the network:
  mail and people, and that makes it very robust. However, I don't think
  it fits well into DAP4. It adds too many options to the request, which
  has already been muddied up quite a bit.

NB: This document describes how DAP4 provides asynchronous responses
when the protocol is used with HTTP. Most of the information here could
be applied to implementations where DAP4 is used with other kinds of
transport protocols.

## Proposed solution

Asynchronous responses are responses that will take the server some time
to build. When a client is told that a response 'is asynchronous,' it
must know to come back at a later time to retrieve the response. The
concept is a very simple one, and the existing network infrastructure is
very good at supporting these kinds of interactions. A major factor in
the success of the proposed solution will be the level of uniform
support for the design. Secondly, as is often the case, the details will
be more complex than the underlying concept. In particular, the request
mechanism must be extended so that synchronous (regular) requests are
not affected by the addition of asynchronous requests and, at the same
time, clients do not inadvertently make asynchronous requests. another
detail is that the (asynchronous) responses are *ephemeral* because they
typically only persist for a period of time and then be purged.

A typical 'workflow' for an asynchronous request is:

1.  A client makes a data request that indicates that it will accept
    either an asynchronous or synchronous response. Optionally, the
    client can place a time constraint on the response, indicating that
    if the response will not be ready in a given period of time, it does
    not want the response.
2.  The server returns an initial response (without delay) that
    indicates the request has indeed resulted in an asynchronous
    response and provides the client with a URL and time estimate.
3.  The client reads the time estimate and waits...
4.  The client dereferences the URL and gets the response.

The remainder of this document will expand on this basic workflow.

### Client willingness to accept asynchronous responses

A client can indicate willingness to accept asynchronous responses in
one of two ways:

- By including the
  [X-DAP-Async-Accept](#Accept_DAP_Asynchronous_Response "wikilink")
  HTTP header.
- By adding the
  [async](#DAP4_Constraint_Expression_extension_for_Async "wikilink")
  keyword to the DAP constraint expression.

If the client indicates that it must have access to the asynchronous
response content within a certain time (utilizing either the
[X-DAP-Async-Accept](#Accept_DAP_Asynchronous_Response "wikilink") HTTP
header and/or the
[async](#DAP4_Constraint_Expression_extension_for_Async "wikilink")
keyword in the constraint expression) and the response will not be
available in that time frame, the server MUST reject the request and
return an HTTP status of [412](#412_Precondition_Failed "wikilink") and
the [DAP Asynchronous Request
Rejected](#DAP_Asynchronous_Request_Rejected "wikilink") XML document.

If both the *X-DAP-Async-Accept* HTTP header and the *async* keyword are
used, the keyword takes precedence.

Servers must reject requests that require an asynchronous response if
the client has not indicated willingness to accept such a response.
Rejection of such requests is indicated by all three of the following:

1.  [HTTP status of
    400](#400_DAP_Asynchronous_Response_Required "wikilink")
2.  Inclusion of the
    [X-DAP-Async-Required](#DAP_Asynchronous_Response_Required "wikilink")
    HTTP response header
3.  The response body must contain the [DAP Asynchronous Response
    Required](#DAP_Asynchronous_Response_Required "wikilink") XML
    document.

This safety check (requiring clients to explicitly indicate their
willingness to accept asynchronous responses) is required because
otherwise very simple clients might inadvertently make requests that
will result in an asynchronous responses, and these kinds of responses
are likely to use disproportionately (relative to synchronous responses)
more server resources. We want to make DAP4 so that simple clients work
well and don't encounter unexpected 'hiccups.'

### Initial processing by the server

When a request is accepted by the server and it will result in an
asynchronous response, the server MUST the server MUST return a 202
(Accepted) HTTP status code and the [DAP Asynchronous Request
Accepted](#DAP_Asynchronous_Request_Accepted "wikilink") XML document.
This document contains a URL to the pending result of the request.

Of course, this discussion is about the mechanism that enables a client
to make a request and the server to provide *information about* an
asynchronous response to that request. It does not cover any of the
nearly infinite ways a server might actually make the *content* of that
response. It is likely that servers will write the responses to files
and the URL returned to the client will be used to retrieve that file,
but there's no requirement that servers do that. The only requirements
on server are that:

1.  The URL returned asserts, using the [constraint expression syntax
    for
    async](#DAP4_Constraint_Expression_extension_for_Async "wikilink")
    that the client accepts async responses.
2.  The URL returned can be dereferenced and that operation will return
    the response requested by the client.

### Response retrieval by the client

When a client requests an asynchronous result that is ready, the server
MUST return a 200 (OK) HTTP status code and the resulting data response.
If the client attempts to access the asynchronous result prior to it's
availability, the server SHOULD return an HTTP response status of [409
(DAP Response Not
Ready)](#409_Conflict_-_DAP4_Response_Not_Ready "wikilink") along with
the [DAP Asynchronous Response Not
Available](#DAP4_Asynchronous_Response_Not_.28Yet.29_Available "wikilink")
XML document. If the server does not return the 409 response status then
it MUST return a 404 (Not Found) response along with whatever document
it deems fit as the response body.

If the client attempts to access the asynchronous result after it is no
longer available, the server SHOULD return an [HTTP response status of
410 (Gone)](#410_Gone_-_DAP4_Response_No_Longer_Available "wikilink")
along with the [DAP4 Asynchronous Response
Gone](#DAP4_Asynchronous_Response_Gone "wikilink") document. If the
server does not return the 410 response status then the server MUST
return a 404 (Not Found) response along with whatever document it deems
fit as the response body.

In each case above where the server SHOULD return a specific error code,
but may return a 404 code instead, the intent is for servers to provide
the most appropriate use of HTTP/1.1's error codes while also providing
servers with an 'out' when that is hard for them to do. For example,
knowing that a response, which is essentially ephemeral, is gone would,
in theory, require to server to keep a record of every URL ever issued
for an asynchronous response and that is not practical. At the same
time, it is easy to see that a client would really like to know that the
response has not yet been finished (i.e., it has not waited long enough)
or that it is gone (i.e., it waited too long).

### Detail: Client requests

#### DAP4 Constraint Expression extension for Async

By adding a keyword/value pair to the DAP4 constraint expression we can
allow a client to encode it's willingness to accept an asynchronous
response, along with the a maximum amount of time the client can wait
before it can access the response.

async
A value of zero indicates the client is willing to unconditionally
accept an asynchronous response. A positive integer value will be
interpreted as the number of seconds that the client will wait for
access to the response. If the value is negative the serve MUST return
an error.

<!-- -->

Examples
Client is willing to unconditionally accept an asynchronous response


<font size="2">`?async=0`<font>

Client is willing to wait for 60 seconds for access to the asynchronous
response


<font size="2">`?async=60`</font>

#### X-DAP-Async-Accept

A client may indicate willingness to accept asynchronous responses by
including the *X-DAP-Async-Accept* HTTP header. Clients can make
conditional requests for asynchronous responses by indicating the
maximum time they are willing to wait by using the
**X-DAP-Async-Accept** HTTP header with a value given in seconds. A
value of zero indicates that the client is willing to accept whatever
delay the server may encounter.

### Detail: Server responses

Several 'experimental' HTTP headers are used by this design. They convey
information either in the request (like the *X-DAP-Async-Accept*
described above) or they encode information for a response. While only
clients that intend to support asynchronous responses need to understand
all of these, *every* client SHOULD understand the
*X-DAP-Async-Required* header. Because we need to support clients like
web browsers, knowledge of that header is not required, but
DAP4-specific clients will provide the most information to users if they
know to look for at least that response header.

#### X-DAP-Async-Required

The *X-DAP-Async-Required* HTTP response header is included in the
response if the request requires an asynchronous response and the client
has not indicated willingness to accept such a response. Rejection of
the request should also be indicated by the [400 DAP Asynchronous
Response Required](#400_DAP4_Asynchronous_Response_Required "wikilink")
HTTP response code.

#### X-DAP-Async-Accepted

The *X-DAP-Async-Accepted* HTTP response header is included in the
response if the server has accepted an asynchronous request. Acceptance
of the request should also be indicated by the [202 Asynchronous Request
Accepted](#202_Accepted "wikilink") HTTP response code.

#### HTTP Response Codes

HTTP provides a number of response codes beyond the simple 200 (OK), 404
(Not Found) and 500 (Internal Server Error). In this design we describe
how those standard codes SHOULD be used by DAP4 servers. We don't
enumerate all of the possible codes, instead opting for a description of
those that most relevant.

##### 202 Accepted

A server indicates that a request has been accepted and will be handled
asynchronously by returning a '202 Accepted' HTTP response code. The
response body must contain a document in one of the asynchronous
information media types listed [below](#Media_Types "wikilink"). A
server MUST return this response, and only do so, when a client has
indicated a willingness to process an asynchronous response and the
response will actually be returned using the asynchronous mechanism.

##### 400 DAP4 Asynchronous Response Required

The '400 DAP Asynchronous Response Required' HTTP response code is used
to indicate that the DAP4 request has been rejected because an
asynchronous response is required and the client did not indicate
willingness to accept an asynchronous response.

The response code text is used to indicate the reason for the rejection.
However, since the '400' HTTP response code is not specific to
asynchronous DAP (the standard text for the '400' code is "Bad
Request"), the *X-DAP-Async-Required* HTTP response header is also
included in the response (see
[above](#Accept_DAP_Asynchronous_Response "wikilink")).

**Note** that a standard 400 HTTP response code is returned. In this
way, a client that does not understand asynchronous DAP can fail
gracefully. The response code text message has been changed to be more
informative of the reason for the failure. For clients that are aware of
asynchronous DAP, the "DAP-Async-Required" header is set to "true". The
body of the response also returns some information the client can use to
decide on how it will continue.

##### 409 Conflict - DAP4 Response Not Ready

The '409 Conflict' HTTP response code MAY be returned by a server to
indicate that the DA4P request has been rejected because a previous
asynchronous request has not been completed and the result is not ready
for access. If a server utilizes the '409 Conflict' HTTP response code
it must also return a [DAP4 Asynchronous Response Not Yet
Available](#DAP4_Asynchronous_Response_Not_.28Yet.29_Available "wikilink")
document in the response body.

##### 410 Gone - DAP4 Response No Longer Available

The '410 Gone' HTTP response code MAY be used by a server to indicate
that the result of an asynchronous request is no longer available. If a
server utilizes the '410 Gone' HTTP response code it must also return a
[DAP4 Asynchronous Response
Gone](#DAP4_Asynchronous_Response_Gone "wikilink") document in the
response body.

##### 412 Precondition Failed

The '412 Precondition Failed' HTTP response code is used to indicate
that the DAP request has been rejected because it did not meet the
**X-DAP-Async-Accept** condition (see
[above](#Accept_DAP_Asynchronous_Response_Conditionally_on_Estimated_Time_to_Completion "wikilink"))
that was specified in the request.

##### 500 Internal Error

The '500 Internal Error' HTTP response code is used to indicate that the
DAP request has caused an error on the server. The request body and
other headers must be compliant with the [DAP4 Error
Response](DAP4_Web_Services_v3#DAP4_Error_Response "wikilink") and
[Status Codes](DAP4_Web_Services_v3#Status_Codes "wikilink") sections of
the [web services specification](DAP4_Web_Services_v3 "wikilink"). The
request should not be repeated.

#### Asynchronous Response Documents

The uses of these documents are:

- to inform clients that a request will result in an asynchronous
  response;
- to provide clients with the status of an an accepted asynchronous
  request; and
- to inform clients that a request for and asynchronous response has
  been rejected.

These response documents are the payloads to various responses,
including errors. By using the HTTP 400-series error response codes, the
design ensures that generic web clients will understand that their
request was in error (even if they don't really understand why). The
text provided with the response code will be sufficient that person
could understand the gist of the problem, if not more. The response
documents described here, along with the *X-DAP* describe above, are a
way of providing additional information to a savvy client so that it can
take full advantage of the synchronous response system.

These documents are XML that follows the DAP Asynchronous XML schema and
are declared in the namespace
**http://opendap.org/ns/dap/asynchronous**.

##### DAP4 Asynchronous Response Required

This document informs clients that a request will result in an
asynchronous response, and that the client has not yet indicated it's
willingness to accept an asynchronous response. It might seem
superfluous to include a document that clearly only a client
knowledgable about the asynchronous response features could parse, but
many such clients may not, as a matter of course, indicate they will
accept these responses. For example, a user-configurable parameter might
be turn off support for the feature. The *expectedDelay* and
*responseLifetime* elements convey information about conditions the
clients can expect if it submits an asynchronous request for the
response. As noted below, these are estimates made by the server since a
number of things that the server cannot predict can affect them in the
interleaving time between the client's requests. Additionally, a server
MAY return values of zero for either of the values, indicating that it
cannot make an accurate estimate.

<font size="2">

``` xml
<AsynchronousResponse status="required">
  <expectedDelay seconds="600" />
  <responseLifetime seconds="3600"/>
</AsynchronousResponse>
```

</font>

This response MUST be associated with the 400 HTTP response code and the
*X-DAP-Async-Required* response header.

##### DAP4 Asynchronous Request Accepted

This response informs clients that a request resulting in an
asynchronous response has been accepted, along with operational
information about retrieving the asynchronous response result. Note that
the *expectedDelay* and *responseLifetime* elements are an estimate by
the server. A server SHOULD ensure that the response will remain
available for the time period given by *expectedDelay* and
*responseLifetime*. We say *SHOULD* and not *MUST* because we cannot
predict all possible operational situations where these kinds of
responses might be used. For example, a server might be providing access
for several types of users who might have different access priorities,
especially to limited resources like those typically involved with
asynchronous access, and thus some responses might be further delayed,
or removed early, to enable processing of requests from users with
higher priority. It should be kept in mind, however, that the usefulness
of the asynchronous responses will depend, in part, on servers providing
a facility on which clients can depend.

While the *expectedDelay* and *responseLifetime* elements are required,
a server MAY set their *seconds* attribute to *0* to indicate that it
cannot provide a reliable value. In this case, clients SHOULD poll every
300 seconds and servers SHOULD expect this behavior. This is the default
TCP user timeout period (see <http://tools.ietf.org/html/rfc5482>).

<font size="2">

``` xml
<AsynchronousResponse status="accepted">
  <expectedDelay seconds="600" />
  <responseLifetime seconds="3600"/>
  <link href="http://server.org/async/path/result" />
</AsynchronousResponse>
```

</font>

This response document MUST be associated with the 202 HTTP status code
and the *X-DAP-Async-Accepted* response header.

##### DAP4 Asynchronous Response Not (Yet) Available

This document informs clients that a while a previous request for an
asynchronous response has been accepted, the result is not available.

<font size="2">

``` xml
<AsynchronousResponse status="pending"/>
```

</font>

This response document MUST be associated with the [409 HTTP response
code](#409_Conflict_-_DAP4_Response_Not_Ready "wikilink").

Servers SHOULD return this response document and it's associated HTTP
status of 409, but servers MAY return any document in the response body
along with either a a 404 (Not Found) or a 400 (Bad Request) HTTP
status.

##### DAP4 Asynchronous Response Gone

This document informs clients that a while a previous request for an
asynchronous response has been accepted, the result is *no longer*
available.

<font size="2">

``` xml
<AsynchronousResponse status="gone"/>
```

</font>

This response document MUST be associated with the [410 HTTP status
code](#410_Gone_-_DAP4_Response_No_Longer_Available "wikilink").

Servers SHOULD return this response document and it's associated HTTP
status of 410, but servers MAY return any document in the response body
along with either a a 404 (Not Found) or a 400 (Bad Request) HTTP
status.

##### DAP4 Asynchronous Request Rejected

This document informs clients that a request for an asynchronous
response has been rejected, even though the client said it is willing to
process an asynchronous response. There are at least as many reasons a
server might reject the request for an asynchronous response as there
are systems that might return such responses. However, this design
provides suggested response codes for cases that seem likely so that
clients can make educated decisions about the reason for the rejection.
The reason codes supported are:

time: The client indicated that it was only willing to wait *X* seconds and the server thought it would take more time to build the result.
unavailable: A needed resource is not available. This might indicate that hardware, like a robot tape system, cannot be currently accessed.
privileges: The client is not allowed to make the request.
other: Self evident...

In addition to the reason codes, this response will contain a text
description of the reason for rejection.

Servers SHOULD make every effort to use the correct reason codes and
provide cogent descriptions.

<font size="2">

``` xml
<AsynchronousResponse status="rejected">
    <reason code="time"/>
    <description>Acceptable access delay was less than estimated delay.</description>
</AsynchronousResponse>
```

</font>

This response document MUST associated with the 412 HTTP status code.

Servers SHOULD return this response document along with an HTTP status
of 412, but servers MAY return any document in the response body along
with an HTTP status of 404 (Not Found) or of 400 (Bad Request) in its
place.

##### DAP4 Error

If the server encounters an error it must MUST (MAY?) return an HTTP
status of 500 (Internal Error) along with a request body and other
headers compliant with the [DAP4 Error
Response](DAP4_Web_Services_v3#DAP4_Error_Response "wikilink") and
[Status Codes](DAP4_Web_Services_v3#Status_Codes "wikilink") sections of
the [web services specification](DAP4_Web_Services_v3 "wikilink"). The
request should not be repeated.

### Examples

#### Constrained Data Request-Response using GET

Simple Request:

<font size="2">

    GET /dap/path/data.nc?projection=x,y,temp HTTP/1.1
    Host: server.org

</font>

If the server decides it needs to handle this request in an asynchronous
manner, it will refuse the request because it did not say it would
accept an asynchronous response.

Response:

<font size="2">

``` xml
400 DAP Asynchronous Response Required
X-DAP-Async-Required: true
Content-Type: text/xml;charset=UTF-8

<AsynchronousResponse status="required">
  <expectedDelay seconds="600" />
  <responseLifetime seconds="3600"/>
</AsynchronousResponse>
```

</font>

#### Constrained Data Request-Response with DAP-Async-Accept Request Header

Request: <font size="2">

    GET /dap/path/data.nc?projection=x,y,temp HTTP/1.1
    Host: server.org
    X-DAP-Async-Accept: 0

</font>

Alternately, this request would produce the same result using only the
URL: <font size="2">

    GET /dap/path/data.nc.dap?accept=0&projection=x,y,temp HTTP/1.1
    Host: server.org

</font>

Response: <font size="2">

``` xml
202 Accepted
Content-Type: text/xml;charset=UTF-8

<AsynchronousResponse status="accepted">
  <expectedDelay seconds="600" />
  <responseLifetime seconds="3600"/>
  <link href="http://server.org/async/path/result" />
</AsynchronousResponse>
```

</font>

**NB**: This example originally included an *Accept* header with the
value of *multipart/mixed*. However, that is not a good example. The
HTTP/1.1 specification says that when a specific media type is indicated
as the only one acceptable, a server must return a 406 response code if
it cannot return that media type. The meaning of *Accept: \*/\** is the
same as not including the header, so I have removed the header from
these examples. We need to be heads up in the ways that we suggest that
header should be used by clients

#### Constrained Data Request-Response with conditional DAP-Async-Accept Request Headers

Request: <font size="2">

    GET /dap/path/data.nc?projection=x,y,temp HTTP/1.1
    Host: server.org
    X-DAP-Async-Accept: 60

</font>

Alternately, this request would produce the same result using only the
URL: <font size="2">

    GET /dap/path/data.nc.dap?acceptAsync=60&projection=x,y,temp HTTP/1.1
    Host: server.org

</font>

Response: <font size="2">

``` xml
412 Precondition Failed
Content-Type: text/xml;charset=UTF-8

<AsynchronousResponse status="rejected">
    <reason code="time"/>
    <description>Acceptable access delay was less than estimated delay.</description>
</AsynchronousResponse>
```

</font>

#### Premature Request For Asynchronous Result

Request: <font size="2">

    GET /async/path/data.nc?projection=x,y,temp HTTP/1.1
    Host: server.org

</font>

Alternately, this request would produce the same result using only the
URL: <font size="2">

    GET /async/path/data.nc?projection=x,y,temp HTTP/1.1
    Host: server.org

</font>

Response: <font size="2">

``` xml
409 Conflict
Content-Type: text/xml;charset=UTF-8

<AsynchronousResponse status="pending"/>
```

</font>

## Discussion