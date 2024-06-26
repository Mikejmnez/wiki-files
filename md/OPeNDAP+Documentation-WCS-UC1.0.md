**Point Of Contact:** Mr. Patrick West

## Description

WCS GetCoverage request with a pre-built response

## Goal

The goal of this use case is to provide an end-to-end request/response
to a GetCoverage request. It is intended to get the OLFS and BES
involved in the WCS system, with the BES providing the response to this
request.

## Summary

The primary actor, a person sitting at a computer, enters a pre-defined
URL into their browser for a GetCoverage request and receives a
Coverages document that contains a Coverage element that has a Reference
element that links to a target NetCDF-CF response to be saved to their
local disk.

We are attempting to coordinate communication between the OLFS and the
BES in this WCS system, with a request coming in to the OLFS from the
users browser, the OLFS formulating the request to the BES, and the BES
responding to that request with a Coverages document containing a
Reference element with the the link to the target NetCDF-CF file.

The request and the response will be pre-defined. In other words, the
user won’t be browsing around the catalogues or making GetCapabilities
or DescribeCoverage calls and the BES will have a pre-built response,
which is a a Coverages document (file) containing a Reference element
with the the link to the target NetCDF-CF file.

## Actors

1.  Primary actor – user sitting at browser
2.  Secondary actor – OLFS – receives the request from the browser
3.  Secondary actor – BES – receives request from the OLFS and responds
    to the request
4.  Secondary actor – pre-built Coverages document saved as a file.
5.  Secondary actor – pre-built target NetCDF-CF file

## Preconditions

1.  Using the WCS 1.1.2 specification
2.  The request URL is a known request, provided for this use case
3.  The request parameters are in KVP format
4.  The request parameters version, request type and Id are already
    known
5.  The domain subset is in WGS84 BB, Circumscribe N.J. on grid vertices
6.  The temporal subset is optional and will be left out
7.  The range subset is optional and will be left out
8.  The target response file format will be NetCDF-CF
9.  The response from Hyrax will be a Coverages document containing a
    Coverage with a Reference element containing a link to the pre-built
    NetCDF file.

## Triggers

The user enters the provided URL representing the GetCoverage request
into the browser

## Basic Flow

1.  OLFS receives the request
2.  OLFS parses the KVP parameters from the request URL including the
    version, request type, and id
3.  OLFS sets the domain subset (bb)
4.  OLFS sets the temporal subset (optional ???)
5.  OLFS sets the range subset (optional ???)
6.  OLFS sends the request to the BES as an XML document
7.  The BES parses the XML document representing the GetCoverage request
8.  The BES matches the coverage id (and other input)
9.  The BES returns a pre-built Coverages document that a Coverage
    element with a Reference containing a link to the pre-built response
    file, which is in NetCDF-CF format
10. The OLFS returns the Coverages document to the user
11. The user receives the response, and locates the Coverage containing
    the Hyrax URL to the target response file. The user takes that URL
    and enters it into their browser.
12. The OLFS sends the request to the BES to stream the target response
    file back to the user. This functionality already exists (get stream
    for definition).

## Alternate Flow(s)

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
4.  In Basic Flow step 9, the BES fails to access the file containing
    the pre-built Coverages document.
5.  The BES builds a WcsException element and returns it to the user via
    the OLFS.

## Post Conditions

None.

## Activity Diagram

<figure>
<img src="WCS-UC1.0-ActivityDiagram.png"
title="WCS-UC1.0-ActivityDiagram.png" width="600" />
<figcaption>WCS-UC1.0-ActivityDiagram.png</figcaption>
</figure>

## Notes

**Question:**


Will the BES be streaming the response file back to the OLFS, and then
on to the user? Or will the BES return a URL to the response file that
the user will then click on to download?

**Answer:** The BES will return a link to the target file, not stream
the file back. The user will then be able to click on this link and save
it to their local disk.

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

Use Case 1 - GetCoverage

Precondition

- Version, Request Type, Id already known
- KVP request to OLFS
- Domain Subset
- BB (WGS84 BB) Circumscribe N.J. on grid verteces
- Temporal is optional and will be left out
- Range Subset: optional
- Format: NetCDF-CF

Trigger

- Request from user

Sequence of Events

- Request to OLFS/Handler
- KVP parsing
  - Request Type, Version, Id
- Set Domain Subset (bb)
- Set Temporal Subset
- Set Range Subset
- Generate coverage response
  - Match id and other stuff
  - ask BES for the response
  - BES returns pre-built response

[Category:WCS](Category:WCS "wikilink")