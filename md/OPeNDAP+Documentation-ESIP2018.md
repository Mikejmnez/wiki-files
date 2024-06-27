## JSON

These JSON dialects create 1:1 onto mappings to the native "binary"
representation / source encoding.

- NCO-JSON \<-\> nc4
- hdf-json\<-\> hdf5
- DAP2 JSON \<-\> DAP2

## BALTO

clowder:
<https://opensource.ncsa.illinois.edu/confluence/display/CATS/Home>

## DSR

How to generate DSR?

- Bake the response into the code (current)
- Dynamically generate the response by:
  - Have OLFS ask BES what responses are supported for a particular
    dataset: For example, the covjson handler in the BES does this as
    part of generating the covjson response. We could duplicate that
    work in the OLFS (yuck) or we could add code to covjson that allows
    us to ask the question.
  - Get DDX/DMR and examine content in OLFS: Probably the most efficient
    but requires that we duplicate BES logic in the OLFS. For example:
    How do we determine if a dataset is a candidate for a covjson
    response? We have to examine the DDS/DAS/DDX/DMR to determine of the
    metadata is sufficient and the BES code already does this
  -