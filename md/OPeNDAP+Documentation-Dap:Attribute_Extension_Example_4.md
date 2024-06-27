## Example 4

Here we mingle WCS content into the DDX document. In this example the
wcs:CoverageDescription contains/encapsulated the DDX content. In
addition, key components of the coverage such as the wcs:Field elements
are correctly associated with the DDX elements that they are describing.

#### Source XML

<?xml version="1.0" encoding="UTF-8"?>

<Dataset name="200803061600_HFRadar_USEGC_6km_rtv_SIO.nc"
         ns="http://xml.opendap.org/ns/DAP2"
         xmlns:wcs="http://www.opengis.net/wcs/1.1"
         xmlns:ows="http://www.opengis.net/ows/1.1"
         xmlns:owcs="http://www.opengis.net/wcs/1.1/ows"
         xmlns:gml="http://www.opengis.net/gml/3.2"
         xmlns:xlink="http://www.w3.org/1999/xlink"
        >

`   `<wcs:CoverageDescription>

`       `<ows:Title>`Near-Real Time Surface Ocean Velocity`</ows:Title>
`       `<ows:Abstract>`CoverageDescription generated by OPeNDAP WCS UseCase 2.0`</ows:Abstract>
`       `<wcs:Identifier>`coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc`</wcs:Identifier>
`       `<wcs:Domain>
`           `<wcs:SpatialDomain>
`               `<ows:BoundingBox crs="urn:ogc:def:crs:EPSG::4326">
`                   `<ows:LowerCorner>`-97.8839 21.736`</ows:LowerCorner>
`                   `<ows:UpperCorner>`-57.2312 46.4944`</ows:UpperCorner>
`               `</ows:BoundingBox>
`           `</wcs:SpatialDomain>
`           `<wcs:TemporalDomain>
`               `<gml:timePosition>`2008-03-27T16:00:00.000Z`</gml:timePosition>
`           `</wcs:TemporalDomain>
`       `</wcs:Domain>
`       `<wcs:Range>

`           `<Attribute name="NC_GLOBAL" type="Container">
`               `<Attribute name="Conventions" type="String">
`                   `<value>`CF-1.1`</value>
`               `</Attribute>
`               `<Attribute name="title" type="String">
`                   `<value>`Near-Real Time Surface Ocean Velocity`</value>
`               `</Attribute>
`               `<Attribute name="institution" type="String">
`                   `<value>`Scripps Institution of Oceanography`</value>
`               `</Attribute>
`               `<Attribute name="source" type="String">
`                   `<value>`Surface Ocean HF-Radar`</value>
`               `</Attribute>
`               `<Attribute name="history" type="String">
`                   `<value>`12-Mar-2008 22:26:19: NetCDF file created`</value>
`               `</Attribute>
`           `</Attribute>

`           `<Grid name="u">
`                 `**<wcs:Field>**
`                    `**<ows:Title>`surface_eastward_sea_water_velocity`</ows:Title>**
`                    `**<ows:Abstract>`Eastward component of a 2D sea surface velocity vector.`</ows:Abstract>**
`                    `**<wcs:Identifier>`u`</wcs:Identifier>**
`                    `**<wcs:Definition>**
`                       `**<ows:AnyValue/>**
`                    `**</wcs:Definition>**
`                    `**<wcs:NullValue>`-32768`</wcs:NullValue>**
`                    `**<owcs:InterpolationMethods>**
`                       `**<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>**
`                    `**</owcs:InterpolationMethods>**
`                 `**</wcs:Field>**
`               `<Attribute name="standard_name" type="String">
`                   `<value>`surface_eastward_sea_water_velocity`</value>
`               `</Attribute>
`               `<Attribute name="units" type="String">
`                   `<value>`m s-1`</value>
`               `</Attribute>
`               `<Attribute name="_FillValue" type="Int16">
`                   `<value>`-32768`</value>
`               `</Attribute>
`               `<Attribute name="scale_factor" type="Float32">
`                   `<value>`0.009999999776`</value>
`               `</Attribute>
`               `<Attribute name="ancillary_variables" type="String">
`                   `<value>`DOPx`</value>
`               `</Attribute>
`               `<Array name="u">
`                   `<Int16/>
`                   `<dimension name="time" size="1"/>
`                   `<dimension name="lat" size="460"/>
`                   `<dimension name="lon" size="701"/>
`               `</Array>
`               `<Map name="time">
`                   `<Int32/>
`                   `<dimension name="time" size="1"/>
`               `</Map>
`               `<Map name="lat">
`                   `<Float32/>
`                   `<dimension name="lat" size="460"/>
`               `</Map>
`               `<Map name="lon">
`                   `<Float32/>
`                   `<dimension name="lon" size="701"/>
`               `</Map>
`           `</Grid>

`       `</wcs:Range>

`       `<wcs:SupportedCRS>[`urn:ogc:def:crs:EPSG`](urn:ogc:def:crs:EPSG)`::4326`</wcs:SupportedCRS>
`       `<wcs:SupportedFormat>`netcdf-cf1.0`</wcs:SupportedFormat>
`       `<wcs:SupportedFormat>`dap2.0`</wcs:SupportedFormat>
`   `</wcs:CoverageDescription>
`   `<dataBLOB href=""/>

</Dataset>

#### Namespace Blocking Transform

Returns all elements NOT in the DAP2 namespace. Or in other words,
discard elements in the DAP2 namespace:

<xsl:stylesheet version="1.0"
                xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
                xmlns:dap="http://xml.opendap.org/ns/DAP2" >
`   `<xsl:output method='xml' version='1.0' encoding='UTF-8' indent='yes'/>

`   `<xsl:strip-space elements="*" />

`   `<xsl:template match="*">
`       `<xsl:choose>
`           `**<xsl:when test="namespace-uri()!='http://xml.opendap.org/ns/DAP2'">**
`               `<xsl:copy >
`                   `<xsl:call-template name="textAndattributes" />
`                   `<xsl:apply-templates />
`               `</xsl:copy>
`           `</xsl:when>
`           `<xsl:otherwise>
`               `<xsl:apply-templates />
`           `</xsl:otherwise>
`       `</xsl:choose>
`   `</xsl:template>

`   `<xsl:template  match="@*|text()" />

`   `<xsl:template name="textAndattributes" ><xsl:copy-of select="text()|@*" /></xsl:template>

</xsl:stylesheet>

#### Result

The extracted wcs:CoverageDescription element:

<wcs:CoverageDescription ns="http://xml.opendap.org/ns/DAP2" xmlns:wcs="http://www.opengis.net/wcs/1.1"
                         xmlns:ows="http://www.opengis.net/ows/1.1"
                         xmlns:owcs="http://www.opengis.net/wcs/1.1/ows"
                         xmlns:gml="http://www.opengis.net/gml/3.2"
                         xmlns:xlink="http://www.w3.org/1999/xlink">
`  `<ows:Title>`Near-Real Time Surface Ocean Velocity`</ows:Title>
`  `<ows:Abstract>`CoverageDescription generated by OPeNDAP WCS UseCase 2.0`</ows:Abstract>
`  `<wcs:Identifier>`coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc`</wcs:Identifier>
`  `<wcs:Domain>
`     `<wcs:SpatialDomain>
`        `<ows:BoundingBox crs="urn:ogc:def:crs:EPSG::4326">
`           `<ows:LowerCorner>`-97.8839 21.736`</ows:LowerCorner>
`           `<ows:UpperCorner>`-57.2312 46.4944`</ows:UpperCorner>
`        `</ows:BoundingBox>
`     `</wcs:SpatialDomain>
`     `<wcs:TemporalDomain>
`        `<gml:timePosition>`2008-03-27T16:00:00.000Z`</gml:timePosition>
`     `</wcs:TemporalDomain>
`  `</wcs:Domain>
`  `<wcs:Range>
`     `**<wcs:Field>**
`        `**<ows:Title>`surface_eastward_sea_water_velocity`</ows:Title>**
`        `**<ows:Abstract>`Eastward component of a 2D sea surface velocity vector.`</ows:Abstract>**
`        `**<wcs:Identifier>`u`</wcs:Identifier>**
`        `**<wcs:Definition>**
`           `**<ows:AnyValue/>**
`        `**</wcs:Definition>**
`        `**<wcs:NullValue>`-32768`</wcs:NullValue>**
`        `**<owcs:InterpolationMethods>**
`           `**<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>**
`        `**</owcs:InterpolationMethods>**
`     `**</wcs:Field>**
`  `</wcs:Range>
`  `<wcs:SupportedCRS>[`urn:ogc:def:crs:EPSG`](urn:ogc:def:crs:EPSG)`::4326`</wcs:SupportedCRS>
`  `<wcs:SupportedFormat>`netcdf-cf1.0`</wcs:SupportedFormat>
`  `<wcs:SupportedFormat>`dap2.0`</wcs:SupportedFormat>
</wcs:CoverageDescription>

#### Namespace Passing Transform

Returns all elements NOT in the DAP2 namespace. Or in other words,
discard elements in the DAP2 namespace:

<xsl:stylesheet version="1.0"
                xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
                xmlns:dap="http://xml.opendap.org/ns/DAP2" >
`   `<xsl:output method='xml' version='1.0' encoding='UTF-8' indent='yes'/>

`   `<xsl:strip-space elements="*" />

`   `<xsl:template match="*">
`       `<xsl:choose>
`           `**<xsl:when test="namespace-uri()='http://xml.opendap.org/ns/DAP2'">**
`               `<xsl:copy >
`                   `<xsl:call-template name="textAndattributes" />
`                   `<xsl:apply-templates />
`               `</xsl:copy>
`           `</xsl:when>
`           `<xsl:otherwise>
`               `<xsl:apply-templates />
`           `</xsl:otherwise>
`       `</xsl:choose>
`   `</xsl:template>

`   `<xsl:template  match="@*|text()" />

`   `<xsl:template name="textAndattributes" ><xsl:copy-of select="text()|@*" /></xsl:template>

</xsl:stylesheet>

#### Result

`The DAP2 DDX document minus the foreign namespace XML:`

    <Dataset ns="http://xml.opendap.org/ns/DAP2" xmlns:wcs="http://www.opengis.net/wcs/1.1"
             xmlns:ows="http://www.opengis.net/ows/1.1"
             xmlns:owcs="http://www.opengis.net/wcs/1.1/ows"
             xmlns:gml="http://www.opengis.net/gml/3.2"
             xmlns:xlink="http://www.w3.org/1999/xlink"
             name="200803061600_HFRadar_USEGC_6km_rtv_SIO.nc">
       <Attribute name="NC_GLOBAL" type="Container">
          <Attribute name="Conventions" type="String">
             <value>CF-1.1</value>
          </Attribute>
          <Attribute name="title" type="String">
             <value>Near-Real Time Surface Ocean Velocity</value>
          </Attribute>
          <Attribute name="institution" type="String">
             <value>Scripps Institution of Oceanography</value>
          </Attribute>
          <Attribute name="source" type="String">
             <value>Surface Ocean HF-Radar</value>
          </Attribute>
          <Attribute name="history" type="String">
             <value>12-Mar-2008 22:26:19: NetCDF file created</value>
          </Attribute>
       </Attribute>
       <Grid name="u">
          <Attribute name="standard_name" type="String">
             <value>surface_eastward_sea_water_velocity</value>
          </Attribute>
          <Attribute name="units" type="String">
             <value>m s-1</value>
          </Attribute>
          <Attribute name="_FillValue" type="Int16">
             <value>-32768</value>
          </Attribute>
          <Attribute name="scale_factor" type="Float32">
             <value>0.009999999776</value>
          </Attribute>
          <Attribute name="ancillary_variables" type="String">
             <value>DOPx</value>
          </Attribute>
          <Array name="u">
             <Int16/>
             <dimension name="time" size="1"/>
             <dimension name="lat" size="460"/>
             <dimension name="lon" size="701"/>
          </Array>
          <Map name="time">
             <Int32/>
             <dimension name="time" size="1"/>
          </Map>
          <Map name="lat">
             <Float32/>
             <dimension name="lat" size="460"/>
          </Map>
          <Map name="lon">
             <Float32/>
             <dimension name="lon" size="701"/>
          </Map>
       </Grid>
       <dataBLOB href=""/>
    </Dataset>

[DAP3/4](Category:Development "wikilink")
[DAP3/4](Category:DAP4 "wikilink")