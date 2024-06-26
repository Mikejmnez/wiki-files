OPeNDAP provides a way to easily share science data across the internet.
It is used widely in earth-science research settings, but is not limited
to that. Using a flexible data model and a well-defined transmission
format, an OPeNDAP client can request data from a wide variety of
OPeNDAP servers, allowing researchers to enjoy flexibility similar to
the flexibility of the web.

This guide introduces the important concepts behind the OPeNDAP data
model and transmission format, as well as the clients and servers that
use them. It is not a reference for any particular client or server,
though you will find links to that documentation within its chapters.

[Introduction](UserGuideIntroduction "wikilink")
A look at the transmission format, the services provided by the OPeNDAP
servers, and the advantages of using OPeNDAP. All users will find it
helpful to read this.

<!-- -->

[Data Model](UserGuideDataModel "wikilink")
A review of the data types OPeNDAP sends between client and server and
issues involved in translating one to another. Researchers who will be
using OPeNDAP to transfer data may find this useful.

<!-- -->

[OPeNDAP Messages](UserGuideOPeNDAPMessages "wikilink")
A closer look at the messages passed to and from an OPeNDAP server and
the various services that may be provided. Also reviews constraint
expressions, which can be used to select data from specific datasets.
Researchers who will use OPeNDAP to get data for analysis will find this
useful.

<!-- -->

[The OPeNDAP Server](UserGuideServer "wikilink")
The structure of the OPeNDAP server, including a closer look at the
server software provided by the OPeNDAP group itself. People involved in
providing data to OPeNDAP users should read this before looking at the
documentation for the server they will install.

<!-- -->

[The OPeNDAP Client](UserGuideClient "wikilink")
The parts of the OPeNDAP client, including a review of the available
client software, from within as well as outside the OPeNDAP group.
Developers interested in creating new OPeNDAP clients, as well as users
who want to convert a program to a network-aware OPeNDAP client should
read this chapter.