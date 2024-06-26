## Kinds of files the handler will serve

This handler will serve data stored in files that can be read using the
GDAL GIS library, including GeoTIFF, JPEG2000 and GRIB2.

##### Dataset representation

These are all GIS datasets, so DAP2 responses will contains Grid
variables with latitude and longitude maps. For DAP4, the responses will
be coverages with latitude and longitude domain variables.

### Known problems

Often the data returned when using nothing but a GeoTIFF, JPEG2000 or
GRIB2 file contains none of the metadata that make them useful for
people not extremely familiar with the particular dataset. Thus, in most
cases some extra work will have to be done either using NcML or an
ancillary DAS file to add metadata to the dataset.

## Configuration parameters

None.