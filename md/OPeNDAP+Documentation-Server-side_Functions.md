## Server-side Functions Working Group

### Motivation

May different OPeNDAP server implementations include the ability to
request that the server process the data before returning the data to
the requesting client. In some implementations, the client requests the
processing by embedding the instructions into the URL of the request
(GDS, F-TDS) . Other implementations favor putting the processing
request into the query string of the URL(pyDAP, I think).

This working group will explorer the feasibility of developing a single
method and syntax to request server-processing for common types of
transformations. For client writers and more importantly for users, this
would be a tremendous advantage to be able to make server-side requests
using one syntax regardless of the underlying server implementation.

### Statement of Work

Identify those implementations which offer server-side processing.
Identify a common subset of processing requests. Develop a common
mechanism for embedding the request into the URL and a common syntax for
the identified processing types. Some candidate for requests that could
have a common syntax are sum, average, minimum and maximum along one or
more axes.

### Working Documents

In this section we should build documents that represent the consensus
of our work. Rather than be an open-ended exchange the goal is to
continue to shape these documents so that when consensus is reached,
these documents can be put forward as the specification of the
capabilities and syntax that we agreed should be implemented by those
choosing to participate. Please use the discussion tab and email for the
initial exchange of ideas and consensus building.

1.  [Current Implementations](Current_Implementations "wikilink")
2.  [Proposed Capabilities](Proposed_Capabilities "wikilink")
    - "Server as client" to read other OPeNDAP data sets as part of the
      analysis.
    - Standard functions
    - Escaping to "native" scripts
    - Security (syntax checking, OS "escapes", etc)
    - Capabilities discovery
    - Variable naming (client specified, best practices defaults, etc)
3.  [Proposed Syntax](Proposed_Syntax "wikilink")

### Members

1.  [Roland Schweitzer](mailto:Roland.Schweitzer@noaa.gov)
2.  [Patrick West](mailto:pwest@ucar.edu)
3.  [Rob De Almeida](mailto:rob@pydap.org)
4.  [Antonio S. Cofiño](mailto:antonio.cofino@unican.es)

### Resources

- [The OPeNDAP Functions](mailto:opendap-functions@opendap.org) mail
  list.
- ["Server-side OPeNDAP Analysis - A General Approach Utilizing Legacy
  Applications through TDS" ppt presentation to OPeNDAP Developer's
  meeting
  2007](http://wiki.opendap.org/twiki/pub/Developers/DevMeeting2007Program/RHS-OPeNDAP_Dev_Con.ppt)
- [The GrADS Data Server](http://www.iges.org/grads/gadoc/users.html)
- [SWAMP Presentation from the OPeNDAP
  Meeting](http://wiki.opendap.org/twiki/pub/Developers/DevMeeting2007Program/sld_WZJ07b.pdf)
- [SWAMP Paper from Advances in Grid and Pervasive Computing, Second
  International Conference, GPC
  2007](http://dust.ess.uci.edu/ppr/ppr_WZJ073.pdf)
- [See this section 1445-1715 Server-Side Operations with 1530-1600
  Break](http://wiki.opendap.org/twiki/bin/view/Developers/DevMeeting2007Program)

--Roland Schweitzer (Mon Sep 24 14:21:10 CDT 2007)

[Server Side Functions](Category:Working_Groups "wikilink")