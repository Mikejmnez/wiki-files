## Overview

Most DAP clients are capable of dealing with HTTP BASIC Authentication
situations. However, Shibboleth presents a particular challenge because
it does not adhere to regular HTTP behavior in that it doesn't challenge
the clients with a returned 401 status when a requested resource
requires authentication for access. Rather, Shibboleth initiates a
series of 302 redirects that take the client to an authentication web
form which is returned with a status of 200. And even if the client
insists on presenting its credentials to Shibboleth using HTTP BAsic
authentication this effort will be ignored.

## The Fix

The way to enable a client to work with Shibboleth require that both the
Shibboleth Service Provider (SP) and Identity Provider (IdP) have
enabled the [Enhanced Client or Proxy (ECP)
profile](https://wiki.shibboleth.net/confluence/display/SHIB2/ECP). The
ECP profile was intended to provide Shibboleth authentication access for
non-browser clients.

### Basic modifications to OpenDAP clients

Since the client may never know when it will encounter a Shibboleth
protected resource it must indicate with **every** request that it is
capable of engaging the server in the ECP profile exchange. This is done
by including the mime type *`application/vnd.paos+xml`* in the
*`Accept`* header and by including the *`PAOS`* header. For a client
that is requesting a netcdf-3 response for a DAP resource the request
headers would include:

``` apache
Accept: application/x-netcdf; application/vnd.paos+xml
PAOS: ver="urn:liberty:paos:2003-08";"urn:oasis:names:tc:SAML:2.0:profiles:SSO:ecp"
```

When the requested resource is protected by Shibboleth this will cause
the server to initiate the ECP profile flow by returning a document with
the *`Content-Type`* header set to *`application/vnd.paos+xml`* which
indicates to the client that it needs to process the response as a
Shibboleth ECP profile exchange.

##### Cookies

In addition to including the information in the *Accept* and *PAOS*
headers as described above, clients should be configured to use *HTTP
Cookies*.

##### Summary

In summary, to support Shibboleth, an OpenDAP client should include the
two headers shown above in every out-bound request and be prepared to
process an 'ECP profile' exchange as the result of every response -
similar to the way OpenDAP clients must detect error responses that may
come back from any request. Clients should also support HTTP Cookies.

### Details

The details of the exchange are described briefly on the Shibboleth wiki
here: <https://wiki.shibboleth.net/confluence/display/CONCEPT/ECP> and
in the specification of the ECP profile exchange here:
<http://docs.oasis-open.org/security/saml/Post2.0/saml-ecp/v2.0/cs01/>.
Also, a sample bash shell program that utilizes curl to perform the
access and authentication exchange can be found here:
<https://wiki.shibboleth.net/confluence/download/attachments/4358416/ecp.sh?api=v2>

##### Overview of the processing steps

When a client receives a response that indicates it must authenticate
using Shibboleth (*Content-Type:* set to *application/vnd.paos+xml*), it
must follow this basic flow:

1.  Save off the response location provided by the Service Provider (SP)
    in a SOAP header, as well as optional RelayState.
2.  Check for an optional set of "supported IdPs" from the SP in a SOAP
    header, filtered against IdP(s) the client supports.
3.  Route the SOAP body from the SP to the IdP along with the user's
    credentials



Make sure to strongly verify the IdP's identity while doing so.

1.  Compare the response location provided by the IdP in a SOAP header.



It is an error for these to not match

1.  Route the SOAP body from the IdP back to the SP, along with any
    RelayState information saved from earlier.
2.  Check for a HTTP 200 or 302 response from the SP, indicating success
    or a redirect to the original resource.



You can follow a redirect, or simply preserve the original request from
the beginning of the process internally and replay it.

In practice this means that for steps 1 and 5, a SOAP response must be
parsed by the client and some information must be extracted. That
information, an XML fragment, is then used as part of a HTTP POST in
order to get to the subsequent step. In this example, the processing is
done using *sed* in a [Bash
script](https://wiki.shibboleth.net/confluence/download/attachments/4358416/ecp.sh?api=v2).
Also note that [the python
script](https://wiki.shibboleth.net/confluence/download/attachments/4358416/ecp.py?api=v2)
is also a very readable example.

## Conclusion

One important difference between Shibboleth, URS/OAuth2 and HTTP/S Basic
authentication backed by an LDAP server, is that the latter two work
seamlessly (or for the most part, seamlessly) with common HTTP
implementations. If the HTTP libraries are configured correctly to
process redirects and handle authentication challenges, then those two
authentication schemes will work fine. In particular, the client
software using HTTP libraries will likely never know that an
authentication step took place, meaning that they won't have to be
modified to use OpenDAP servers that require credentials. Or, if they
are modified, it's likely only to correctly support existing HTTP
features.

However, current HTTP libraries don't provide support for Shibboleth and
so the clients must fill the void. This means that OpenDAP client
software must be modified to perform the extra processing steps for
Shibboleth authentication, especially the steps involving SOAP messages.
These extra processing steps could be encoded in some of the OpenDAP
library software, relieving much of the burden on application
developers, but as is the case with the HTTP libraries (e.g., libcurl),
that has yet to happen.