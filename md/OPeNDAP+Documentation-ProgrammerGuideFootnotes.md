1.  While this manual describes the C++ toolkit in detail, all of the
    concepts and much of the structure can be directly translated to the
    Java toolkit.
2.  Whatever it is. The DODS client can be another server, a user
    application linked with a DODS-compliant API, or a standalone
    program using the DODS data access protocl API. In any case, the use
    of the class libraries described in this document is identical.
3.  Actually, there is no reason that type, etc. cannot be stored as an
    attribute; however, it must be in the DDS regardless
4.  A Pix is a "pseudoindex" object. See the libstdc++ documentation for
    more information.
5.  For example, the subclasses JGConnect and NCConnect exist for JGOFS
    and NetCDF, respectively.
6.  A single entry in a sequence, modelled as a row in a relational
    table, is sometimes called an instance of the sequence. This is
    useful terminology, but is occasionally confusing when we are also
    talking about instances of objects.
7.  The NetCDF client library has been rewritten a couple of times since
    these examples were taken from it. Most of the changes involve
    additional functionality not necessary for the illustrations here.
    We have also removed some error-checking to make the intention
    clearer. Therefore the actual code in the NetCDFsoftware may not
    match these examples. However, these examples will work.
8.  The nph- is a relic, dating from the misty dawn of the World Wide
    Web and the first http standards. It stands for "Non-Parsing Header"
    (See the CGI 1.1 Standard for more information.), and is the only
    way to pass data through many httpd servers unparsed.
9.  You can use this even if you want to access files outside that
    subtree. Simply use a symbolic link and make sure that your server
    is set to follow symbolic links.
10. The "Data:" keyword is not in the scope of the text DDS so it is
    possible to have the text Data: in the DDS.
11. Remember that the "how" is to be answered very specifically, and on
    the user's level (i.e. "Do such-and-such, spelled like this, to make
    the array returned be nx5 instead of 5xn."), and not on the
    programmer's level (i.e. "You use the invert method to return an
    array of 5xn instead of nx5.")
12. For the Sequence data type, the DDS contains only the current
    instance of the data. Repeated calls to the Sequence's deserialize
    function are required to return successive instances of the
    sequence.