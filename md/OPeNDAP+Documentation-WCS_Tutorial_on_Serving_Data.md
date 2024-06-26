A work in progress that is not little more than notes.

Here's an NcML file that will take the tried and true *COADS Climaology*
file and add enough metadata for the WCS service to build a valid
coverage: <font size="2">

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<netcdf location="/data/nc/coads_climatology.nc" ns="http://www.unidata.ucar.edu/namespaces/netcdf/ncml-2.2">

  <attribute type="Structure" name="NC_GLOBAL">
    <attribute type="String" name="Conventions" value="CF-1.0"/>
  </attribute>

  <!-- We could use UDD metadata instead of this. -->
  <attribute name="WcsMetadata" type="OtherXML">
    <Domain ns="http://www.opengis.net/wcs/1.1" xmlns:ows="http://www.opengis.net/ows/1.1" xmlns:gml="http://www.opengis.net/gml">
      <SpatialDomain>
    <ows:BoundingBox crs="urn:ogc:def:crs:EPSG::4326">
      <ows:LowerCorner>0 -90</ows:LowerCorner>
      <ows:UpperCorner>360 90</ows:UpperCorner>
    </ows:BoundingBox>
      </SpatialDomain>
      <TemporalDomain>
    <TimePeriod>
      <BeginPosition>2002-07-01T09:00/15:00</BeginPosition>
      <EndPosition>2002-07-31T15:00/21:00</EndPosition>
      <TimeResolution>6 hours</TimeResolution>
    </TimePeriod>
      </TemporalDomain>
    </Domain>
   <!--  <SupportedCRS ns="http://www.opengis.net/wcs/1.1">urn:ogc:def:crs:EPSG::4326</SupportedCRS> -->
  </attribute>

  <variable name="SST">
    <variable name="COADSX">
      <attribute name="standard_name" type="String" value="longitude"/>
    </variable>

    <variable name="COADSY">
      <attribute name="standard_name" type="String" value="latitude"/>
    </variable>

    <variable name="TIME">
      <attribute name="standard_name" type="String" value="time"/>
    </variable>
  </variable>

 <variable name="AIRT">
    <variable name="COADSX">
      <attribute name="long_name" type="String" value="longitude"/>
    </variable>

    <variable name="COADSY">
      <attribute name="long_name" type="String" value="latitude"/>
    </variable>

    <variable name="TIME">
      <attribute name="long_name" type="String" value="time"/>
    </variable>
  </variable>

</netcdf>
```

</font>

Here's a note from Benno about using UDD metadata in place of the
cumbersome *WcsMetadata* element:

> UDD is my abbreviation, actually you need to say "Unidata Dataset
> Discovery v1.0", which is far longer than I will ever remember.
> The conventions registry is at
> <http://xml.opendap.org/ontologies/NetcdfConventionRegistry.owl>
>
> which connects the ontology files describing the conventions to the
> strings that identify the convention in the Conventions tag
> We have an example of using UDD.
> <http://test.opendap.org:8090/ioos/mday_joinExist.ncml> which has
> metadata that expands to
> <http://iridl.ldeo.columbia.edu/cgi-bin/view/browse2.pl?node=http%3A%2F%2Ftest.opendap.org%3A8090%2Fioos%2Fmday_joinExist.ncml&repository=opendap1-owl>
>
> Benno