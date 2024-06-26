This is based on this data set:
<http://dev1.opendap.org:8080/opendap/netcdf/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.ddx>

<?xml version="1.0" encoding="UTF-8"?>

<Capabilities ns="http://www.opengis.net/wcs/1.1"
 xmlns:ows="http://www.opengis.net/ows/1.1"
 xmlns:xlink="http://www.w3.org/1999/xlink"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.opengis.net/wcs/1.1  ../wcsGetCapabilities.xsd
 http://www.opengis.net/ows/1.1 ../../../ows/1.1.0/owsAll.xsd"
 version="1.1.2" updateSequence="1.0">
`  `
`  `
`  `
`  `<ows:ServiceIdentification>
`     `<ows:Title>`OPeNDAP Development WCS`</ows:Title>
`     `<ows:Abstract>`A WCS service under development as a part of the OPeNDAP Hyrax server.`</ows:Abstract>
`     `<ows:Keywords>
`        `<ows:Keyword>`Web Coverage Service`</ows:Keyword>
`        `<ows:Keyword>`OPeNDAP`</ows:Keyword>
`        `<ows:Keyword>`NetCDF`</ows:Keyword>
`     `</ows:Keywords>
`     `<ows:ServiceType>`WCS`</ows:ServiceType>
`     `<ows:ServiceTypeVersion>`1.1.2`</ows:ServiceTypeVersion>
`     `<ows:Fees>`NONE`</ows:Fees>
`     `<ows:AccessConstraints>`NONE`</ows:AccessConstraints>
`  `</ows:ServiceIdentification>
`  `
`  `
`  `
`  `<ows:ServiceProvider>
`     `<ows:ProviderName>`OPeNDAP`</ows:ProviderName>
`     `<ows:ProviderSite xlink:href="http://www.opendap.org"/>
`     `<ows:ServiceContact>
`        `<ows:IndividualName>`Nathan Potter`</ows:IndividualName>
`        `<ows:PositionName>`Senior Developer`</ows:PositionName>
`        `<ows:ContactInfo>
`           `<ows:Phone>
`              `<ows:Voice>`401-555-7890`</ows:Voice>
`              `<ows:Facsimile>`401-555-8901`</ows:Facsimile>
`           `</ows:Phone>
`           `<ows:Address>
`              `<ows:DeliveryPoint>`165 Dean Knauss Dr.`</ows:DeliveryPoint>
`              `<ows:City>`Narragansett`</ows:City>
`              `<ows:AdministrativeArea>`Rhode Island`</ows:AdministrativeArea>
`              `<ows:PostalCode>`02882`</ows:PostalCode>
`              `<ows:Country>`USA`</ows:Country>
`              `<ows:ElectronicMailAddress>`support[at]opendap[dot]org`</ows:ElectronicMailAddress>
`           `</ows:Address>
`           `<ows:OnlineResource xlink:href="http://www.opendap.org/support/index.html"/>
`           `<ows:HoursOfService>`24x7x365`</ows:HoursOfService>
`           `<ows:ContactInstructions>`email`</ows:ContactInstructions>
`        `</ows:ContactInfo>
`        `<ows:Role>`Developer`</ows:Role>
`     `</ows:ServiceContact>
`  `</ows:ServiceProvider>
`  `
`  `
`  `
`  `<ows:OperationsMetadata>
`     `<ows:Operation name="GetCapabilities">
`        `<ows:DCP>
`           `<ows:HTTP>
`              `<ows:Get xlink:href="http://ndp.opendap:8080/opendap/WCS?"/>
`           `</ows:HTTP>
`        `</ows:DCP>
`        `<ows:Parameter name="service">
`           `<ows:AllowedValues>
`              `<ows:Value>`WCS`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`        `<ows:Parameter name="version">
`           `<ows:AllowedValues>
`              `<ows:Value>`1.1.2`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`        `<ows:Parameter name="AcceptVersions">
`           `<ows:AllowedValues>
`              `<ows:Value>`1.1.2`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`     `</ows:Operation>
`     `<ows:Operation name="DescribeCoverage">
`        `<ows:DCP>
`           `<ows:HTTP>
`              `<ows:Get xlink:href="http://ndp.opendap:8080/opendap/WCS?"/>
`           `</ows:HTTP>
`        `</ows:DCP>
`        `<ows:Parameter name="service">
`           `<ows:AllowedValues>
`              `<ows:Value>`WCS`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`        `<ows:Parameter name="version">
`           `<ows:AllowedValues>
`              `<ows:Value>`1.1.2`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`        `<ows:Parameter name="Identifier">
`           `<ows:AllowedValues>
`              `<ows:Value>`coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`     `</ows:Operation>
`     `<ows:Operation name="GetCoverage">
`        `<ows:DCP>
`           `<ows:HTTP>
`              `<ows:Get xlink:href="http://ndp.opendap:8080/opendap/WCS?"/>
`           `</ows:HTTP>
`        `</ows:DCP>
`        `<ows:Parameter name="service">
`           `<ows:AllowedValues>
`              `<ows:Value>`WCS`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`        `<ows:Parameter name="version">
`           `<ows:AllowedValues>
`              `<ows:Value>`1.1.2`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`        `<ows:Parameter name="Identifier">
`           `<ows:AllowedValues>
`              `<ows:Value>`coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`        `<ows:Parameter name="InterpolationType">
`           `<ows:AllowedValues>
`              `<ows:Value>`nearest`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`        `<ows:Parameter name="format">
`           `<ows:AllowedValues>
`              `<ows:Value>`netcdf-cf1.0`</ows:Value>
`              `<ows:Value>`dap2.0`</ows:Value>
`           `</ows:AllowedValues>
`        `</ows:Parameter>
`     `</ows:Operation>
`  `</ows:OperationsMetadata>
`  `
`  `
`  `
`  `<Contents>
`     `<CoverageSummary>
`        `<ows:Title>`6km HF Radar data`</ows:Title>
`        `<ows:Abstract>`A test dataset for use developing WCS services.`</ows:Abstract>
`        `<ows:Metadata xlink:href="http://ndp.opendap:8080/opendap/coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.das"/>
`        `<ows:WGS84BoundingBox>
`           `<ows:LowerCorner>`-97.8839 21.736`</ows:LowerCorner>
`           `<ows:UpperCorner>`-57.2312 46.4944`</ows:UpperCorner>
`        `</ows:WGS84BoundingBox>
`        `<Identifier>`coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc`</Identifier>
`     `</CoverageSummary>
`     `<SupportedCRS>[`urn:ogc:def:crs,crs:EPSG:6.3:4326`](urn:ogc:def:crs,crs:EPSG:6.3:4326)</SupportedCRS>
`     `<SupportedFormat>`netcdf-cf1.0`</SupportedFormat>
`     `<SupportedFormat>`dap2.0`</SupportedFormat>
`  `</Contents>
</Capabilities>

------------------------------------------------------------------------

[Category:WCS](Category:WCS "wikilink")