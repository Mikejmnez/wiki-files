## Mapping WCS/DAP Data Models

It has been proposed that netcdf variables map to WCS fields, and netcdf
datasets map to WCS coverages (Domenico & Nativi). This works for DAP as
well, particularly as DAP has nested datasets, and WCS has nested
coverages. However, a coverage (both theoretically and in practice) has
a single coordinate reference system (CRS) (location, time, possibly
height/depth though those CRSs seem fluid in both CF and WCS). That
suggests that there be an extra coverage nesting that sorts by CRS. This
leads to a number of alternatives:

1.  map DAP GRID variables to WCS fields, use the wcs:CRS property to
    group into (and name) coverages. Drop the dataset nesting (if any).
    Where would the dataset metadata go?
2.  map DAP GRID variables to WCS fields, use the wcs:CRS property to
    group into (and name) coverages. Use as one layer of nested
    coverages -- datasets would also map to coverages. Would datasets go
    above/below CRS coverages?
3.  map DAP GRID variables to WCS coverages, datasets to higher levels
    of coverage.
4.  map DAP GRID variables to WCS fields, map the top coverage level to
    a dataset-CRS combination, subdatasets would go underneath. That way
    the nesting is preserved.
5.  use ncml to insure that all datasets we which to map to coverages
    have precisely one CRS, i.e. if a dataset has more than one CRS, use
    ncml to seperate it into multiple datasets, each with its own CRS.
    This essentially finesses the issue.

*Note that the WCS 1.1.2 specification (OGC document 07-067r5, Table 13)
says that if a coverage is georectified, then its domain description
must have exactly one gridCRS.*

Variables to fields make theoretical sense, but WCS defaults to all
fields of a coverage if range subsetting is not requested. I think
originally people mapped data arrays into coverages, not fields. Of
course, there are not (m)any WCS 1.1.2 clients out there, so whose to
say what will really happen?

In addition, it is looking to me ([ndp](User:Ndp "wikilink")) that the
data model mapping is different depending on the supported format of the
return.

- GeoTIFF - Supports a single axis, typically RGB or RGG-alpha. The axis
  can be longer, but few clients would be able to use it and the
  semantics of the axis would be lost .

<!-- -->

- NetCDF - Supports multiple axes and the semantics of the content are
  well defined.

So it seems as if it would be better to describe the datasets as many
coverages if the target format is geotiff, and as coverages with fields
if the target format is netcdf.

How to accomplish this: I think maybe you would need a WCS terminus
(server) for each representation - one returns a set of Coverages that
map to GeoTIFF and the other that map to NetCDF.

## DescribeCoverage Examples

In the CoverageDescription the Range element is mandatory and must
contain a WCS data model description of the Coverage. I think that this
may well fall under the realm of Benno's semantic analysis of the DDX.
Consider our pre-built use case data set:

`  `[`http://dev1.opendap.org:8080/opendap/coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.html`](http://dev1.opendap.org:8080/opendap/coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.html)

How do we represent it? Lets look at the sea surface velocity first,
since I have no clue how to represent the rest of the semantic
relationships in the file

With the caveat that I don't clearly understand the WCS data model, here
are a couple of attempts at representing the velocity field:

### Example 1

`      `<Range>
`          `<Field>
`              `<ows:Title>`Sea Surface Velocity`</ows:Title>
`              `<ows:Abstract>`Sea surface velocity 2D vector.`</ows:Abstract>
`              `<Identifier>`Velocity`</Identifier>
`              `<Definition>
`                  `<ows:AnyValue/>
`              `</Definition>
`              `<NullValue>`-32768`</NullValue>
`              `<owcs:InterpolationMethods>
`                  `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`              `</owcs:InterpolationMethods>
`              `<Axis identifier="Velocity">
`                  `<AvailableKeys>
`                      `<Key>`u`</Key>
`                      `<Key>`v`</Key>
`                  `</AvailableKeys>
`              `</Axis>
`          `</Field>
`      `</Range>

### Example 2

`           `<Field>
`               `<ows:Title>`Sea Surface Velocity`</ows:Title>
`               `<ows:Abstract>`Sea surface velocity 2D vector.`</ows:Abstract>
`               `<Identifier>`Velocity`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`               `<Axis identifier="surface_eastward_sea_water_velocity">
`                   `<AvailableKeys>
`                       `<Key>`u`</Key>
`                   `</AvailableKeys>
`               `</Axis>
`               `<Axis identifier="surface_northward_sea_water_velocity">
`                   `<AvailableKeys>
`                       `<Key>`v`</Key>
`                   `</AvailableKeys>
`               `</Axis>
`           `</Field>

### Example 3

Both of the previous examples rely on my human semantic analysis. From a
strictly mechanical view it might be like this:

`          `<Field>
`              `<ows:Title>`surface_eastward_sea_water_velocity`</ows:Title>
`              `<ows:Abstract>`Eastward component of a 2D sea surface velocity vector.`</ows:Abstract>
`              `<Identifier>`surface_eastward_sea_water_velocity`</Identifier>
`              `<Definition>
`                  `<ows:AnyValue/>
`              `</Definition>
`              `<NullValue>`-32768`</NullValue>
`              `<owcs:InterpolationMethods>
`                  `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`              `</owcs:InterpolationMethods>
`          `</Field>
`          `<Field>
`              `<ows:Title>`surface_northward_sea_water_velocity`</ows:Title>
`              `<ows:Abstract>`Northward component of a 2D sea surface velocity vector.`</ows:Abstract>
`              `<Identifier>`surface_northward_sea_water_velocity`</Identifier>
`              `<Definition>
`                  `<ows:AnyValue/>
`              `</Definition>
`              `<NullValue>`-32768`</NullValue>
`              `<owcs:InterpolationMethods>
`                  `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`              `</owcs:InterpolationMethods>
`          `</Field>

In the short term I could use some help (Benno?) in trying to understand
the specification.

[ndp](ndp "wikilink")

------------------------------------------------------------------------

[Category:WCS](Category:WCS "wikilink")