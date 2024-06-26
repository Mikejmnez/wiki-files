This is based on this data set:
<http://dev1.opendap.org:8080/opendap/netcdf/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.ddx>

<?xml version="1.0" encoding="UTF-8"?>

<CoverageDescriptions ns="http://www.opengis.net/wcs/1.1" xmlns:ows="http://www.opengis.net/ows/1.1" xmlns:owcs="http://www.opengis.net/wcs/1.1/ows" xmlns:gml="http://www.opengis.net/gml/3.2" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:schemaLocation="http://www.opengis.net/wcs/1.1  http://schemas.opengis.net/wcs/1.1.0/wcsDescribeCoverage.xsd  http://www.opengis.net/ows/1.1  http://schemas.opengis.net/ows/1.1.0/owsAll.xsd  http://www.opengis.net/wcs/1.1/ows http://schemas.opengis.net/wcs/1.1.0/owsDataIdentification.xsd http://www.opengis.net/gml/3.2  http://schemas.opengis.net/gml/3.2.1/gml.xsd">
`   `<CoverageDescription>
`       `<ows:Title>`Near-Real Time Surface Ocean Velocity`</ows:Title>
`       `<ows:Abstract>`CoverageDescription generated by OPeNDAP WCS UseCase 2.0`</ows:Abstract>
`       `<Identifier>`coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc`</Identifier>
`       `<Domain>
`           `<SpatialDomain>
`               `<ows:BoundingBox crs="urn:ogc:def:crs:EPSG::4326">
`                   `<ows:LowerCorner>`-97.8839 21.736`</ows:LowerCorner>
`                   `<ows:UpperCorner>`-57.2312 46.4944`</ows:UpperCorner>
`               `</ows:BoundingBox>
`           `</SpatialDomain>
`           `<TemporalDomain>
`               `<gml:timePosition>`2008-03-27T16:00:00.000Z`</gml:timePosition>
`           `</TemporalDomain>
`       `</Domain>
`       `<Range>
`           `<Field>
`               `<ows:Title>`surface_eastward_sea_water_velocity`</ows:Title>
`               `<ows:Abstract>`Eastward component of a 2D sea surface velocity vector.`</ows:Abstract>
`               `<Identifier>`u`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`           `</Field>
`           `<Field>
`               `<ows:Title>`surface_northward_sea_water_velocity`</ows:Title>
`               `<ows:Abstract>`Northward component of a 2D sea surface velocity vector.`</ows:Abstract>
`               `<Identifier>`v`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`           `</Field>
`           `<Field>
`               `<ows:Title>`longitudinal dilution of precision`</ows:Title>
`               `<ows:Abstract>`The longitudinal dilution of precision (DOPx) represents the\\012contribution of the radars' configuration geometry to\\012uncertainty in the eastward velocity estimate (u). DOPx is a\\012direct multiplier of the standard error in obtaining the\\012standard deviation for the eastward velocity estimate from the\\012least squares best fit. DOPx and DOPy are commonly used to\\012obtain the geometric dilution of precision\\012(GDOP = sqrt(DOPx^2 + DOPy^2)), a useful metric for filtering\\012errant velocities due to poor geometry.`</ows:Abstract>
`               `<Identifier>`DOPx`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`           `</Field>
`           `<Field>
`               `<ows:Title>`latitudinal dilution of precision`</ows:Title>
`               `<ows:Abstract>`The latitudinal dilution of precision (DOPy) represents the\\012contribution of the radars' configuration geometry to\\012uncertainty in the northward velocity estimate (v). DOPy is a\\012direct multiplier of the standard error in obtaining the\\012standard deviation for the northward velocity estimate from the\\012least squares best fit. DOPx and DOPy are commonly used to\\012obtain the geometric dilution of precision\\012(GDOP = sqrt(DOPx^2 + DOPy^2)), a useful metric for filtering\\012errant velocities due to poor geometry.`</ows:Abstract>
`               `<Identifier>`DOPy`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`           `</Field>
`           `<Field>
`               `<ows:Title>`Contributing radar site latitudes`</ows:Title>
`               `<ows:Abstract>`Contributing radar site latitudes`</ows:Abstract>
`               `<Identifier>`site_lat`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`           `</Field>
`           `<Field>
`               `<ows:Title>`Contributing radar site longitudes`</ows:Title>
`               `<ows:Abstract>`Contributing radar site longitudes.`</ows:Abstract>
`               `<Identifier>`site_lon`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`           `</Field>
`           `<Field>
`               `<ows:Title>`Contributing radar site code.`</ows:Title>
`               `<ows:Abstract>`Contributing radar site code.`</ows:Abstract>
`               `<Identifier>`site_code`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`           `</Field>
`           `<Field>
`               `<ows:Title>`Contributing radar site network affiliation code.`</ows:Title>
`               `<ows:Abstract>`Contributing radar site network affiliation code.`</ows:Abstract>
`               `<Identifier>`site_netCode`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`           `</Field>
`           `<Field>
`               `<ows:Title>`RTV processing parameters`</ows:Title>
`               `<ows:Abstract>`Contributing radar site network affiliation code.`</ows:Abstract>
`               `<Identifier>`procParams`</Identifier>
`               `<Definition>
`                   `<ows:AnyValue/>
`               `</Definition>
`               `<NullValue>`-32768`</NullValue>
`               `<owcs:InterpolationMethods>
`                   `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`               `</owcs:InterpolationMethods>
`           `</Field>
`       `</Range>
`       `<SupportedCRS>[`urn:ogc:def:crs:EPSG`](urn:ogc:def:crs:EPSG)`::4326`</SupportedCRS>
`       `<SupportedFormat>`netcdf-cf1.0`</SupportedFormat>
`       `<SupportedFormat>`dap2.0`</SupportedFormat>
`   `</CoverageDescription>
</CoverageDescriptions>

[Category:WCS](Category:WCS "wikilink")