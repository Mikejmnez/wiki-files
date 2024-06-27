### Background

John has noted that in CDM, he introduced a notion of Coordinate System
which is a set of maps defined independently of any variable. In xml,
this would be something like this.

    <CoordinateSystem name="...">
    <map variable="var1"/>
    ...
    <map variable="varn"/>
    </CoordinateSystem>

## Rationale

The argument for it is that sets of maps are often going to be used in
identical form in a number of variables. Further, it allows one to add
attributes to the coordinate system element to provide extra
information.

## Proposal

Do we add this element to the DDX?

## Discussion

There seems no immediate hurry on this. It can be added rather
transparently later on when a demonstrated need exists.