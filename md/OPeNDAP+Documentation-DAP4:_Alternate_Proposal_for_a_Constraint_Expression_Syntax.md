[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

The current approach for OPULS has been to design the grammar and
semantics of constraint expressions.

It occurs to me, however, that the concurrent thing we need to do is
describe what kinds of DMRs will result from our proposed constraints.
That is, given a constraint and a DMR for the unconstrained dataset.
describe the DMR that corresponds to the result of applying the
constraint.

## Proposal

For brevity, I will use the term CDMR for the constrained DMR and just
DMR for the unconstrained DMR.

Specifically, we need to specify what declarations are present in the
CDMR and how they relate to the declarations from the DMR.

My current format is that a constraint is either a projection or filter
or a definition Roughly this.

    constraint:   projection
                | projection '|' predicate
                | name = [x:y:z]
                ;

Note that selections on arrays is currently omitted since we have not
come to agreement about it. Only projections/slicing and filters on
sequences are considered. Also, my goal is to have a completely
self-contained CDMR that contains the minimal set of declarations so
that there are no undefined references.

### Enumerations

An enumeration is included in the CDMR only if some variable or field
references it.

### Existing Shared Dimensions

Shared dimension declarations from the DMR are not included in the CDMR.
A reference in the CDMR to a shared dimension in the dmr is allowed; it
this occurs, then the declaration of the shared dimension is included in
the CDMR.

### New Shared Dimensions

New shared dimensions can be defined. They are not allowed to have the
same name as an existing shared dimension in the DMR (alternative is to
override).

### Variables

Each clause in the constraint must specify a top level variable and that
variable is declared int the CDMR.

### Atomic Variables

If the variable is atomic, then it is included in the CDMR with the same
type and the same arity, but with each dimension being either an
anonymous dimension or a shared dimension, including possibly a new
shared dimension.

### Structure Variables

If the variable is a Structure, then a subset of its fields will be
included in the variable declaration where the fields are those
specifically mentioned in a constraint projection. As with atomic
variables, each structure and field will have the same arity and type as
the underlying declaration in the DMR.

### Sequence Variables

If the variable is a Sequence, then for declaration purposes, it is
treated like a Structure (as above). Note that applying a filter to a
Sequence will not change its declaration form because the number of
records in the sequence is not specified in the DMR. Note also that
mentioning a Sequence field in the filter does not necessarily mean it
will be included in the DMR. It will only be included if it is mentioned
in the projection part of the constraint.

### Fields

The above rules are applied recursively to fields of Structures and
Sequences.

### Groups

Each declaration in the CDMR that corresponds to a declaration in the
DMR will cause its containing group (and that group's parents) to be
included in the CDMR. This ensures that the FQN for a declaration in the
CDMR is the same as in the DMR.

For new shared dimension declarations, the constraint can specify a
group by giving the full FQN for the new dimension. Otherwise, the new
shared dimension will be included in the root group. Remember that name
clashes are not allowed, so some declarations of new shared dimensions
will be illegal and will cause an error response.

[Dennis Heimbigner](User:dmh "wikilink") Initial draft 10/16/2013

## Addendum

Using the above rules, I believe a corresponding grammar is as follows.
Note that the {...} rules are not included (yet).

    %token NAME STRING INTEGER FLOAT BOOLEAN
    %left ','
    %precedence NOT
    %start constraint
    %%
    constraint:
        constraintlist
        ;
    constraintlist:
              clause
            | constraint ';' clause
            ;
    clause:
              projection
            | selection
            | dimdef
            ;
    projection:
            segmentlist
            ;
    segmentlist:
              segment
            | segmentlist '.' segment
            ;
    segment:
              name
            | name slicelist
            ;
    slicelist:
              slice
            | slicelist slice
            ;
    slice:
              '[' ']' /* total dimension */
            | '[' dimref ']' /* reference to an existing dimension */
            |  '[' index ']'
            | '[' index ':' index ']'
            | '[' index ':' index ':' index ']'
            | '[' index ':' ']'
            | '[' index ':' index ':' ']'
            ;
    index:  INTEGER ;

    selection:
            projection '|' filter
            ;
    filter:
              predicate
            | predicate ',' predicate  /* ',' == AND */
            | '!' predicate %prec NOT
            ;
    predicate:
              primary relop primary
            | primary eqop primary eqop primary
            | primary eqop primary
            ;

    relop: '<' | '>' | '<' '=' | '>' '=' ;
    eqop: '=' | '!' '=' | '~' '=' ;

    primary:
              fieldpath
            | constant
            ;
    fieldpath:
              name
            | fieldpath '.' name
            ;
    dimref: NAME ;
    dimdef:
            NAME '=' slice
            ;
    name: NAME ;
    constant: STRING | INTEGER | FLOAT | BOOLEAN ;

## Discussion

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")