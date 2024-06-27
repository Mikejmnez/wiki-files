The current spec (revision 1.17) contains a note about specifying
lengths using a scheme that does not limit the length to one integer.
The scheme is proposed for the <nop>BitImage type, but I wonder if it
should be used anywhere we specify a length? The scheme is as follows:
the high-order bit is used to indicate whether another length byte
follows and the remaining 7 bits are length value. For example, a length
of 500 would be 1000 0011 0111 0100 (0x83 0x74). The set high-order bit
of the first byte indicates a second byte follows. The cleared
high-order bit of the second byte indicates it's the last byte.

James Gallagher - 15 Sep 2003

Take a look at the [UTF-8](http://www.cl.cam.ac.uk/~mgk25/unicode.html)
FAQ. UTF-8 uses an encoding scheme that is robust. It is easy to spot
missing bytes, for example. OTOH, the scheme Tom has suggested has the
advantage that it'll be easy to code and will never use more than N+1
bytes for somethng that would fit in N bytes as an unsigned integer.

I think we should adopt this unless there's another widely-used scheme
that accomplishes the same goal.

James Gallagher - 26 Sep 2003