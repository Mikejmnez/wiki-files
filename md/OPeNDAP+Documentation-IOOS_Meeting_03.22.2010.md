## WCS Semantic Software Activities

### Code Review

Need code review to see if we can make it more robust and efficient. I
think there are patterns of use in the code that can be factored into
common methods. [Ticket 1518](http://scm.opendap.org/trac/ticket/1518)

### Why are updates so slow? [Ticket 1521](http://scm.opendap.org/trac/ticket/1521)

It takes a long time to verify if changes have been made to the imports.

- Even when no changes have been made to the imports list, semantic
  operations are still run. This seems counterintuitive: Can we change
  that? If nothing has changed then no additional semantic operations
  should be undertaken. Currently the last modified times of the imports
  are stored in the repository. This seems to be a very slow and
  unreliable mechanism for reevaluating the imports.
- We need to evaluate what do to when things have changed. Is it more
  expedient to remove and replace changed values from the repository, or
  should we just rebuild the whole thing?
- Can we pre-compute a starting point repository that contains all of
  the inferencing rules and ontologies? This would allow us to avoid the
  long slow acquisition of all of these files every time we rebuild the
  repository.
- Should we consider caching the import list and last modified times as
  directly accessible java objects so that we can write simple java
  procedural code to evaluate the imports list?

### Caching

At start up the catalog is empty prior to the completion of the semantic
operations. [Ticket 1517](http://scm.opendap.org/trac/ticket/1517)

- Should we cache the complete catalog? (Currently Haibo is writing out
  the results of the repository processing, but since additional
  elements are added by the java code his copy is not complete.)
- Should we consider making the service simply create the appropriate
  inputs for the LocalFileCatalog and skip more tightly coupled
  integration?

### Servlet/Webapp

Let's make the WCS service a stand alone web app. A seperate jar file
that runs in it's own context in the servlet engine.

- Fix the local/universal ID problem. [Ticket
  1511](http://scm.opendap.org/trac/ticket/1511)
- Re-factor the code as a servlet (or maybe even as its own web app)
  with it's own servlet context. [Ticket
  1516](http://scm.opendap.org/trac/ticket/1516)

### Additional Semantic Functions

DAP Variable Access [Ticket 1520](http://scm.opendap.org/trac/ticket/1520)
Need to access the data values of dap arrays from the inferencing. In
particular, we need the first and last values of arrays associated with
map vectors (or more likely their "bounds" vectors) so that we can
create bounding boxes. A function should take dataseturi, local variable
names, and indexing constraints expressed in some reasonable fashion

<!-- -->

General semantic Function framework [Ticket 1519](http://scm.opendap.org/trac/ticket/1519)

## GeoTIFF Module

See [GeoTIFF responses for
Hyrax](GeoTIFF_responses_for_Hyrax "wikilink")

## KML Module

See [KML_responses_for_Hyrax](KML_responses_for_Hyrax "wikilink")

## THREDDS Catalog Clients

IDV seems like it might be a client that can navigate catalogs. (Not
surprising, considering it's a UNIDATA thing)

Several clients demonstrated at IOOS meeting that allowed users to
navigate THREDDS catalogs in search of data:

- Environmental Data Connector
- ERDDAP
- IOOS National Catalog Viewer (See
  [\#THREDDS_Catalog_Metadata](#THREDDS_Catalog_Metadata "wikilink"))

## THREDDS Catalog Metadata

See [THREDDS Catalog Metadata](THREDDS_Catalog_Metadata "wikilink")

## Dap Capabilities Response

See [DAP Capabilities](DAP_Capabilities "wikilink")

## DAP Asynchronous Responses

Comment added to [DAP 4.0
Design](DAP_4.0_Design#Organization_of_the_multipart_MIME_document "wikilink")

## DAP 3.x and 4.x

- TDS and new DAP standards

:;Claim: For DAP 3.x and 4.x to become widely adopted we will need to
get the TDS to become compatible with these versions.



The TDS is clearly the dominant DAP service software. This position
makes it difficult (if not impossible) for us to push the DAP standards
forward without enabling the TDS. INn order for the TDS to produce DAP
3.x and 4.x output we will need to start supporting these updated
protocols in the Java-DAP.

:;Assertion



If we don't do this, then changes to the DAP will be irrelevant.

- Do we want to make the DAP 3.2 DDX the default DDX response?
  - Are there any users of the DAP 2.0 DDX?

## To Do

- Look at [Fudge Messaging](http://www.fudgemsg.org)