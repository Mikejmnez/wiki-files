## Authentication Working Group

### Motivation

When it is necessary to restrict access to OPeNDAP datasets, the server
must challenge the user to **authenticate**, and process the request (or
not) based on whether the user is **authorized** to access the dataset.
Unless there is a specified standard, it is possible that the various
servers and clients may not be able to interoperate.

### Statement of Work

This working group will propose a standard on how OPeNDAP servers and
clients authenticate. The standard will allow any conformant OPeNDAP
server and client to interoperate. It will specify the interaction
between client and server, not the implementation. However, it may also
provide a "best practices" or "experience to date" document to guide
implementation.

### Members

1.  [John Caron](mailto:caron@unidata.ucar.edu)
2.  Patrick West
3.  Nathan Potter
4.  Bryan Lawrence
5.  Jerry Pan (mailto:pany@ornl.gov)
6.  Luca Cinquini
7.  Frank Siebenlist
8.  [Rob De Almeida](mailto:rob@pydap.org)

### Resources

- ["TDS and OPeNDAP security" ppt presentation to OPeNDAP Developer's
  meeting
  2007](http://www.unidata.ucar.edu/staff/caron/presentations/OpendapSecurity.ppt)
- [HTTP Authentication in Netcdf-Java
  library](http://www.unidata.ucar.edu/software/netcdf-java/reference/HTTPauthentication.html)

--[JohnCaron](User:JohnCaron "wikilink") 14:54, 20 April 2007 (PDT)

[Authentication](Category:Working_Groups "wikilink")