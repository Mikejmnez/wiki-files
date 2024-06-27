[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

This page is intended to provide a concrete proposal for replacing the
current variable length dimension model with a more syntactically and
semantically clear alternative. As such it replaces a [previous
proposal](DAP4:_VLens_(and_Sequences) "wikilink").

## Problem Addressed

In my opinion, the decision to treat variable length objects
(*sequences* of objects) using the '\*' dimension notation is a mistake.
The reason is that (1) it requires special handling for \* in the code,
(2) it violates the desired constraint that a dimension have a specific
size across all of its occurrences (at a specific point in time).

In order to rectify this, I proposed basically two alternatives.

1.  Define a new type constructor, temporarily called *Sequence* here,
    and analogous to the existing DAP2 Sequence construct.
2.  Define a new type constructor analogous to the HDF5/netcdf-4 *vlen*
    construct. So one might define as a type, \<Int32\*\> to indicate
    that its type is a sequence of Int32 objects.

There is a relationship between the two proposals in that the first is
effectively equivalent to \<Sequence\*\>.

## Proposed Solution

Specifically, I propose we adopt alternative 1, but possibly with a
keyword other than *Sequence*.

Its semantics is that it represents an arbitrary number of instances of
objects where the objects conform to the format of a Structure
equivalent to the Sequence syntax.

For example, consider this sequence declaration.

    <Sequence name="s">
    <Int32 name="field1"/>
    </Sequence>

It is equivalent in semantics to a sequence of structure instances of
this form.

    <Structure name="s">
    <Int32 name="field1"/>
    </Structure>

The serialized representation is the obvious one. It consists of a
count, N say, representing the number of objects in the sequence
followed by N serialized instances of the equivalent structure.

Note that I explicitly allow arrays of Sequences to exist. For example.

    <Sequence name="s">
    <Int32 name="field1"/>
    <Dim name="d2"/>
    <Dim size="17"/>
    </Sequence>

## Discussion

This proposal seems to me to be the simplest solution to the VLEN
problems I have identified. There is at least one drawback.

- In translating from, say, CDM, to DAP4, it will require the creation
  and naming of an extra Sequence construct to appear in the DMR. In
  some cases, this can be mitigated, but will generally be necessary.

From previous discussions, there seems to be a consensus that calling
this new constructor a *Sequence* is undesirable because it would be
confused with the DAP2 *Sequence* constructor. Some possible alternative
names are as follows.

- vector
- series
- ~~vlen~~
- sequence (consider keeping this and living with any confusion)

[Dennis](User:dmh "wikilink")

[Jimg](User:Jimg "wikilink") 14:11, 21 July 2013 (PDT) : We should use
*vlen* only if our definition matches that of HDF4/5 *exactly*. If our
definition is the same as HDF's, then we should definitely use that
name.

Question: What does the definition of an array with two dimensions look
like where the first dimension size is some fix number and the second
size varies? What about the case where both sizes vary?

[Dennis](User:dmh "wikilink") Jul 22, 2013 8:38:41 AM : I have removed
*vlen* as an possible alternative for the reason Jim gives above. I
have, however, added *sequence* because although confusing, is still a
good name.

To answer Jim's question about dimensions, in the first case, I assume
the initial situation is this: Int32 x\[5\]\[\*\]. The equivalent would
be

    Sequence synthname1 { Int32 x;} [5]

The DMR form would be

    <Sequence name="synthname1">
    <Int32 name="x"/>
    <Dim size="5">
    </Sequence>

For the second case, I assume the initial situation is this: Int32
x\[\*\]\[\*\]. The equivalent would be

    Sequence synthname1 { Sequence synthname2 {Int32 x;} }

The DMR form would be

    <Sequence name="synthname1">
    <Sequence name="synthname2">
    <Int32 name="x"/>
    </Sequence>
    </Sequence>

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")