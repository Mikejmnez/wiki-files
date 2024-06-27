The problem facing the netCDF 3 handler is that it needs to provide a
representation of character arrays so that common clients can easily use
those types. These character arrays are often used in netCDF 3 to hold
strings. Thus, one logical choice is to encode a netCDF 3 character
array as a DAP String. However, a client that models data as arrays
where the size of all (or most) data objects is know prior to accessing
their value will have a hard time with the DAP String since it's
declaration does not convey its length.

## Problem Summary

*This is from an email from John Caron*

The semantic mismatch between OpenDAP and netCDF has led to several
possible conventions in mapping from netCDF to OpenDAP on the server,
and from OpenDAP to netCDF on the client. One design goal is that netCDF
files on the server should be semantically equivalent as seen by a
client using the netCDF API. Another goal is to make the common case of
a rank 1 char array in netCDF map to an OpenDAP String.

Older versions of the netCDF - OpenDAP C++ library mapped char\[n\]
arrays to a DArray of element type DString, where each DString has
length 1. Currently, the following convention is the "correct" way for a
OpenDAP server to represent netCDF char\[n\] arrays:

1.  A char\[n\] array maps to a DString. (this is the common case)
2.  A rank k char\[n, m, …, p, q\] NetCDF array maps to a rank k-1
    OpenDAP DArray\[n, m, …p\] of element type DString, where each
    DString has length q. An attribute "strlen" is added to the
    variable, inside an attribute table called "DODS" (to distinguish it
    as an attribute added by the DODS layer). The strlen attribute means
    that all of the DString data elements have the same data length (in
    this example, length q).

<!-- -->

    Attributes {
        var1 {
            DODS {
            Int32 strlen 54;
        }
        }
    }

### Another Approach

Also from John: Another approach to this problem is "to add a 'this is
really a char' attribute and use a byte type. At the moment, that seems
like a simpler choice."

## Discussion and Resolution

Dennis suggested that we used an array of Byte for the character array.
This means that we would loose the notion of string-ness but there would
be no size ambiguity. We could add an attribute that the Byte array
contains data from the char type.

[jimg](User:Jimg "wikilink") 16:08, 8 January 2009 (PST) My suggestion
is that we adopt the solution John has implemented in TDS and the Java
DAP since there's code (client code in particular) already out there
that recognizes it. I will look into the changes that need to be made in
the netCDF handler and also how those will affect the more prominent
clients.

[jimg](User:Jimg "wikilink") 11:35, 9 January 2009 (PST) I just looked
at the code and the three files we will need to modify are NCArray.cc,
NCStr.cc and ncdds.cc. We might look at the whole attribute thing, too.