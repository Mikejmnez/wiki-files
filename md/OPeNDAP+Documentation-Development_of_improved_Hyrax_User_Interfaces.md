The Hyrax data server provides one of the simplest User Interfaces (UIs)
that OPeNDAP supports. While it has not undergone any significant change
in almost a decade, that interface remains one of the most widely used
with which to access data using our server. In the intervening time
since it was first implemented, many new technologies have emerged which
greatly expand the capabilities of a web-based UI. In this proposal we
outline the enhancements possible to this popular interface (known as
the ‘HTML form interface’ as well as more colorful names) and the
benefits those enhancements would bring to users.

Why the ‘HTML Form Interface’ is so important:

- Ubiquitous: The interface is part of the server so it is always
  present
- It can work with any data served: It is the LCD of UIs

## Features

What features are proposed for the interface?

### Geo-Data Awareness

- The interface (UI) will recognize geo-referenced Grid variables;
  present a map, a time line and use geogrid() functions when building
  requests
- The UI will also recognize geo-referenced Arrays and enable Grid-like
  selection capabilities using values gleaned from DAP attributes

Requires the ability to interpret metadata in the DDX. I think James'
idea is a heuristic, but we might consider code that can process CF-1.0
as a starting place. That won't get bounding box, but if UDD-1.0 is
added then it could work. We might involve the semantic engine work that
has been going in the WCS to get the CF and UDD metadata explicitly
(Java/OLFS). We might just leverage the code associated with geogrid()
and use that to identify the geo-metadata (C++/BES).

If certain features exercise logic about geo-location (like checking for
the extents of Map vectors, applying metadata convention semantics, etc)
then we would want to think carefully about where that happens.

### Time Awareness

- Add time as a recognized type in the UI (at least)

### Server Analysis

- The UI will examine a server and suggest using an aggregation if the
  data set currently viewed is part of one hosted by that server (does
  not check for an aggregation managed by a remote server).

This would need to happen in the BES, or the BES would have to able
answer the question via the BES XML API, or the BES would have to embed
that information in the DDX.

### Structural Content Improvements

- UI will use AJAX where it can to be more interactive
- The UI will reduce clutter to improve the display of large data sets
  (using AJAX) and other techniques.

## New server features will further increase the interface’s usefulness

- The netCDF file output means that the UI can deliver data in a form
  that is more easily used. Currently the data are delivered in DAP
  binary form, something that only a handful of clients can use. The
  netCDF file response will expand the set of ‘clients’ to all of the
  software that can read netCDF files (this was added and released as
  part of Hyrax 1.5 in early April 2009).
- Expanded server-side functions will simplify writing complex
  Constraint Expressions. Much of the data served by DAP is geospatial
  data but its storage precludes easily using it as such. The new
  server-side functions will provide a new interface which greatly
  simplify this use
- Aggregation will enable the server to collapse certain operations that
  used to take several ‘trips’ to the server into a single request which
  makes a form interface, which is fundamentally non-interactive, easier
  to use.

## Implementation Plan

[Javascript Data Request Form](Javascript_Data_Request_Form "wikilink")

[Category:Google Summer of
Code](Category:Google_Summer_of_Code "wikilink")