See also [REAP Cataloging and
Searching](REAP_Cataloging_and_Searching "wikilink")

## Implementation

### Version 0.8, 3/5/10

The crawler will crawl a THREDDS catalog, visiting all of the datasets
it lists and, recursively, all of the child catalogs it contains. The
traversal is breadth-first, so each dataset listed in a catalog is
examined before crawling the child catalogs. To limit looping when two
catalogs are mutually referential, the crawler records each catalog URL
and uses that record to ensure that each catalog is examined only once.
In addition the crawler uses a persistent store (serialized
ConcurrentHashMap) to record each dataset examined. Because these
operation are potentially expensive, this is used to stop retrieving and
processing DDX documents from datasets already examined. The HTTP 1.1
Conditional GET request is used to access DDX documents only when they
are different from the versions previously accessed.

What the crawler does not yet do:

- The crawler is not multi-threaded
- It does not build EML documents from the DDX documents
- It does not store the results in metacat

#### The code

The Crawler is in SVN at
<http://scm.opendap.org/trac/browser/branch/ioos/olfs>, but the code
specific to the crawler is in
<http://scm.opendap.org/trac/browser/branch/ioos/olfs/src/opendap/metacat>
and can be built using the ant target *DapIngest* (and run from ant
using *dap_ingest*).

There is code to process the DDX and build EML using XSLT at
<http://scm.opendap.org/trac/browser/trunk/reap>

[Category:REAP](Category:REAP "wikilink")
[Category:THREDDS](Category:THREDDS "wikilink")