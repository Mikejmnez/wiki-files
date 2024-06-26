Should the `Capabilities` response include information about datasets or
should we use THREDDS catalogs for that?

Jose has proposed enabling several CE syntaxes. Should that information
go here?

Should server version information go here? James Gallagher - 17 Sep 2003

**Note**: Version information is now returned in two places:

1.  The DAP protocol version is returned in the HTTP header `XDAP`
2.  The versions of software used by a server is returned as part of the
    DAP 3.x `version` response. This XML document may also contain
    information about installed server-side functions. Jame Gallagher 24
    June 2008.