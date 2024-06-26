1.  The phrase distributed data refers to datasets that reside on
    different computers which are linked by a network such as the
    Internet. The computers may or may not be physically remote from
    each other. The main point is that the computers manage their data
    resources independently. In this guide the terms remote and
    distributed are used to imply independently managed resources.
2.  For example, suppose a user wishes to access the NODC XBT database
    using a program that uses the netCDF API. A program that can process
    the arrays that netCDF manipulates are largely unsuitable for XBT
    station data. However, a user can define constraint expressions in
    the URL to sample the data and deliver it in a form the netCDF API
    can use. For more information about constraint expressions, see
    Section 4.1. For more information about data models and translation,
    see Chapter 6.
3.  Or a program specially developed to read data from OPeNDAP servers.
4.  Note that there is a limit to what can be translated. An API meant
    to support two-dimensional arrays may be able to handle
    one-dimensional vector data, but a program designed to process
    one-dimensional vector data will not know what to do with a
    two-dimensional array. The set of data access APIs supported by
    OPeNDAP contain several such mismatches. See Section 6.1.2 for more
    information.
5.  It is also referred to as the Data Descriptor Structure and the Data
    Attribute Structure. See Chapter 6 for more details about these
    structures.
6.  The only part of the URL whose spelling is not at the discretion of
    the administrator of the host machine is the http, and the nph- at
    the beginning of the CGI script name. Even the nc, indicating
    netCDF, can be changed, although for clarity's sake, we hope people
    won't do so. Incidentally, the nph- is a relic, dating from the
    early days of the World Wide Web and the first hypertext protocol
    standards. It stands for "Non-Parsing Header" (See the CGI 1.1
    Standard for more information.), and is the only way to pass data
    through many httpd servers unparsed.
7.  The WWW Interface is only available for servers later than version
    3.1.
8.  This is not true of some APIs, such as JGOFS. That API, however,
    uses a data dictionary to allow the user to think that the data
    access is through files.
9.  It is possible to use gcc instead of g++, but in that case, -lg++
    must be added to the end of the library list.
10. The "OR" function may be implemented with a list. For example, to
    say that i must equal 3 OR 11 you would write i = {3,11}The clause
    evaluates to true when i equals any one of the elements.
11. For the sake of clarity, this and several of the following
    constraint expression examples span multiple lines. While the
    constraint expression evaluator ignores newline characters, program
    limitations of the OPeNDAP client will likely prevent a user from
    typing a newline in a constraint expression.
12. Because it contains an array, the dataset pictured in Figure 4.1.1
    is technically not a valid JGOFS dataset. We have included the array
    for pedagogical purposes, and hope that the JGOFS purists will
    forgive us.
13. The second step is, of course, optional.
14. A couple of services, such as the version and help services, are
    built into the server software, and need no configuration.
15. The geturl program knows about the OPeNDAP protocols, so you can
    also omit the .das suffix, and use the -a option to the geturl
    command. This tells geturl to append .das for you.
16. In the remainder of this document, the phrase sequence data, or just
    sequence, will mean an ordered set of elements each of which
    contains one or more sub-elements where all of the sub-elements of
    an element are somehow related to each other.
17. As in C, OPeNDAP array indices start at zero.
18. The absolute location and orientation of the entire array is
    specified by another set of scalar values; we are here considering
    the relationship between data type members.
19. The client process can limit the information received by either
    using a constraint expression or prematurely closing the I/O stream.
    In the latter case the server will exit without sending the entire
    sequence.
20. We have learned to shy away from this term since we have found that
    \`metadata' to one person is \`data' to another; the categorization
    often limits the usefulness of the underlying information.
21. To define attributes for the entire dataset, create an entry for a
    variable with the same name as the dataset.
22. Containers, aliases, and global attributes were introduced into
    OPeNDAP at version 2.16. In early OPeNDAP releases, the DAS was not
    a hierarchical structure; it was similar to a flat-file database.
    Although using the new structure is strongly recommended for new
    code, old code will still work with the old DAS. See The DODS
    Toolkit Referencefor a description of the changes made to the
    AttrTable class.
23. "Temperature" can be spelled "T", "Temp", "TEMPERATURE", "TEMP", and
    so on. Worse, "T" is also commonly used for "Time."
24. The default values enable a maximum cache size of 20 megabytes, a
    cache expiration time of 24 hours, and no proxy servers.
25. This is not to be confused with the OPeNDAP Matlab or IDL GUIs,
    which are clients of their own. This is simply a client feature that
    can display transmission and error information to the user.
26. You can also use a safe Tcl interpreter. Refer to the Tcl
    documentation for information.