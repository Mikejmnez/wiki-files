(Much of this section started out from material written by Peter Baumann
of the International University Bremen which I found on this page:
<http://galeon-wcs.jot.com/WikiHome/Implementation%20Progress%20Page/> ,
specifically here:
<http://galeon-wcs.jot.com/WikiHome/Implementation%20Progress%20Page/WCS-result-spec_v5.doc?cacheTime=1139909665610>
)

The WCS GetCoverage request is composed from a set of six basic
operations:

- domain (i.e., spatio-temporal) subsetting
- range (i.e., measure) subsetting
- resampling
- coordinate transformation
- data format encoding
- result file provision

Processing a GetCoverage request can conceptually be understood as
executing the operation sequence shown in Fig. X; depending on the
particular request, some steps may be skipped. While a concrete
implementation may choose a different evaluation strategy it shall
ensure that the result is identical to the one achieved by the sequence
depicted in the following section.

### Select Source Coverage

### Convert request bounding box to native CRS of Coverage

### Perform Spatial Sub-setting

### Perform Temporal Sub-setting

### Perform Range Sub-setting

### Perform Spatial scaling into Request Bounding Box

- Includes interpolation as specified by client.

### Perform projection into target CRS

- Includes interpolation as specified by client.

### Perform Data Format Encoding

- May involve implicit range interpolation and, hence, accuracy loss,
  depending on format chosen.

### Transmit resulting byte stream to client