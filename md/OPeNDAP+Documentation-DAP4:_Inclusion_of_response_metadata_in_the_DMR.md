[\<-- back to OPULS Development](OPULS_Development "wikilink")

James Gallagher

## Background

At the recent ESIP meeting, as well as the DataOne meeting that
immediately preceded it, many people mentioned that responses from
services would be more useful if they contained some *meta* information
about the response. This information would help users understand how the
response was made; it is essentially *provenance* information without
the heavyweight burden that it contain everything known about those
data.

## Problem addressed

When users are confronted with web services (or feel confronted) they
often wonder about the origin of the data they are accessing. Local data
does not typically present this problem because there are often people
close by who can answer questions about the data's origin and subsequent
processing. Binding information to the data response that holds this
kind of information is one way to address a fundamental issue with
remote data access systems. NB: These same issues (will) arise with file
transfer systems, but users seem more comfortable (or less
uncomfortable...) with using data from them. Also, DataOne in
particular, addresses a user population that is less likely to have used
remote data archives in the past and so, as a group, are less accepting
of the (remote) data's validity. These new users are raising valid
questions about how they can tell if data are suitable for their needs.

## Proposed solution

Bundle information in the DMR. The information should include:

1.  where it came from;
2.  the source data set(s);
3.  processing that happened;
4.  the software version;
5.  how to cite the dataset in a publication;
6.  licensing information/restrictions.

Based on a cursory reading of the [PROV-O
specification](http://www.w3.org/TR/prov-o/), I think that notation
could be used for all of this.

~~The information will be encoded in a new XML element to be added to
the DMR called *DAPResponseProvenance*.~~ The information will be
encoded in a DAP4 attribute called *DAPResponseProvenance* that will use
the type *OtherXML*. The contained XML will use PROV-O (or, as Patrick
West points out, the related *PROV* or *PROV-XML*) standards from the
W3C. That change to a DAP Attribute of type OtherXML from a new XML
element is from Dennis and Ethan.

While this could get very detailed, I propose the following for the six
kinds of information listed:

where it came from: The URL used to access the resource.
the source data set(s): Filename (not pathname, though). For an aggregation, the name of the NCML file. Extension: for aggregations, list all files touched. Because this might be complicated, it might not make an initial version.
processing that happened: This would be the name of the access (DMR, Data, ...). Extension: any server functions that were used.
the software version: This will vary for different servers (it might be one number or a list or numbers), but in the end it is one or more of the tried and true *x.y.z* version numbers along with names.
how to cite the dataset in a publication: Nominally a DOI or instructions. This would hopefully be boilerplate for a given server, although having it vary will be what users want since there's a move to have dataset citations list people who did the work and an institutional server will provide access to datasets with several different authors for different datasets.
licensing information/restrictions: URL reference to a license.

## Rationale for the solution

Adding the information to the response is important - an alternative is
to provide a URL that has some or all of it, but that seems too easy to
break (and depends on the network always being present).

Whether the information is encoded in DAP attributes or using non-DAP
XML is a bit harder. Using plain XML seems better because to encode the
information as DAP attributes means developing our own set of
conventions or using PROV-O (or the equivalent) but jammed into DAP
attributes. No software will understand this out of the box. Using
PROV-O in plain XML should make it accessible to generic XML tools as
long as they can be configured to get the element that contains the
information.

## Discussion

[Jimg](User:Jimg "wikilink") 11:50, 30 July 2013 (PDT) Adopted the
suggestion from Dennis and Ethan to use OtherXML for the provenance
information.