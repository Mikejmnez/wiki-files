# Validation Analysis of WCS Services

I did some quick and dirty validation analysis of the WCS service
responses from existing WCS servers.

------------------------------------------------------------------------

# Site: CEOP AIRS

**URL**: <http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET>

**Sampled**: 04/29/2008

## GetCapablities Response

**Request URL**:
<http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET?SERVICE=WCS&VERSION=1.0.0&REQUEST=GetCapabilities>

### **WCS Schema**: <http://schemas.opengis.net/wcs/1.0.0/wcsCapabilities.xsd>

#### Error 1

*Message*: org.jdom.input.JDOMParseException: Error on line 78:
cvc-complex-type.2.4.a: Invalid content was found starting with element
'description'. One of '{"<http://www.opengis.net/wcs>":lonLatEnvelope}'
is expected.

*Action*: Moved <description> to before the <name> in each
<CoverageOfferingBrief>.

#### Error 2

*Message*: org.xml.sax.SAXParseException: cvc-complex-type.3.1: Value
'WGS84(DD)' of attribute 'srsName' of element 'lonLatEnvelope' is not
valid with respect to the corresponding attribute use. Attribute
'srsName' has a fixed value of '<urn:ogc:def:crs:OGC:1.3:CRS84>'.

*Action*: Changing value of srsName from WGS84(DD) to
<urn:ogc:def:crs:OGC:1.3:CRS84> repairs the problem. Also, since the
value is FIXED by the schema the attribute srsName can be omitted (I
think...)

#### Document validates

## DescribeCoverage Response

**Request URL**:
<http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET?SERVICE=WCS&VERSION=1.0.0&REQUEST=DescribeCoverage&coverage=%22TSurfAir%22>

### **WCS Schema**: <http://schemas.opengis.net/wcs/1.0.0/describeCoverage.xsd>

#### Error 1

*Message*: org.jdom.input.JDOMParseException: Error on line 10:
cvc-complex-type.2.4.a: Invalid content was found starting with element
'description'. One of '{"<http://www.opengis.net/wcs>":lonLatEnvelope}'
is expected.

*Action*: Moved <description> to before the <name> in each
<CoverageOffering>.

#### Error 2

*Message*: org.jdom.input.JDOMParseException: Error on line 7:
cvc-complex-type.3.1: Value 'WGS84(DD)' of attribute 'srsName' of
element 'lonLatEnvelope' is not valid with respect to the corresponding
attribute use. Attribute 'srsName' has a fixed value of
'<urn:ogc:def:crs:OGC:1.3:CRS84>'.

*Action*: Changing value of srsName from 'WGS84(DD)' to
'<urn:ogc:def:crs:OGC:1.3:CRS84>' repairs the problem. Also, since the
value is FIXED by the schema it can be omitted. Just leaving out the
srsName attribute works too.

#### Error 3

*Message*: org.jdom.input.JDOMParseException: Error on line 32:
cvc-complex-type.2.4.a: Invalid content was found starting with element
'gml:Envelope'. One of '{"<http://www.opengis.net/gml>":Grid,
"<http://www.opengis.net/gml>":Polygon}' is expected.

*Action*: In each <wcs:spatialDomain> moved all <gml:Envelope>s to
before the <gml:RectifiedGrid>s.

#### Error 4

*Message*: org.jdom.input.JDOMParseException: Error on line 699:
cvc-complex-type.2.4.a: Invalid content was found starting with element
'gml:timePeriod'. One of '{"<http://www.opengis.net/gml>":timePosition,
"<http://www.opengis.net/wcs>":timePeriod}' is expected.

*Action*: Changed name space of <timePeriod> to WCS 1.0.0 namespace.

#### Error 5

*Message*: org.jdom.input.JDOMParseException: Error on line 700:
cvc-complex-type.2.4.a: Invalid content was found starting with element
'gml:beginPosition'. One of
'{"<http://www.opengis.net/wcs>":beginPosition}' is expected.

*Action*: Changed name space of <beginPosition> to WCS 1.0.0 namespace.

#### Error 6

*Message*: org.jdom.input.JDOMParseException: Error on line 701:
cvc-complex-type.2.4.a: Invalid content was found starting with element
'gml:endPosition'. One of '{"<http://www.opengis.net/wcs>":endPosition}'
is expected.

*Action*: Changed name space of <endPosition> to WCS 1.0.0 namespace.

#### Error 7

*Message*: org.jdom.input.JDOMParseException: Error on line 709:
cvc-complex-type.2.4.a: Invalid content was found starting with element
'description'. One of '{"<http://www.opengis.net/wcs>":axisDescription,
"<http://www.opengis.net/wcs>":nullValues}' is expected.

*Action*: Moved <description> to before the <name> in each <RangeSet>.

#### Error 8

*Message*: org.jdom.input.JDOMParseException: Error on line 720:
cvc-complex-type.2.4.a: Invalid content was found starting with element
'supportedInterpolations'. One of
'{"<http://www.opengis.net/wcs>":supportedFormats}' is expected.

*Action*: Moved <supportedFormats> to before <supportedInterpolations>

#### Error 9

*Message*: org.jdom.input.JDOMParseException: Error on line 726:
cvc-enumeration-valid: Value 'Nearest neighbor' is not facet-valid with
respect to enumeration '\[nearest neighbor, bilinear, bicubic, lost
area, barycentric, none\]'. It must be a value from the enumeration.

*Action*: Changed the case of "Nearest neighbor" to "nearest neighbor"

#### Error 10

*Message*: org.jdom.input.JDOMParseException: Error on line 727:
cvc-enumeration-valid: Value 'Nearest neighbor' is not facet-valid with
respect to enumeration '\[nearest neighbor, bilinear, bicubic, lost
area, barycentric, none\]'. It must be a value from the enumeration.

*Action*: Changed the case of "Nearest neighbor" to "nearest neighbor"

#### Document validates

------------------------------------------------------------------------

# Site: DataFed OGC_NASA

**URL**: <http://webapps.datafed.net/ogc_NASA.wsfl>

**Sampled**: 04/29/2008

## GetCapablities Response

**Request URL**:
<http://webapps.datafed.net/ogc_NASA.wsfl?SERVICE=WCS&VERSION=1.0.0&REQUEST=GetCapabilities>

### **WCS Schema**: <http://datafed.net/xs/OGC/wcs/1.0.0/wcsfix.xsd>

#### Document validates.

### **WCS Schema**: <http://schemas.opengis.net/wcs/1.0.0/wcsCapabilities.xsd>

#### Error 1

*Message*: org.jdom.input.JDOMParseException: Error on line 2:
cvc-elt.1: Cannot find the declaration of element 'WCS_Capabilities'.

*Action*: Added the namespace declaration
xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" and the
schemaLocation xsi:schemaLocation="<http://www.opengis.net/wcs>
<http://schemas.opengis.net/wcs/1.0.0/wcsCapabilities.xsd> attributes to
the WCS_Capabilities element.

#### Error 2

*Message*: org.jdom.input.JDOMParseException: Error on line 71:
cvc-complex-type.3.1: Value 'CRS84' of attribute 'srsName' of element
'lonLatEnvelope' is not valid with respect to the corresponding
attribute use. Attribute 'srsName' has a fixed value of
'<urn:ogc:def:crs:OGC:1.3:CRS84>'.

*Action*: Changed all occurrences of "WGS84(DD)" to
"<urn:ogc:def:crs:OGC:1.3:CRS84>"

#### Document validates.

## DescribeCoverage Response

**Request URL**:
<http://webapps.datafed.net/ogc_NASA.wsfl?SERVICE=WCS&VERSION=1.0.0&REQUEST=DescribeCoverage>

### **WCS Schema**: <http://datafed.net/xs/OGC/wcs/1.0.0/wcsfix.xsd>

#### Document validates.

### **WCS Schema**: <http://schemas.opengis.net/wcs/1.0.0/describeCoverage.xsd>

#### Error 1

*Message*: org.jdom.input.JDOMParseException: Error on line 1:
cvc-elt.1: Cannot find the declaration of element 'CoverageDescription'.

*Action*: Added the namespace declaration
xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" and the
schemaLocation xsi:schemaLocation="<http://www.opengis.net/wcs>
<http://schemas.opengis.net/wcs/1.0.0/describeCoverage.xsd> attributes
to the CoverageDescription element.

#### Error 2

*Message*: org.jdom.input.JDOMParseException: Error on line 10:
cvc-complex-type.3.1: Value 'WGS84(DD)' of attribute 'srsName' of
element 'lonLatEnvelope' is not valid with respect to the corresponding
attribute use. Attribute 'srsName' has a fixed value of
'<urn:ogc:def:crs:OGC:1.3:CRS84>'.

*Action*: Changed all occurrences of srsName="WGS84(DD)" to
srsName="<urn:ogc:def:crs:OGC:1.3:CRS84>"

#### Error

*Message*: org.jdom.input.JDOMParseException: Error on line 40:
cvc-datatype-valid.1.2.1: 'image/gif' is not a valid value for 'Name'.

*Action*:

#### Document Validation Failed

But that's not surprising since the DataFed is using their own version
of the WCS schema.