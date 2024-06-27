## Background

In DAP2, the DAS allowed nested sets of attributes. The primary reason
for this (I assume) was that the DAS was separate from the DDS and
nesting in the DAS could be made to match nesting in the DDS. This
allowed for the setting of attributes on nested variables in structures,
for example. [Jimg](User:Jimg "wikilink") 14:51, 5 September 2012 (PDT)
I never clarified this: No, nested attributes were not added for this
reason. They were added because HDF4 supported them. Subsequently, we
found them useful for other things and, separately, other people did
too.

In his original DAP4 xsd schema, James again allowed for nested
attributes.

## Proposal

I propose that we do not allow nested attributes in DAP4.

## Rationale

The current rng/xsd DAP4 schema allows one to directly place attributes
almost anywhere in the DDX. It seems to me that this makes any need for
nested attributes superfluous. [Jimg](User:Jimg "wikilink") 14:55, 5
September 2012 (PDT) No. Limiting attributes to the DMR (aka DDX) is a
limitation that we can easily avoid by supporting 'nested attributes'.

*Dennis Heimbigner*

## Discussion

[Jimg](User:Jimg "wikilink") 12:36, 13 April 2012 (PDT) HDF4 and HDF5
allow nested attributes - we added them to DAP2 to support HDF4.

[Jimg](User:Jimg "wikilink") 15:00, 5 September 2012 (PDT) A further
comment: I'm not sure why 'nested attributes' are an issue - they add
considerable expressive power, are used by HSF4 and HDF5 as well as
other formats and people like them. Representing HDF4 and HDF5
accurately is not possible without them or employing a 'convention'. And
it seems the cost of implementation is fairly low.