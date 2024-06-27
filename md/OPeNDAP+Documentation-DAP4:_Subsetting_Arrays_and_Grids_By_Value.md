[\<-- back to OPULS
Development](Development#OPULS_Development "wikilink")

[ndp](User:Ndp "wikilink")

## Background

DAP2 did not support sub-setting of Arrays and Grids using relational
operators. Many of our support questions over the years have been from
frustrated users who were attempting to perform relational sub-setting
on these objects and couldn't understand why it wasn't working.

## Problem addressed

Allow users to subset Arrays (and "Grids") using relational operators.

## Proposed solution

Allow users to apply relational constraints to the values of Arrays (and
"Grids"). The server should return a Sequence which holds the matching
Array values, along with the values of all of the associated Maps.

For example, let's consider this data object:

<font size="2">

``` xml
<Dimension name="x" size="1024"/>
<Dimension name="y" size="1024"/>
<Dimension name="z" size="1000"/>

<!-- The dimensions of a Coordinate MUST be SharedDimensions -->
<Float32 name="lon">
    <dimension ref="x"/>
    <dimension ref="y"/>
</Map>

<Float32 name="lat">
    <dimension ref="x"/>
    <dimension ref="y"/>
</Map>

<Float32 name="depth">
    <Attribute name="unit" type="String"><value>meters</value></Attribute>
    <dimension ref="x"/>
    <dimension ref="y"/>
    <dimension ref="z"/>
</Map>

<Float64 name="sal">
    <dimension ref="x"/>
    <dimension ref="y"/>
    <dimension ref="z"/>
    <map name="lat" />
    <map name="lon" />
    <map name="depth" />
</Float64>
```

</font>

Applying the constraint "<font size=2>`?sal<3.2`</font>" would return
this:

<font size="2">

``` xml
<Sequence name="sal">
    <Float64 name="sal" />
    <Float32 name="lat"/>
    <Float32 name="lon"/>
    <Float32 name="depth"/>
</Sequence>
```

</font>

And the evaluation might look something like this (pseudo-code) for a
returned sequence:

<font size="2">

``` java
for(int x=0; x<1024 ; x++){
    for(int y=0; y<1024 ; y++){
        for(int z=0; z<1000 ; z++){
            if(sal[x][y][z] < 3.2){
                match_sal   = sal[x][y][z];
                match_lat   = lat[x][y];
                match_lon   = lon[x][y];
                match_depth = depth[x][y][z];
                send_sequence_row(match_sal, match_lat, match_lon, match_depth);
            }
        }
    }
}
```

</font>

Or the evaluation might look something like this (pseudo-code) for a
returned mask:

<font size="2">

``` java

int xsize = 1024;
int ysize = 1024;
int zsize = 1000;

Mask sal_mask = new Mask(x,y,z);
sal_mask.setAll(false);

for(int x=0; x<1024 ; x++){
    for(int y=0; y<1024 ; y++){
        for(int z=0; z<1000 ; z++){
            if(sal[x][y][z] < 3.2){
                match_sal   = sal[x][y][z];
                sal_mask.set(x,y,z,true);
            }
        }
    }
}

sal_mask.serialize();
```

</font>

## Rationale for the solution

Subsetting arrays and "grids" using relational expressions is unlikely
to yield a set of matching items that can still be viewed as the same
object in the data model. By representing the result as a Sequence we
are able to return a reasonable representation of the result of the
applied constraint using another representation found in the data model.

## Discussion

[Dennis](User:dmh "wikilink")(4/28/2012) I like this proposal. It avoids
the problem of using variable length dimensions and provides an
additional use for sequences.

[Dennis](User:dmh "wikilink")(4/29/2012) I might suggest that the
produced sequence should contain columns for the dimensions of the
selected variable. Using the above example, I would suggest the new
sequence should look like this. <font size="2">

``` xml
<Sequence name="sal">
    <Float64 name="sal" />
    <Int32 name="x" />
    <Int32 name="y" />
    <Int32 name="z" />
    <Float32 name="lat"/>
    <Float32 name="lon"/>
    <Float32 name="depth"/>
</Sequence>
```

</font>

[Dennis](User:dmh "wikilink")(4/28/2012) ~~I am not sure, however that I
see the algorithm for choosing the values for the map columns. Nathan,
can you elaborate on the rule for doing that?~~ Never mind; this issues
requires some detailed discussion.

[Dennis](User:dmh "wikilink")(4/28/2012) slightly off topic. I thought
we had agreed that map variables are just declared using ordinary
variable declarations. The example above is using the element keyword to
define coordinate variables. E.g.

    <Map name="lon" type="Float32">
        <dimension ref="x"/>
        <dimension ref="y"/>
    </Map>

should instead be:

    <Float32 name="lon">
        <dimension ref="x"/>
        <dimension ref="y"/>
    </Float32>

[ndp](User:Ndp "wikilink") That's probably the case - I was just doing a
copy-pasta from somewhere else on the wiki for the example. And I'm sure
that somewhere hasn't been brought into line with our current
discussion. I suspect that once we iron out an agreement on the various
bits of the data model we'll need to go back and rework the wiki content
to reflect our thinking.