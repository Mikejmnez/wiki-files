### 64-bit data types

Should we add 64-bit integer datatypes to the DAP? Issue: 64-bit
integers are going to be everywhere. Or are they? Adding this type will
make writing clients harder because the clients will have to deal with
types they may not be able to represent unless lots of extra work is
done. But 64-bit is the future...

(Comment: If we will have to include 64 bit integers in the future and
re-do the software, it's better to include it in the spec now so that
future software is future- i.e. forward- compatible. Paul Hemenway 18
Sept. 03)

### Enumerations

Should we support enumerations? I noticed that ASN.1 does and I remember
that their absence in Java was/is a big deal. There are good reasons to
keep enums out of a general programming language (See Lakos, Large Scale
C++ Software Design) but a transport system might really benefit from
them. The flip side is that after 7+ years of using the DAP, we haven't
needed them enough to make this exactly a top priority. Comments?

James Gallagher - 19 Sep 2003

### Time

How about adding a data type specifically for holding [ISO
8601](http://www.cl.cam.ac.uk/~mgk25/iso-time.html) date/times? I talked
about this with Peter C., et al., and came to the conclusion that it's a
pretty interesting idea. I *think* ASN.1 has a type for this (my ASN.1
book is at home...). It seems like time is pretty universal (for example
a word processing doc needs time, so does an OS) and not just a 'science
thing'.

James Gallagher - 26 Sep 2003

### Boolean

It seems a boolean type should be included. It's part of netCDF4 (See
the UML for netCDF4 in AbstractModel).

James Gallagher - 09 Oct 2003

### 64-bit Floating Point values

I realize this seems a bit far fetched, but most RDBMS systems support
these big floats, as do some programming languages (at least Java has
java.math.BigDecimal) Is there utility in this type? I have brought it
up before and generally folks seemed to think it silly, but we might as
well capture the arguments.

Nathan Potter - 09 Oct 2003

My own inclinations on these issues:

- 64-bit ints (java longs) : Yes
- booleans : Yes
- date/time : Yes probably, but there are complicated issues with
  calendars. I think we should just use the standard Gregorian calendar,
  and anyone using a different calandar cant use the date/time type.
- enums : no, don't seem necessary
- BigDecimal: no, don't seem necessary

Some other questions:

1.  HDF5 has "opaque" type, not sure why this isn't equivalent to "byte
    array", so I'm inclined not to use.
2.  Are OpenDAP Strings ASCII, or do they have an encoding flag that
    would allow them to contain, e.g., Unicode? I think an (optional)
    encoding flag would be good.

John Caron - 13 Oct 2003

### Char

Suggestion: The DAP would benefit from a =char= datatype since APIs like
netCDF use this to store strings. It is much easier to use a char than
to encode char arrays as strings (which includes the assumption that
those arrays *are* strings) and then undo that encoding on the
client-side (where the client may not be able to represent strings, as
is the case with the netCDF client-library).

An alternative view is that a server that is reading from some source of
character data and representing that as a String (or array of Strings)
can add an attribute indicating the maximum length of the string(s). For
some servers this will be hard, but for some like the netcdf server,
it's easy to figure out. The attribute information would be optional, so
if it's hard for a server to figure it out, it doesn't have to. The
absence of the attribute is a clue to the client that the strings are
*not* regular but are of varying length.