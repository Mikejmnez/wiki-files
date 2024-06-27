## Use Cases for the NetCDF Client Library's Translation Feature(s)

### Actors

Developers, End users of analysis programs.

### Use cases

Break the use cases into three groups, one for each of the data types.
There are some features that are common to all of these and those will
be broken out as 'common use cases' even though maybe they should be
considered common 'features' instead.

Looking over these some more, it's more like they are features and not
use cases... -- Main.JamesGallagher - 03 Nov 2004

1.  Structure
    1.  Structure names should collapse so that a struct1 with var1 and
        var2 becomes struct1.var1 and struct1.var2. Using these names
        will make CEs work as expected, too. %GREEN%Done.%ENDCOLOR%
    2.  Make sure to handle arrays of Structures. Make sure to name the
        dimensions (when un-named) <struct name>-unnamed-<i> for i = 0
        ... N-1 for N dimensions. This will indicate to clients that the
        different variables' share common dimensions. %ORANGE%Low
        Priority; Arrays of Structures occur rarely.%ENDCOLOR%
    3.  Attributes: Copy the attributes of var1 to struct1.var1.
        %GREEN%Done. The S2000415.HDF file can be used to test this. It
        appears to work correctly.%ENDCOLOR%
    4.  Attributes: Given a Structure struct1 and to variable var1 and
        var2, the attributes of the translated variable struct1.var1 are
        those of struct1 merged with var1. Similarly, struct1.var2 has
        the attributes of var2 and struct1 combined. %ORANGE%Low
        Priority; Most Structures don't have their own
        attributes.%ENDCOLOR%
2.  Sequence
    1.  The CL is used to open a data source with one, single-level
        Sequence: The Sequence should appear as a collection of
        arrays.%GREEN% Done. %ENDCOLOR%
    2.  The CL is used to open a data source with a nested Sequence: The
        Sequence should appear as a collection of arrays, where the
        Sequence hierarchy is 'flattened' so that values in the outer
        level are repeated. %RED%Skip This until everything else is
        done. It's hard and the other requirements, when put together,
        add up to something that many peope will find very
        useful.%ENDCOLOR%
    3.  The CL is used to open a data source with a constraint
        expression: The CE will determine how many elements of the
        Sequence are returned.
    4.  The CL is used to open a data source with a Sequence but no CE
        is given: The first 100 elements of the Sequence will be
        accessed. %GREEN% Done. Sort of. If limit is not specified, only
        one value is accessed. If it is given, limit values are
        accessed. %ENDCOLOR%
    5.  An end-user opens a data source using no CE, looks at the first
        hundred values of a Sequence and then closes and reopens the
        data source, this time using a CE.
    6.  Attributes. %ORANGE%Low priority: most Sequences don't have
        thier own attributes.%ENDCOLOR%
3.  String
    1.  The CL should represent DAP Strings as char arrays with the
        single dimension named <var-name>-chars.
    2.  The size of the array should be max-length (255) characters
        unless limit-<var-name> is used to specify a limit.
    3.  If the size of the string is larger than limit, then replace the
        last three chars with '...'. If it's shorter than limit, pad
        with nulls.
    4.  Attributes
4.  Global attributes
    1.  translation-scheme: String attribute. Added to the NC_GLOBAL
        attribute container. %GREEN%Done for Sequences and Structures.
        10/25%ENDCOLOR%
    2.  The CL opens a data source which has a NC_GLOBAL attribute
        container. Use the attribtues in that as the global attributes.
        %GREEN%Done. Test: <nop>NCConnectTest (using fnoc1.nc).
        %ENDCOLOR%
    3.  The CL opens a data source which does not have a NC_GLOBAL
        container. Search for another likely candidate and use that.
        %GREEN%Done. Test: <nop>NCConnectTest (using 3B42.980909.5.HDF).
        %ENDCOLOR%
    4.  The CL opens a data source which has nested attribtues in the
        global container. Flatten it and use the resulting attributes.
        %GREEN%Done. Test: <nop>NCConnectTest(using 3B42.980909.5.HDF).
        When attributes are flattened, a colon (:) is used to separate
        the original names in the new attribute's name.%ENDCOLOR%

### Common use cases:

1.  Behavior of client-side URL parameters
    1.  limit: Implement this first. The limit parameter sets the
        default size for variably-sized values. For a Sequence this
        means setting the number of rows of that Sequence which will be
        read. This size is then reported via the netCDF API as the
        number of dimensions in the arrays which correspond to the
        fields of the Sequence. When preload is true, this is not used.
        %GREEN% Done. 11/3. %ENDCOLOR%
    2.  preload: implement this second but not until translation for
        Sequences is complete. What does this mean for Strings? When
        true, load values when the data source is opened. All the values
        are loaded and the sized of arrays which correspond to Sequence
        fields are the true sizes; that is, the arrays hold all of the
        data in the Sequence.
    3.  limit-<variable>: Provides a limit for a specific variable. This
        will work just like limit above, but sets the limit for only the
        named variable. Other variables get either the value of limit or
        the default limit. %GREEN% Done. 11/3. %ENDCOLOR%
    4.  scheme: do this later; we're only going to implement the
        'flatten' scheme.
2.  New attributes added to a variable
    1.  translation: String; added to every variable that's the result
        of translation. The values should be 'translated' or
        'synthesized'. The later is for more complex translation schemes
        than flatten, so it'll not occur until/if those are implemented.
        %GREEN%Done for Structures and Sequences. 10/26%ENDCOLOR%
    2.  limit-exceeded: true if the actual size of the variable is
        bigger than the limit. I'm not sure if this will be easy to
        implement. Plus there's no boolean attribute type. Make
        implementing this a lower priority than other attribute-related
        features. %ORANGE%Low priority; I'm not sure how to implement
        this given that we're using the range fetch feature of
        Sequences. What if a CE requests 100 elements and the server
        returns 100; there might be more and there might
        not...%ENDCOLOR%

---

Note: I have two other test data sources that can be used to test the
attribute processing. There are all HDF4 data sources since support for
HDF4 seems to be the most important. -- Main.JamesGallagher - 25 Oct
2004

[category:NC-DAP](category:NC-DAP "wikilink")