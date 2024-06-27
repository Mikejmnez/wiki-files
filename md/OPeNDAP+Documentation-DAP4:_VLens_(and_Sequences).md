> "Oh what a tangled web we weave when first we practice to build a type
> system."

## BackGround

Recently, I made the claim to James that the Sequence construct could
serve to represent CDM/HDF5 vlen constructs.

His reply was as follows:

> In my opinion we will want vlens to be in the data model, if not as a
> type, then as a feature of arrays - see the schema and my text on the
> Data Model page. The reason for that \[is that\] I have already tried
> using Sequence for vlen (in hdf4) and it was a failure. Not a failure
> from the technical POV but because neither server writers nor client
> writers nor client users ever 'got it.' I think one part of the
> problem for those people was that vlens in HDF4 do not support
> relational operators via the API while Sequences are supposed to. So
> the conceptual mismatch doomed the idea.

This is a very important observation, and has caused me to re-think how
sequences and vlens should be handled in DAP4.

### Vlens

Let us start by addressing the addition of vlens to the DAP4 data model.

Some possible ways to insert vlens into the dap4 data model include the
following.

1.  In CDM, a vlen is marked by a "\*" as a dimension name in the set of
    dimensions associated with a variable; the list of dimensions is
    allowed to have any number of occurrences of "\*". \[Aside, I will
    note that "unlimited" is also an option for CDM, but should not be
    needed for DAP4 because at the time of a request for data, the size
    of the unlimited dimension is known\]
2.  James has proposed something similar except that the "\*" is
    restricted to occurring as the last dimension.
3.  Another possibility is to create a new container object, call it
    *Vlen*, that (like sequence) is inherently of variable length and is
    not dimensionable. \[Aside: The term "vlen" is kind of odd; the term
    "list" would actually make more sense\]

If we were to choose option 2, then we must address the question of
translation between CDM and DAP4. Going from DAP4 to CDM is
straightforward because having the last dimension be "\*" is legal in
CDM. Going from CDM to DAP4 requires the introduction of a number of
Structure elements.

Consider the following CDM example (using a pseudo-syntax)

`Int32 v[d1][*][d2][*][d3];`

This would have to be represented in DAP4 something like this.

` Structure v_1 {`
`   Structure v_2 {`
`     Int32 v[d3];`
`   }[d2][*];`
` }[d1][*];`

In the event that the last dimension is a "\*":

` Int32 v[d1][*][d2][*];`

This would have to be represented in DAP4 something like this, which has
one less extra structure.

` Structure v_1 {`
`     Int32 v[d2][*];`
` }[d1][*];`

### Alternate proposal

\[Added 4/7/2012\] <ins>After our most recent telecon, it seems that
James is of the strong opinion that we need to keep Sequences, including
nested Sequences. In light of this, I propose that we therefore drop the
vlen construct and just stick with sequences. This would be essentially
equivalent to option 2 above, but using the keyword "sequence" instead
of "vlen".

Note that this partly contradicts the inference from James' experience
above but differs in that we are saying that selection CANNOT be applied
to some sequences, which is different, I thin, than saying that
selection CAN be applied to some vlens.</ins>

## Discussion

- As a personal matter, I would prefer to use the CDM representation.
  Adding a new container type (Vlen),while appealing semantically, only
  complicates the model more. James' approach requires the use of
  additional Structure definitions which, in my opinion, obscures the
  underlying semantics.
- One thing that I need to check is how this affects the proposed on the
  wire format in [DAP4: DAP4 On the Wire
  Format](DAP4:_DAP4_On_the_Wire_Format "wikilink").

### Sequences

Nathan Potter noted the following.

> At one time we considered using *nested* sequences as a representation
> of an SQL database, where essentially the keys that link the tables in
> the DB define the structure of some nested sequence thing. I think
> it's a useful idea, but there is no implementation of it to play with.
> \[Aside: I could swear that someone told me that they actually used a
> relational database as the back end to a sequence\]
>
> \[ [ndp](User:Ndp "wikilink") - *I mentioned (in the same email that
> contained the nested sequence comment quoted above) that we wrote and
> released a server that represents a single database table or view as a
> single sequence. This is quite different from the point that I was
> making in the section quoted above that there may be a use case for
> nested sequences. (which was in response to your question "Are there
> any other legitimate examples for using NESTED sequences.") -
> [ndp](User:Ndp "wikilink") 12:08, 26 February 2012 (PST)*\]

It is this ability to have selection constraints applied that separates
sequences from vlens. It is clear that in the absence of selection
constraints, there is no essential difference between sequences and
vlens. Further, in the few examples I could find or were sent to me, it
seemed that nested sequences were being used as, in effect, vlens.

So, we seem to have two very similar concepts (vlen and sequence), which
complicates the DAP4 model. The question for me is:


Do we get rid of the Sequence concept, or at least define it as
equivalent to the following?


Structure {...} \[\*\]

My current belief is that we should keep sequences but with the
following restrictions:

1.  Sequences can only occur as top-level, scalar, variables within a
    group.
2.  Sequences may not be nested in any other container (i.e. other
    sequences or structure)

This keeps sequences for the original purpose of acting as "relations".
All other places where we might use a sequence before will now use a
vlen.

As with vlens, the translation between DAP4 and CDM needs to be
addressed.

1.  The conversion from DAP4 to CDM can be addressed using the rule
    above, namely that a sequence is, in CDM, represented as follows.

    Structure {...} \[\*\]
2.  Translation from CDM to DAP4 allows for the option of never using
    sequences, but always using vlens. An alternate translation might be
    to say that if you have a top-level CDM structure whose only
    dimension is a vlen, then translate that to a sequence (in effect
    inverting the DAP4-\>CDM translation).

*-Dennis Heimbigner*

------------------------------------------------------------------------

[JohnCaron](User:JohnCaron "wikilink") 10:32, 10 April 2012 (PDT)

### Sequences vs vlens vs Dimensions

The problem with HDF vlens is that they applied it to the datatype, ie
"vlen of integer". I think this is what Dennis means by vlen as a
container. While this is not wrong, it always struck me as clumsy. A
variable length Dimension seems quite elegant. So CDM does not have
vlens as a container.

A Sequence in CDM land is a Structure(\*), that is variable length
"array" of Structures. I think thats quite elegant also. The CDM doesnt
support relational constraints, but Id like to add that. However, Id
like to add that in a way that is optional, since sometimes an efficient
implementation is not always possible.

BUFR has variable length arrays and Structures (top level and nested)
all over the place. Theres no simple way to represent BUFR data without
them.

So I would propose:

1.  keeping the Sequence type to mean "Structure(\*) against which
    relational constraints can be applied". A Structure(\*) does not
    allow relational constraints.
2.  I would allow nested Sequences.
3.  use variable length Dimensions, not vlens.

------------------------------------------------------------------------

[Dennis](User:dmh "wikilink") (4/23/2012)

This is yet another proposal for sequences and vlens.

<ins>**Proposal**</ins>

**Sequences** are representations for relations in the relational
database sense. To enforce this, they are subject to the following
restrictions.

1.  Sequences may not be nested and may only be declared at the top
    level of a group.
2.  All fields of a Sequence must be of a primitive type or may be a
    Structure. In any case, the field may not have dimensions.
3.  Any primitive typed field of a sequence may be used an a selection
    constraint. Specifically, this includes fields that are enclosed in
    a structure.

If \#2 is too restrictive, we can consider the following alternative.

1.  Fields may be of any type (except Sequence) and may have any number
    of dimensions, including vlen dimensions (see below). However, rule
    \#3 still applies, so any field that is dimensioned (directly or via
    some enclosing dimensioned structure) may not be used in a selection
    constraint.

**Vlens** are representations of vectors of variable length and are
completely unrelated to Sequences. They are indicated by the occurrence
of "\*" as the name of a dimension. They have the following
restriction(s).

1.  Given a variable with dimensions \[d1\]\[d2\]...\[dn\], only
    dimension dn may be a vlen dimension.

<ins>**Rationale**</ins>

The rationale for sequences is that if they are fronts for relations,
then we should treat them as such and enforce restrictions to make then
usable as such. This means that the fields of the Sequence must be
columns in the relation. They must be primitive, undimensioned types so
that e.g. sql can properly interpret them. Note that including
undimensioned structures is ok because the fields in the structure can
be flattened to become just additional columns in the relation.

If the alternative choice mentioned above is used, then columns that are
not primitive undimensioned types will be stored as blobs in the
relational database and will (as a rule) not be accessible to selection
constraints.

The question may arise: is a sequence equivalent to some structure with
a vlen dimension (e.g. Structure {...}\[\*\]). The answer is no. Vlens
and sequence are entirely disjoint concepts. However, when converting to
DAP4 to CDM it may be acceptable to **translate** a Sequence to
Structure{...}\[\*\], but this is purely a translation decision.

Additionally, some server may want to translate non-relational data to a
Sequence because the server has the ability to do selection constraints
on that non-relational data. The translation from that data to DAP4 is
up to the server, but the resulting translation must still adhere to the
rules for sequences listed above. This has the consequence that writers
of translations may have to learn about relational normal forms in order
to convert their data to the proper form of a set of Sequences, but so
be it.

### Resolution

[Jimg](User:Jimg "wikilink") 13:09, 27 April 2012 (PDT)

I propose that we accept Dennis' Yet Another Proposal for Sequences and
vlens (YAPS) with the option that Item \#2 *is* too restrictive and that
we should adopt his alternative (*Fields \[of a Sequence\] may be of any
type (except Sequence) and may have any number of dimensions, including
vlen dimensions (see below). However, rule \#3 still applies, so any
field that is dimensioned (directly or via some enclosing dimensioned
structure) may not be used in a selection constraint.*)

I also propose that we read the four letters v l e n as *variable length
dimension* and that we write that phrase when appropriate from now on.
;-)

To restate the proposal so that minimal reading is needed, the proposal
can be broken down into two parts:

#### Sequences

##### Data model

Sequences are representations for relations in the relational database
sense. To enforce this, they are subject to the following restrictions.

1.  Sequences may not be nested and may only be declared at the top
    level of a group. \[Clarification of the 'nesting' limitation: The
    Sequence type serves as a container for all other DAP4 types
    *except* Sequence itself. This exclusion applies to not only to a
    Sequence's child elements, but all of its 'descendent' elements,
    too. For example, a Structure held within a Sequence cannot contain
    a Sequence. To reiterate: Instances of the Sequence type can only
    appear at the top level of a Group.\]
2.  Fields may be of any type (except Sequence) and may have any number
    of dimensions, including variable length dimensions (see below).
    However, any field that is dimensioned (directly or via some
    enclosing dimensioned structure) may not be used in a selection
    constraint.
3.  Any primitive typed \[*Atomic*\] field of a sequence may be used
    \[in\] a selection constraint. Specifically, this includes fields
    that are enclosed \[within one or more structures\]. \[Clarification
    of the type-based limitations of the application on relational
    operators: For atomic types contained within a Sequence, DAP4
    servers will implement relational operators. For Arrays and Grids,
    relational operators do not apply, while *projection* operators do
    (using the same logic as is used for BLOBs in a RBD). For
    Structures, relational types do not apply to the Structure but can
    be applied to its children, subject to these rules.\]

##### Representation

For APIs such as the Common Data Model (CDM) that do provide a
relational interface, it will be acceptable to represent a Sequence as a
Structure with a variable length dimension. This is a decision that
client software will make, not the server.

#### Variable length dimensions

For any Array, one or more dimensions may be a *variable length
dimension* subject to the following restriction: For any dimension to be
*variable length* all of the other dimensions that vary faster must also
be variable length dimensions or the dimension must be the one that
varies fastest. For a variable with dimensions
\[d1\]\[d2\]...\[dn-1\]\[dn\], the dimension *dn* may be variable in
length. The dimension *dn-1* may be variable length if and only if *dn*
is variable length, for any value of *n*.

\[Original text: *Vlens are representations of vectors of variable
length and are completely unrelated to Sequences. They are indicated by
the occurrence of "\*" as the name of a dimension. They have the
following restriction(s). Given a variable with dimensions
\[d1\]\[d2\]...\[dn\], only dimension dn may be a vlen dimension.* I've
made a change allowing more than one variable length dimension because I
think we need to support things like Float32 \[10\]\[\*\]\[\*\] for some
GRIB data. One idea we talked about was using nested structures to allow
two or more variable length dimensions. However, our (OPeNDAP's)
previous experience with HDF4 where structures with single elements were
added was that it created confusion for users.\]