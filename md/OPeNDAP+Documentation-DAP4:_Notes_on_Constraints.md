[\<-- back to OPULS
Development](Development#OPULS_Development "wikilink")

### This page contains various thoughts about DAP4 constraints.

#### Projections

There are two kinds of "projection" implicit in DAP2 constraints.

1.  Range projections such as A\[0:2:22\] where the result keeps the
    same rank, but the dimensions sizes are reduced and the result is a
    subset of the values in the original array, A.
2.  Column projections, which are the traditional relation projections
    in which a relation (Sequence) has some of its columns (fields)
    removed. This might convert, for example, Sequence {int32 f1;
    float32 f2;} to Sequence {int32 f1;} if column/field f1 is projected
    away.

One oddity (flaw?) in the way DAP2 constraints are constructed is that
they completely separate a (column) projection specification from any
associated selection constraint. This makes interpretation of the
constraint somewhat more difficult and also has the consequence that it
is impossible to return two different projection+selection combinations
over the same sequence/relation. I think, that we should strongly
consider modifying the grammar of constraints to use something much
closer to the SQL project-where kind of construct.

#### Extended Dimensional Selection

With respect to [Nathan's proposal for
selections](DAP4:_Subsetting_Arrays_and_Grids_By_Value "wikilink"), I
might note that it does lose dimensional information that might be both
useful and performance enhancing. For instance, it might be useful to be
able to say something like

    ?sal[0:22][0:9][]<3.2

and get something back like this: <font size="2">

``` xml
<Sequence name="sal">
    <Float64 name="sal">
      <Dimension size="23" />
      <Dimension size="10" />
    </Float64>
    <Float32 name="lat"/>
    <Float32 name="lon"/>
    <Float32 name="depth"/>
</Sequence>
```

</font>