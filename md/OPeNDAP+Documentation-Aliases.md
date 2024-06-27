As this discussion moves forward I see two issues here: Alias syntax and
Alias scope. Where Alias syntax is how we write them down, and Alias
scope is what they can (or cannot) point to.
**Before we can nail down the syntax we need to decide the scope.**

------------------------------------------------------------------------

I think being able to alias variables and attributes so values can have
multiple names is important. When we (Nathan and I) combined the
attributes with the variables in the DDX object we found that it was
hard to use the simple dot notation to specify names because an
Attribute and Variable might have the same name.

I propose the following rules for Alias source naming:

1.  An alias source (and from now on I'll just say 'source') uses dots
    (.) to separate components of a variable when those have more than
    one part.
2.  An alias source uses colons (:) to separate components of a
    attribute when those have more than one part.
3.  A source must be absolute. The source may either beign with the name
    of the dataset or, as a shorthand for that, it may begin with a dot.
    (Once we have these rules sorted out we can look at supporting
    relative sources.)
4.  Aliases for variables and attributes cannot be mixed. That is an
    alias that creates a new name in the 'variable space' cannot have an
    attribute as its source.

Here are some example DDXs (written using the old notation) and some
examples of Aliases:

    <Dataset name="testdata">
        <Attribute name="stuff" type="String"> <!-- #1 -->
            <value>Stuff about this stuff</value>
            <value>Silly</value>
        </Attribute>
        <Attribute name="cruise" type="Alias">
            <value>testdata:stuff</value>      <!-- cruise has the type and value of #1 -->
        </Attribute>
    </Dataset>

    <Dataset name="testdata">
        <Attribute name="stuff" type="Container"> <!-- #2 -->
            <Attribute name="inner_stuff" type="String"> <!-- #3 -->
                <value>Inner stuff</value>
            </Attribute>
        </Attribute>

        <Attribute name="table" type="Alias">
            <value>testdata:stuff</value>              <!-- table's value is #2, the whole container -->
        </Attribute>

        <Attribute name="cruising" type="Alias">
             <value>testdata:stuff:inner_stuff</value>  <!-- cruising has the type and value of #3 -->
        </Attribute>
    </Dataset>

The DODS Test server is a great source of DDX objects which make
excellent examples.
Look at the first link on Potter's the DTS:
<http://test.opendap.org:8080/dods/dts/>

Lets try to keep examples to one point to make them easier to use.

I'll add more examples later, but this is a complex topic and I'd like
to add other topics before 'finishing' this one.

Nathan poses some questions: How does this "source" syntax refer to a
variable's attribute table? How does it refer to a variable? ndp

James Gallagher - 17 Sep 2003

----

I don't really think we need this syntax (both the .'s and the :'s) I
think we should have Variable aliases and Attribute aliases. The former
can point to variables, the latter can point to Attributes. Basically
the idea is that variables don't become Attributes (meta-data) and vice
versa. This maintains the long standing separation of data and meta-data
in our architecture. A potential syntax might be:

    <Dataset name="testdata">
        <Attribute name="stuff" type="String"> <!-- #1 -->
            <value>Stuff about this stuff</value>
            <value>Silly</value>
        </Attribute>
        <Attribute name="cruise" type="alias" source=>  <!-- This is an "Attribute Alias" which may only reference another Attribute (meta-data) -->
            <value>testdata:stuff</value>       <!-- cruise has the type and value of #1 -->
        </Attribute>

        <Float32 name="avar"/> <!-- #2 -->
        <Float32 name="bvar"/>
        <Alias   name="cvar" source="testdata.avar"/> <!-- This is a Variable Alias" which may only reference another Variable (data) -->
                                                      <!-- cruise has the type and value of #2 -->
    </Dataset>

Also, in the spec document I noticed that the Alias element had empty
content. Maybe we should think about allowing Aliases (at least variable
aliases) to contain Attributes... Nathan Potter 9/19/03, 9/22/03

------------------------------------------------------------------------

On further reflection I think that we need to make some very important
decisions about [OPeNDAP MetaData
Issues](OPeNDAP_MetaData_Issues "wikilink") and the DAP before we can
really get anywhere with this Alias discussion. Nathan Potter - 22 Sep
2003

------------------------------------------------------------------------

The reason I've been waiting for aliases is to allow sharing of objects,
in particular Dimensions, so that OpenDAP can correctly represent netcdf
semantics. I'm starting to think maybe aliases are just a workaround,
and with a new DAP spec, perhaps this is the time to explicitly add a
Dimension object to the DAP data model.

BTW, we are looking at a new NetCDF data model that allows both global,
named Dimensions (like we have now) and anonymous Dimensions scoped by
their containing Variable, as OpenDAP has now. John Caron 03 Oct 2003