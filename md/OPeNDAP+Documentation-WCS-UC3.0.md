**Point Of Contact:** Nathan Potter

## Description

WCS GetCapabilities request with pre-built response.

## Goal

The goal of this use case is to provide an end-to-end request/response
to a GetCapabilities request. It is intended to get the OLFS and BES
involved in the WCS system, with the BES providing the response to this
request.

## Summary

The primary actor, a person sitting at a computer, enters a pre-defined
URL into their browser for a GetCapabilities request and receives a
pre-built Capabilities document.

We are attempting to coordinate communication between the OLFS and the
BES in this WCS system, with a request coming in to the OLFS from the
users browser, the OLFS formulating the request to the BES, and the BES
responding to that request with the Capabilities document.

The request and the response will be pre-defined. In other words, the
user won’t be browsing around the catalogues or making DescribeCoverage
or GetCoverage calls and the BES will have a pre-built response, which
is a Capabilities document stored in a file.

## Actors

1.  Primary actor – user sitting at browser
2.  Secondary actor – OLFS – receives the request from the browser
3.  Secondary actor – BES – receives request from the OLFS and responds
    to the request
4.  Secondary actor – pre-built Capabilities document saved as a file.

## Preconditions

1.  Using the WCS 1.1.2 specification
2.  The request URL is a known request, provided for this use case
3.  The request parameters are in KVP format
4.  The values of the request parameters *version* and *type* are
    already known
5.  The response from Hyrax will be a Capabilities document containing a
    CoverageSummary of the Coverage developed in [Use Case
    1.0](WCS-UC1.0 "wikilink").

## Triggers

The user enters the provided URL representing the GetCapabilities
request into the browser

## Basic Flow

1.  OLFS receives the request
2.  OLFS parses the KVP parameters from the request URL including the
    version and request type.
3.  OLFS sends the request to the BES as an XML document
4.  The BES parses the XML document representing the GetCapabilities
    request
5.  The BES returns a pre-built Capabilities document containing a
    CoverageSummary of the Coverage developed in [Use Case
    1.0](WCS-UC1.0 "wikilink").
6.  The OLFS returns the Capabilities document to the user

## Alternate Flow

### Error Flow (OLFS)

1.  OLFS receives the request
2.  OLFS attempts to parse the KVP parameters from the request.
3.  The parsing encounters a bad request (based on the 1.1.2
    specification), or …
4.  The OLFS fails to connect to, or the connection drops to, the BES in
    Basic Flow step 3, or …
5.  The OLFS fails to connect to, or the connection drops to, the BES in
    Basic Flow step 5 …
6.  OLFS builds a WcsException and returns it to the user.

### Error Flow (BES)

1.  See Basic Flow steps 1-3
2.  The BES fails to access the pre-built Capabilities response
    document.
3.  The BES builds a WcsException element and returns it to the user via
    the OLFS.

## Post Conditions

None

## Activity Diagram

<figure>
<img src="WCS-Uc3.0.png" title="WCS-Uc3.0.png" width="600" />
<figcaption>WCS-Uc3.0.png</figcaption>
</figure>

## Notes

*There is always some piece of information that is required that has no
other place to go. This is the place for that information.*

## Resources

### Data

|               |                  |                 |                          |              |                  |
|---------------|------------------|-----------------|--------------------------|--------------|------------------|
| Data          | Type             | Characteristics | Description              | Owner        | Source System    |
| HF Radar Data | Remote netCDF-CF | One Time Slice  | Contains one time slice. | Dan Holloway | dev1.opendap.org |

### Application Services

|             |         |                                                                                                            |                  |
|-------------|---------|------------------------------------------------------------------------------------------------------------|------------------|
| Application | Owner   | Description                                                                                                | Source System    |
| OLFS        | OPeNDAP | OPeNDAP Hyrax front end service – receives request from the user’s browser                                 | dev1.opendap.org |
| BES         | OPeNDAP | OPeNDAP Hyrax back-end server – receives request from OLFS and responds with link to target NetCDF-CF file | dev1.opendap.org |

### Other Resources

|                 |                                            |                                     |                                       |                                          |
|-----------------|--------------------------------------------|-------------------------------------|---------------------------------------|------------------------------------------|
| Resource        | Owner                                      | Description                         | Availability                          | Source System                            |
| *(sensor name)* | *Organization that owns/ manages resource* | *Short description of the resource* | *How often the resource is available* | *Name of system which provides resource* |

## Butte Meeting Notes

Use Case 3 - GetCapabilities

Preconditions

- Static XML
- Input to server is kvp
- set version number
- only one coverage available

Trigger

- Request comes into Hyrax

Sequence of Events

- New WCS Servlet/Handler (modify OLFS)
- Parse KVP
- Identify request type and version
- static doc resides in ...
- Returns XML doc

------------------------------------------------------------------------

[Category:WCS](Category:WCS "wikilink")