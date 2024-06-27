## Overview

The LocalFileCatalog is an implementation of the
opendap.wcs.v_1_1_2.WcsCatalog interface. It was designed as a temporary
implementation of WcsCatalog that would give developers a simple catalog
from which to work so that they could to proceed with work on other
parts of the service.

The WCS Service project's original intent (and the goal for the future)
is to provide a catalog implementation that utilizes semantic web
technologies to generate the WCS catalog content from existing (and
probably supplemented) metadata from within the existing OPeNDAP data
framework.

However in there are many more fish to fry with respect to WCS than just
the catalog issue, so in an effort to move that part of the process
forward we have implemented a simple catalog that reads
wcs:CoverageDescription elements local files.

Catalog Implementation Class Name
opendap.wcs.v1_1_2.LocalFileCatalog

The *LocalFileCatalog* is implemented to use a directory in the local
file system. It assumes that every file in the directory is an XML
document that contains a single wcs:CoverageDescription element. The
files are ingested and QC'd at start up.

If you haven't noticed by now, this implementation requires that for
each *Coverage* its wcs:CoverageDescription must be written by another
entity - quite probably a human.

### Configuration (wcs_service.xml)

The local file catalog takes as a configuration element the path to the
directory containing the prebuilt wcs:CoverageDescription documents.

<?xml version="1.0" encoding="UTF-8"?>

<WcsService>
`    `<b>
`   `<WcsCatalog className="opendap.wcs.v1_1_2.LocalFileCatalog">
`        `<CoveragesDirectory>`/absolute/path/to/the/directory/containing/the/wcs:CoverageDescription/documents`</CoveragesDirectory>
`    `</WcsCatalog>
</b>
</WcsService>

[Category:WCS](Category:WCS "wikilink")