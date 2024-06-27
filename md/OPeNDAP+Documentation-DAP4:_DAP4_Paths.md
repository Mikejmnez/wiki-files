[\<\<back to OPULS Development](OPULS_Development "wikilink")

## Background

Consider the following example (~ CDL syntax). <font size="2">

``` c
dimensions: y=100;
group g1 {
    dimensions: x=10;
  group g2 {
    dimensions: x=5, y=6;
    float32 V[x,y];
  }
}
```

</font> The question arises as to which dimension declarations are being
referred to in the declaration of V. In HDF5 or the netcdf CDL syntax,
this would be answered by replacing the *shortname*, x, with the full
*path* name such as /g1/g2/x, and for y, /y. The path specifies,
starting at the root group, the sequence of subgroups to traverse to get
to the declaration of interest, x or y in this case.

## Problem Addressed

The same ambiguous reference problem will also occur in DAP4 because,
like HDF5/CDM/netcdf, it has a lexical group structure.

## Proposal

The specific proposal is that for any occurrence in a DAP4 DDX of a
reference to some item declared elsewhere in the DDX tree, that it be
possible to specify a *path* to disambiguate the specific object to be
referenced.

Using the above example, and not using this proposal, V would be
declared as follows. <font size="2">

``` xml
<Float32 name="V">
  <Dimension name="x"/>
  <Dimension name="y"/>
</Float32>
```

</font> Using this proposal, the dimension references would be rewritten
as follows. <font size="2">

``` xml
<Dimension name="x">
  <path group="g1"/>
</Dimension>
```

</font> and <font size="2">

``` xml
<Dimension name="y">
  <path group=""/> <!-- alternatively <path group="/"/> -->
</Dimension>
```

</font> The latter case shows how to reference an object in the top
level group using either of two equivalent notations.

## Rationale

The obvious alternative to using the <path> construct is to actually
embed the full path name as the name; like this for example.
<font size="2">

``` xml
<Dimension name="/g1/g2/x"/>
<Dimension name="/y"/>
```

</font>

I dislike this solution because we are, in effect, embedding xml
structural information in a string. This means that the string must be
parsed to extract that structural information. No other lexical element
has this requirement.

## Discussion

An open question is this: should it be possible to leave off the path
information and use some algorithm to infer the path.

It turns out that both netcdf-4 and CDM do this for dimensions. CDM in
fact even disallows the use of path names for dimensions. I am not sure
what it does for coordinate variable references.

In any case, for CDM, the inference rule is as follows.

1.  If the dimension of the same name is declared in the immediately
    enclosing group, then use that declaration.
2.  Otherwise, recurse up the sequence of enclosing groups to locate the
    first occurrence of a dimension declaration with the same name.
3.  If no such dimension is found, then declare an error.

It should be noted that this CDM rule means that certain DAP4 DDXs could
not be converted to CDM because this proposal would allow use of
dimension declarations that violated the CDM "up-the-group-tree" search
rule.

[ndp](User:Ndp "wikilink") 07:01, 28 April 2012 (PDT)


Here's an 2 alternate ideas that most likely fails to address the
concerns regarding paths raised above but that works the way I
(intuitively) thought it would.

### Alternate Idea 1

:# [Consider first the definition of Fully Qualified Name
(FQN)](DAP4:_Data_Model#Definitions "wikilink")

:# And then these [Objects with
FQNs](DAP4:_Data_Model#Objects_with_FQNs "wikilink")

:# If an object needs to reference another object it must use that
object's FQN

:# Only FQNs will be accepted as identifiers in the CE.

Consider the following example (~ CDL syntax). <font size="2">

``` c
Dataset {
    dimensions: y=100;
    group g1 {
        dimensions: x=10;
        group g2 {
            dimensions: x=5, y=6;
            float32 V[x,y];
        }
    }
}
```

</font>

Then I believe that the representation is:

<font size="2">

``` xml
<Float32 name="V">
  <Dimension name="g1.x"/>
  <Dimension name="y"/>
</Float32>
```

</font>

### Alternate Idea 2

::;Fully Qualified Name (FQN)



Every object in a DAP4 Dataset has a Fully Qualified Name. These names
follow the common conventions of lexically-scoped identifiers in that is
they begin with a slash then they represent a name scoped at the very
top level and if the don't begin with a slash then the are scoped
relative to the lexical container in which the name occurs. To write
FQNs, the component names are listed, left to right, corresponding to a
traversal of the scopes from outermost to innermost, using slashes (/)
to separate names associated with lexical scopes. Cases where slashes
are used in names are accommodated by allowing the names to be quoted
and quotes to be escaped using a backslash (\\. The (unlikely) sequence
"\\" can be represented using "\\'". That is, the backslash can itself
be escaped although that is only needed if it is a literal and
immediately precedes a literal single quote (').

:# [Objects with FQNs](DAP4:_Data_Model#Objects_with_FQNs "wikilink")

:# If an object needs to reference another object it must use that
object's FQN.

:# If the FQN does not begin with a slash then the FQN is evaluated
relative to the lexical cope of the current Group.

:# If the FQN begins with a slash then it is evaluated relative to the
top level (hidden/nameless) Group.

:# Only FQNs will be accepted as identifiers in the CE.

Consider the following example (~ CDL syntax). <font size="2">

``` c
Dataset {
    dimensions: y=100;
    group g1 {
        dimensions: x=10;
        group g2 {
            dimensions: x=5, y=6;
            float32 V[x,y];
        }
    }
}
```

</font>

Then I believe that a valid representation is:

<font size="2">

``` xml
<Float32 name="V">
  <Dimension name="g1/x"/>
  <Dimension name="y"/>
</Float32>
```

</font>

or this:

<font size="2">

``` xml
<Float32 name="V">
  <Dimension name="/g1/x"/>
  <Dimension name="/y"/>
</Float32>
```

</font>

### FQNs and constraints

[Jimg](User:Jimg "wikilink") 09:38, 12 June 2012 (PDT) One thing that we
must be able to do is reference variables unambiguously in a constraint
expression.

Suggestion: Based on current practice (DAP2), we use a slash (*/*) to
separate groups and a dot (*.*) to separate fields in Grids, Structures
and Sequences. Only the HDF5 handler has Group support - that is, it has
support for HDF5 groups accessed using DAP2 - while all of the code has
support for Structures, et cetera. I suggest we adopt a similar practice
for DAP4, modified as needed for the changes to the Grid type.