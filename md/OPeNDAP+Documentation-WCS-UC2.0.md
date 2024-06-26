**Point Of Contact:** Nathan Potter

## Description

WCS DescribeCoverage request with pre-built response.

## Goal

The goal of this use case is to provide an end-to-end request/response
to a DescribeCoverage request. It is intended to get the OLFS and BES
involved in the WCS system, with the BES providing the response to this
request.

## Summary

The primary actor, a person sitting at a computer, enters a pre-defined
URL into their browser for a DescribeCoverage request and receives a
pre-built CoverageDescriptions document.

We are attempting to coordinate communication between the OLFS and the
BES in this WCS system, with a request coming in to the OLFS from the
users browser, the OLFS formulating the request to the BES, and the BES
responding to that request with the CoverageDescriptions document.

The request and the response will be pre-defined. In other words, the
user won’t be browsing around the catalogues or making GetCapabilities
or GetCoverage calls and the BES will have a pre-built response, which
CoverageDescriptions document stored in a file.

## Actors

1.  Primary actor – user sitting at browser
2.  Secondary actor – OLFS – receives the request from the browser
3.  Secondary actor – BES – receives request from the OLFS and responds
    to the request
4.  Secondary actor – pre-built CoverageDescriptions document saved as a
    file.

## Preconditions

1.  Using the WCS 1.1.2 specification
2.  The request URL is a known request, provided for this use case
3.  The request parameters are in KVP format
4.  The request parameters *version*, request *type* and *id* are
    already known
5.  The response from Hyrax will be a CoverageDescriptions document
    describing the Coverage developed in [Use Case
    1.0](WCS-UC1.0 "wikilink").

## Triggers

The user enters the provided URL representing the DescribeCoverage
request into the browser

## Basic Flow

1.  OLFS receives the request
2.  OLFS parses the KVP parameters from the request URL including the
    *version*, request *type*, and *id*
3.  OLFS sends the request to the BES as an XML document
4.  The BES parses the XML document representing the DescribeCoverage
    request
5.  The BES matches the coverage id.
6.  The BES returns a pre-built CoverageDescriptions document that
    contains a CoverageDescription describing the Coverage used in [Use
    Case 1.0](WCS-UC1.0 "wikilink").
7.  The OLFS returns the CoverageDescriptions document to the user

## Alternate Flow

### Error Flow (OLFS)

1.  OLFS receives the request
2.  OLFS attempts to parse the KVP parameters from the request.
3.  The parsing encounters a bad request (based on the 1.1.2
    specification), or …
4.  The OLFS fails to connect to, or the connection drops to, the BES in
    Basic Flow step 6, or …
5.  The OLFS fails to connect to, or the connection drops to, the BES in
    Basic Flow step 12 …
6.  OLFS builds a WcsException and returns it to the user.

### Error Flow (BES)

1.  See Basic Flow steps 1-7
2.  The BES fails to match the coverage id or some other input
    (Unsupported operations, CRS mismatch, etc.), or …
3.  The BES fails to access the pre-built coverage response document, or
    …
4.  In Basic Flow step 6, the BES fails to access the file containing
    the pre-built CoverageDescriptions document.
5.  The BES builds a WcsException element and returns it to the user via
    the OLFS.

## Post Conditions

None

## Activity Diagram

<figure>
<img src="WCS-Uc2.0.png" title="WCS-Uc2.0.png" width="600" />
<figcaption>WCS-Uc2.0.png</figcaption>
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

Preconditions

- Request type is describe coverage
- coverage id is known
- request is KVP
- describe coverage XML is static

Trigger

- Incoming request from user

Sequence of Events

- Extend OLFS
- check request and version and id
- verify version
- parse kvp query string
- if id is valid, return xml doc
  - get doc location from config

[Category:WCS](Category:WCS "wikilink")