[\<-- back to OPULS Development](OPULS_Development "wikilink")

--[EthanDavis](User:EthanDavis "wikilink") 20:41, 19 April 2012 (PDT)

## Background

Over the years, a number of users have requested support for
asynchronous request/response in OPeNDAP. Most of the requests have been
from large data centers dealing with near-line data archives (e.g., tape
archives) where access to the data may take tens of minutes. Another use
case that has been discussed is server-side processing which, depending
on the processing, may take some time to calculate.

At least one ad hoc attempt to implement this functionality has been
made by an external group.

## Problem addressed

This proposal aims to support requests for data that are not readily
available. The proposal builds on some of the ideas in the [DAP4:
Capabilities and
Versioning](DAP4:_Capabilities_and_Versioning "wikilink").

This proposal tries to support the following use-cases/requirements:

- When a client that does not understand asynchronous DAP makes a
  request that requires an asynchronous response, it should be able to
  fail gracefully.
- When a client does not know an asynchronous response will be required
  on a data request, it should be able to recover and use information in
  the response to decide to
  - not continue with a request
  - make request explicitly stating that it is willing to accept an
    asynchronous response.
- Allow for the following notification schemes:
  - Client polling (default)
  - Email notification
  - Maintain connection and wait for response (see COMET, long poll,
    connection stayalive, etc.)
  - Client application provide HTTP URL on which it can receive
    notification (see Facebook Graph API's
    [notification](http://developers.facebook.com/docs/reference/api/realtime/%7Creal-time)
    for how they support this).

## Proposed solution

Uses link relationships and media types described in the [DAP4:
Capabilities and
Versioning](DAP4:_Capabilities_and_Versioning "wikilink") Proposal.

Servers must reject requests that require an asynchronous response if
the client has not indicated willingness to accept such a response.
Rejection of such requests is indicated with a **[400 DAP Asynchronous
Response Required](#400_DAP_Asynchronous_Response_Required "wikilink")**
HTTP response code and inclusion of the
**[DAP-Async-Required](#DAP_Asynchronous_Response_Required "wikilink")**
HTTP response header. The response body must contain a document in one
of the asynchronous information media types listed
[below](#Media_Types "wikilink"). A client can indicate willingness to
accept asynchronous responses by including the
**[DAP-Async-Accept](#Accept_DAP_Asynchronous_Response "wikilink")**
HTTP header.

Clients can make conditional requests for asynchronous responses by
indicating the maximum time they are willing to wait with a
**[DAP-Async-Accept-If-Expected-Delay-Less-Than](#Accept_DAP_Asynchronous_Response_Conditionally_on_Estimated_Time_to_Completion "wikilink")**
HTTP header with a value given in milliseconds.

### HTTP Response Headers

#### DAP Asynchronous Response Required

**DAP-Async-Required**

The **DAP-Async-Required** HTTP response header is included in the
response if the request requires an asynchronous response and the client
has not indicated willingness to accept such a response. Rejection of
the request should also be indicated by the [400 DAP Asynchronous
Response Required](#400_DAP_Asynchronous_Response_Required "wikilink")
HTTP response code.

### HTTP Request Headers

#### Accept DAP Asynchronous Response

**DAP-Async-Accept**

A client indicates willingness to accept asynchronous responses by
including the **DAP-Async-Accept** HTTP header. The value of the header
must be one of the asynchronous resource media types listed
[below](#Media_Types "wikilink").

#### Accept DAP Asynchronous Response Conditionally on Estimated Time to Completion

**DAP-Async-Accept-If-Expected-Delay-Less-Than**

Clients can make conditional requests for asynchronous responses by
indicating the maximum time they are willing to wait with a
**DAP-Async-Accept-If-Expected-Delay-Less-Than** HTTP header with a
value given in milliseconds.

### HTTP Response Codes

#### 202 Accepted

A server indicates that a request has been accepted and will be handled
asynchronously by returning a **202 Accepted** HTTP response code. The
response body must contain a document in one of the asynchronous
information media types listed [below](#Media_Types "wikilink").

#### 400 DAP Asynchronous Response Required

The **400 DAP Asynchronous Response Required** HTTP response code is
used to indicate that the DAP request has been rejected because an
asynchronous response is required and the client did not indicate
willingness to accept an asynchronous response.

The response code text is used to indicate the reason for the rejection.
However, since the **400** HTTP response code is not specific to
asynchronous DAP (the standard text for the **400** code is "Bad
Request"), the **DAP-Async-Required** HTTP response header is also
included in the response (see
[above](#Accept_DAP_Asynchronous_Response "wikilink")).

**Note** that a standard 400 HTTP response code is returned. In this
way, a client that does not understand asynchronous DAP can fail
gracefully. The response code text message has been changed to be more
informative of the reason for the failure. For clients that are aware of
asynchronous DAP, the "DAP-Async-Required" header is set to "true". The
body of the response also returns some information the client can use to
decide on how it will continue.

An alternative would be to use a non-standard 4xx HTTP response code
(e.g., we could choose 473). Clients should interpret any 4xx code that
they do not recognize as a 400. However, this may not be handled well by
all clients.

#### 412 Precondition Failed

The **412 Precondition Failed** HTTP response code is used to indicate
that the DAP request has been rejected because it did not meet the
**DAP-Async-Accept-If-Expected-Delay-Less-Than** condition (see
[above](#Accept_DAP_Asynchronous_Response_Conditionally_on_Estimated_Time_to_Completion "wikilink"))
that was specified in the request.

### Media Types

Information about an asynchronous response can be represented in one of
the following media types:

- **application/vnd.opendap.org.dap.asynchronous+xml**
- **application/vnd.opendap.org.dap.asynchronous+json**
- **application/vnd.opendap.org.dap.asynchronous+pbuf**

The two main uses of documents in these media types are:

- to inform clients that a request will result in an asynchronous
  response and
- to provide clients with the status of an an accepted asynchronous
  request.

More formal definitions of these media types can be found:

- The XML representation is a document that follows the DAP Asynchronous
  XML schema and is declared in the namespace
  **http://opendap.org/ns/dap/asynchronous**.
- The JSON representation is a document following the definition given
  here (???).
- The protobuf representation is a document following the definition
  given here (???).

### Link Relationships

**http://opendap.org/rel/dap/asynchronous**

The URL of a link with this relation can be used to GET an asynchronous
information resource. The representation received will be a document in
one of the asynchronous information media types listed
[above](#Media_Types "wikilink").

## Rationale for the solution

## Discussion

## Examples

### Constrained Data Request-Response using GET

Request: <font size="2">

    GET /dap/path/data.nc?x,y,temp HTTP/1.1
    Host: server.org
    Accept: application/vnd.opendap.org.dap4.data

</font>

If the server decides it needs to handle this request in an asynchronous
manner, it will refuse the request because it did not say it would
accept an asynchronous response.

Response: <font size="2">

``` xml
400 DAP Asynchronous Response Required
DAP-Async-Required: true
Content-Type: application/vnd.opendap.org.dap.asynchronous+xml;charset=UTF-8

<asynchronous>
  <expectedDelay millisec="600000" />
  <notificationSupport>
    <polling frequencyLimitInMillisecs="60000" />
    <email />
    <longConnect />
    <http />
  </notificationSupport>
  ...
</asynchronous>
```

</font>

### Constrained Data Request-Response with DAP-Async-Accept Request Header

Request: <font size="2">

    GET /dap/path/data.nc?x,y,temp HTTP/1.1
    Host: server.org
    DAP-Async-Accept: true
    Accept: application/vnd.opendap.org.dap4.data

</font>

Response: <font size="2">

``` xml
202 Accepted
Content-Type: application/vnd.opendap.org.dap.asynchronous+xml;charset=UTF-8

<asynchronous>
  <expectedDelay millisec="600000" /> <!-- Estimated delay of 10 minutes. -->
  <notificationSupport>
    <polling frequencyLimitInMillisecs="60000" /> <!-- Don't poll more often than every minute. -->
  </notificationSupport>
  <status value="accepted"
          rel="http://opendap.org/rel/dap/asynch/status"
          type="application/vnd.opendap.org.dap.asynchronous+xml"
          href="http://server.org/dap/path/data.nc-ABCD.status" />
  ...
</asynchronous>
```

</font>

### Constrained Data Request-Response with conditional DAP-Async-Accept Request Headers

Request: <font size="2">

    GET /dap/path/data.nc?x,y,temp HTTP/1.1
    Host: server.org
    DAP-Async-Accept: true
    DAP-Async-Accept-If-Expected-Delay-Less-Than: 60000
    Accept: application/vnd.opendap.org.dap4.data

</font>

Response: <font size="2">

``` xml
412 Precondition Failed
Content-Type: application/vnd.opendap.org.dap.asynchronous+xml;charset=UTF-8

<asynchronous>
  <expectedDelay millisec="600000" /> <!-- Greater than that specified in DAP-Async-Accept-If-Expected-Delay-Less-Than header. -->
  ...
</asynchronous>
```

</font>

### Constrained Data Request-Response using POST

Request: <font size="2">

    POST /dap/path/data.nc HTTP/1.1
    Host: server.org
    DAP-Async-Accept: true
    Content-Type: application/vnd.opendap.org.dap4.ce+xml;charset=UTF-8

    <constraintExp>...</constraintExp>

</font>

Response: <font size="2">

``` xml
202 Accepted
Content-Type: application/vnd.opendap.org.dap.asynchronous+xml;charset=UTF-8

<asynchronous>
  <expectedDelay millisec="600000" /> <!-- Estimated delay of 10 minutes. -->
  <notificationSupport>
    <polling frequencyLimitInMillisecs="60000" /> <!-- Don't poll more often than every minute. -->
  </notificationSupport>
  <status value="accepted"
          rel="self
               http://opendap.org/rel/dap/asynchronous"
          type="application/vnd.opendap.org.dap.asynchronous+xml"
          href="http://server.org/dap/path/data.nc-ABCD.status" />
  ...
</asynchronous>
```

</font>

### Support notification of completion

Request: <font size="2">

</font>

Response: <font size="2">

</font>