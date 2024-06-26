<?xml version="1.0" encoding="UTF-8"?>

`   `<Dataset name="200803061600_HFRadar_USEGC_6km_rtv_SIO.ncml"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    ns="http://xml.opendap.org/ns/DAP2"
    xsi:schemaLocation="http://xml.opendap.org/ns/DAP2  http://xml.opendap.org/dap/dap2.xsd">
`   `
`       `<Attribute name="NC_GLOBAL" type="Container">
`           `<Attribute name="netcdf_library_version" type="String">
`               `<value>`netcdf library version 3.6.1 of Feb  3 2008 23:15:25 $`</value>
`           `</Attribute>
`           `<Attribute name="format_version" type="String">
`               `<value>`HFRNet_1.0.0b2`</value>
`           `</Attribute>
`           `<Attribute name="product_version" type="String">
`               `<value>`HFRNet_1.1.01`</value>
`           `</Attribute>
`           `<Attribute name="Conventions" type="String">
`               `<value>`CF-1.1`</value>
`           `</Attribute>
`           `<Attribute name="title" type="String">
`               `<value>`Near-Real Time Surface Ocean Velocity`</value>
`           `</Attribute>
`           `<Attribute name="institution" type="String">
`               `<value>`Scripps Institution of Oceanography`</value>
`           `</Attribute>
`           `<Attribute name="source" type="String">
`               `<value>`Surface Ocean HF-Radar`</value>
`           `</Attribute>
`           `<Attribute name="history" type="String">
`               `<value>`12-Mar-2008 22:26:19:  NetCDF file created`</value>
`           `</Attribute>
`           `<Attribute name="references" type="String">
`               `<value>`Terrill, E. et al., 2006. Data Management and Real-time`

Distribution in the HF-Radar National Network. Proceedings of the
MTS/IEEE Oceans 2006 Conference, Boston MA, September 2006.</value>

`           `</Attribute>
`           `<Attribute name="creator_name" type="String">
`               `<value>`Mark Otero`</value>
`           `</Attribute>
`           `<Attribute name="creator_email" type="String">
`               `<value>`motero@mpl.ucsd.edu`</value>
`           `</Attribute>
`           `<Attribute name="creator_url" type="String">
`               `<value>[`http://cordc.ucsd.edu/projects/mapping/`](http://cordc.ucsd.edu/projects/mapping/)</value>
`           `</Attribute>
`           `<Attribute name="summary" type="String">
`               `<value>`Surface ocean velocities estimated from HF-Radar are`

representitive of the upper 0.3 - 2.5 meters of the ocean. The main
objective of near-real time processing is to produce the best product
from available data at the time of processing. Radial velocity
measurements are obtained from individual radar sites through the
HF-Radar Network and processed to create near-real time velocities
(RTVs)</value>

`           `</Attribute>
`           `<Attribute name="geospatial_lat_min" type="Float32">
`               `<value>`21.73596001`</value>
`           `</Attribute>
`           `<Attribute name="geospatial_lat_max" type="Float32">
`               `<value>`46.49441910`</value>
`           `</Attribute>
`           `<Attribute name="geospatial_lon_min" type="Float32">
`               `<value>`-97.88385010`</value>
`           `</Attribute>
`           `<Attribute name="geospatial_lon_max" type="Float32">
`               `<value>`-57.23120880`</value>
`           `</Attribute>
`           `<Attribute name="grid_resolution" type="String">
`               `<value>`6km`</value>
`           `</Attribute>
`           `<Attribute name="grid_projection" type="String">
`               `<value>`equidistant cylindrical`</value>
`           `</Attribute>
`           `<Attribute name="regional_description" type="String">
`               `<value>`Unites States, East and Gulf Coast`</value>
`           `</Attribute>
`       `</Attribute>
`       `<Attribute name="DODS_EXTRA" type="Container">
`           `<Attribute name="Unlimited_Dimension" type="String">
`               `<value>`time`</value>
`           `</Attribute>
`       `</Attribute>
`       `<Attribute name="Description" type="String">
`           `<value>`Holy Cow Batman! I think it worked!`</value>
`       `</Attribute>
`       `<b>
`       `<Attribute name="WcsMetadata" type="OtherXML">
`          `<Domain ns="http://www.opengis.net/wcs/1.1" xmlns:gml="http://www.opengis.net/gml" xmlns:ows="http://www.opengis.net/ows/1.1">
`              `<SpatialDomain>
`                  `<ows:BoundingBox crs="urn:ogc:def:crs:EPSG::4326">
`                      `<ows:LowerCorner>`-97.8839 21.736`</ows:LowerCorner>
`                      `<ows:UpperCorner>`-57.2312 46.4944`</ows:UpperCorner>
`                  `</ows:BoundingBox>
`              `</SpatialDomain>
`              `<TemporalDomain>
`                  `<gml:timePosition>`2008-03-27T16:00:00.000Z`</gml:timePosition>
`              `</TemporalDomain>
`          `</Domain>
`          `<SupportedCRS ns="http://www.opengis.net/wcs/1.1">[`urn:ogc:def:crs:EPSG`](urn:ogc:def:crs:EPSG)`::4326`</SupportedCRS>
`          `<SupportedFormat ns="http://www.opengis.net/wcs/1.1">`netcdf-cf1.0`</SupportedFormat>
`          `<SupportedFormat ns="http://www.opengis.net/wcs/1.1">`dap2.0`</SupportedFormat>
`       `</Attribute>
`              `</b>
`       `<Grid name="u">
`           `<Attribute name="standard_name" type="String">
`               `<value>`surface_eastward_sea_water_velocity`</value>
`           `</Attribute>
`           `<Attribute name="units" type="String">
`               `<value>`m s-1`</value>
`           `</Attribute>
`           `<Attribute name="_FillValue" type="Int16">
`               `<value>`-32768`</value>
`           `</Attribute>
`           `<Attribute name="scale_factor" type="Float32">
`               `<value>`0.009999999776`</value>
`           `</Attribute>
`           `<Attribute name="ancillary_variables" type="String">
`               `<value>`DOPx`</value>
`           `</Attribute>
`           `<Array name="u">
`               `<Int16/>
`               `<dimension name="time" size="1"/>
`               `<dimension name="lat" size="460"/>
`               `<dimension name="lon" size="701"/>
`           `</Array>
`           `<Map name="time">
`               `<Attribute name="standard_name" type="String">
`                   `<value>`time`</value>
`               `</Attribute>
`               `<Attribute name="units" type="String">
`                   `<value>`seconds since 1970-01-01`</value>
`               `</Attribute>
`               `<Attribute name="calendar" type="String">
`                   `<value>`gregorian`</value>
`               `</Attribute>
`               `<Int32/>
`               `<dimension name="time" size="1"/>
`           `</Map>
`           `<Map name="lat">
`               `<Attribute name="standard_name" type="String">
`                   `<value>`latitude`</value>
`               `</Attribute>
`               `<Attribute name="units" type="String">
`                   `<value>`degrees_north`</value>
`               `</Attribute>
`               `<Float32/>
`               `<dimension name="lat" size="460"/>
`           `</Map>
`           `<Map name="lon">
`               `<Attribute name="standard_name" type="String">
`                   `<value>`longitude`</value>
`               `</Attribute>
`               `<Attribute name="units" type="String">
`                   `<value>`degrees_east`</value>
`               `</Attribute>
`               `<Float32/>
`               `<dimension name="lon" size="701"/>
`           `</Map>
`       `</Grid>
`       `<Grid name="v">
`           `<Attribute name="standard_name" type="String">
`               `<value>`surface_northward_sea_water_velocity`</value>
`           `</Attribute>
`           `<Attribute name="units" type="String">
`               `<value>`m s-1`</value>
`           `</Attribute>
`           `<Attribute name="_FillValue" type="Int16">
`               `<value>`-32768`</value>
`           `</Attribute>
`           `<Attribute name="scale_factor" type="Float32">
`               `<value>`0.009999999776`</value>
`           `</Attribute>
`           `<Attribute name="ancillary_variables" type="String">
`               `<value>`DOPy`</value>
`           `</Attribute>
`           `<Array name="v">
`               `<Int16/>
`               `<dimension name="time" size="1"/>
`               `<dimension name="lat" size="460"/>
`               `<dimension name="lon" size="701"/>
`           `</Array>
`           `<Map name="time">
`               `<Int32/>
`               `<dimension name="time" size="1"/>
`           `</Map>
`           `<Map name="lat">
`               `<Float32/>
`               `<dimension name="lat" size="460"/>
`           `</Map>
`           `<Map name="lon">
`               `<Float32/>
`               `<dimension name="lon" size="701"/>
`           `</Map>
`       `</Grid>
`       `<Grid name="DOPx">
`           `<Attribute name="long_name" type="String">
`               `<value>`longitudinal dilution of precision`</value>
`           `</Attribute>
`           `<Attribute name="comment" type="String">
`               `<value>`The longitudinal dilution of precision (DOPx) represents the`

contribution of the radars' configuration geometry to uncertainty in the
eastward velocity estimate (u). DOPx is a direct multiplier of the
standard error in obtaining the standard deviation for the eastward
velocity estimate from the least squares best fit. DOPx and DOPy are
commonly used to obtain the geometric dilution of precision (GDOP =
sqrt(DOPx^2 + DOPy^2)), a useful metric for filtering errant velocities
due to poor geometry.</value>

`           `</Attribute>
`           `<Attribute name="_FillValue" type="Int16">
`               `<value>`-32768`</value>
`           `</Attribute>
`           `<Attribute name="scale_factor" type="Float32">
`               `<value>`0.009999999776`</value>
`           `</Attribute>
`           `<Array name="DOPx">
`               `<Int16/>
`               `<dimension name="time" size="1"/>
`               `<dimension name="lat" size="460"/>
`               `<dimension name="lon" size="701"/>
`           `</Array>
`           `<Map name="time">
`               `<Int32/>
`               `<dimension name="time" size="1"/>
`           `</Map>
`           `<Map name="lat">
`               `<Float32/>
`               `<dimension name="lat" size="460"/>
`           `</Map>
`           `<Map name="lon">
`               `<Float32/>
`               `<dimension name="lon" size="701"/>
`           `</Map>
`       `</Grid>
`       `<Grid name="DOPy">
`           `<Attribute name="long_name" type="String">
`               `<value>`latitudinal dilution of precision`</value>
`           `</Attribute>
`           `<Attribute name="comment" type="String">
`               `<value>`The latitudinal dilution of precision (DOPy) represents the`

contribution of the radars' configuration geometry to uncertainty in the
northward velocity estimate (v). DOPy is a direct multiplier of the
standard error in obtaining the standard deviation for the northward
velocity estimate from the least squares best fit. DOPx and DOPy are
commonly used to obtain the geometric dilution of precision (GDOP =
sqrt(DOPx^2 + DOPy^2)), a useful metric for filtering errant velocities
due to poor geometry.</value>

`           `</Attribute>
`           `<Attribute name="_FillValue" type="Int16">
`               `<value>`-32768`</value>
`           `</Attribute>
`           `<Attribute name="scale_factor" type="Float32">
`               `<value>`0.009999999776`</value>
`           `</Attribute>
`           `<Array name="DOPy">
`               `<Int16/>
`               `<dimension name="time" size="1"/>
`               `<dimension name="lat" size="460"/>
`               `<dimension name="lon" size="701"/>
`           `</Array>
`           `<Map name="time">
`               `<Int32/>
`               `<dimension name="time" size="1"/>
`           `</Map>
`           `<Map name="lat">
`               `<Float32/>
`               `<dimension name="lat" size="460"/>
`           `</Map>
`           `<Map name="lon">
`               `<Float32/>
`               `<dimension name="lon" size="701"/>
`           `</Map>
`       `</Grid>
`       `<Array name="site_lat">
`           `<Attribute name="long_name" type="String">
`               `<value>`Contributing radar site latitudes`</value>
`           `</Attribute>
`           `<Attribute name="standard_name" type="String">
`               `<value>`latitude`</value>
`           `</Attribute>
`           `<Attribute name="units" type="String">
`               `<value>`degrees_north`</value>
`           `</Attribute>
`           `<Float32/>
`           `<dimension name="nSites" size="27"/>
`       `</Array>
`       `<Array name="site_lon">
`           `<Attribute name="long_name" type="String">
`               `<value>`Contributing radar site longitudes`</value>
`           `</Attribute>
`           `<Attribute name="standard_name" type="String">
`               `<value>`longitude`</value>
`           `</Attribute>
`           `<Attribute name="units" type="String">
`               `<value>`degrees_east`</value>
`           `</Attribute>
`           `<Float32/>
`           `<dimension name="nSites" size="27"/>
`       `</Array>
`       `<Array name="site_code">
`           `<Attribute name="long_name" type="String">
`               `<value>`Contributing radar site code`</value>
`           `</Attribute>
`           `<Attribute name="string_length" type="Int32">
`               `<value>`25`</value>
`           `</Attribute>
`           `<String/>
`           `<dimension name="nSites" size="27"/>
`       `</Array>
`       `<Array name="site_netCode">
`           `<Attribute name="long_name" type="String">
`               `<value>`Contributing radar site network affiliation code`</value>
`           `</Attribute>
`           `<Attribute name="string_length" type="Int32">
`               `<value>`25`</value>
`           `</Attribute>
`           `<String/>
`           `<dimension name="nSites" size="27"/>
`       `</Array>
`       `<Array name="procParams">
`           `<Attribute name="long_name" type="String">
`               `<value>`RTV processing parameters`</value>
`           `</Attribute>
`           `<Attribute name="comment" type="String">
`               `<value>

01\) Maximum GDOP threshold 02) Maximum speed threshold (cm s-1) 03)
Minimum number of sites required 04) Minimum number of radials required
05) Maximum angular gap to interpolate radial

`   data over (degrees, 0 = no interpolation)`

06\) Maximum gap in range to interpolate radial

`   data over (range-resolution, 0 = no interpolation)`

07\) Spatial search radius for radial solutions (km)</value>

`           `</Attribute>
`           `<Float32/>
`           `<dimension name="nProcParam" size="7"/>
`       `</Array>
`   `
`       `<dataBLOB href=""/>
`   `</Dataset>