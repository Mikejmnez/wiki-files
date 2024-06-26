## Generating Range Field Elements

### Issues with Definition element

In the Range section of a CoverageDescription, each Field element must
have a child Definition element. The contents/values of this element are
discussed in section 13.2 of the OGC Web Services Common Specification
(OWS) 1.1.0 (File:
06-121r3_OGC_Web_Services_Common_Specification_version_1.1.0_with_Corrigendum_1.pdf).

My reading indicates that we are going to have to delve into the CF
metadata in order to construct this Definition element. The examples
provided by OGC don't provide any details because they simply use the
ows:AnyValue element. And this is not addressed in the *Domenico &
Nativi* proposal for encoding extensions.

Here is an example of that usage from our (in progess)
CoverageDescription:

`   `<Field>
`       `<ows:Title>`surface_eastward_sea_water_velocity`</ows:Title>
`       `<ows:Abstract>`Eastward component of a 2D sea surface velocity vector.`</ows:Abstract>
`       `<Identifier>`u`</Identifier>
`       `<Definition>
`           `<ows:AnyValue/>
`       `</Definition>
`       `<NullValue>`-32768`</NullValue>
`       `<owcs:InterpolationMethods>
`           `<owcs:DefaultMethod>`nearest`</owcs:DefaultMethod>
`       `</owcs:InterpolationMethods>
`   `</Field>

However, the Definition element is the place where the range of the
Field and the units of the Field are held. I'll refrain from putting the
schema segment in here but it is in the WCS folder I passed around:
WCS/OGC/SCHEMAS_OPENGIS_NET/ows/1.1.0/owsDomainType.xsd. The Definition
element is of type UnNamedDomainType.

So lets look at the Attributes from the DDX for u, from the example
above:

`       `<Attribute name="standard_name" type="String">
`           `<value>`surface_eastward_sea_water_velocity`</value>
`       `</Attribute>
`       `<Attribute name="units" type="String">
`           `<value>`m s-1`</value>
`       `</Attribute>
`       `<Attribute name="_FillValue" type="Int16">
`           `<value>`-32768`</value>
`       `</Attribute>
`       `<Attribute name="scale_factor" type="Float32">
`           `<value>`0.009999999776`</value>
`       `</Attribute>
`       `<Attribute name="ancillary_variables" type="String">
`           `<value>`DOPx`</value>
`       `</Attribute>

Which we need to morph into:

`       `<Definition>
`           `<ows:AllowedValues>
`               `<owcs:Range>
`                   `<owcs:MinimumValue>`THE_MINIMUM_VALUE(How do we determine this?)`</owcs:MinimumValue>
`                   `<owcs:MaximumValue>`THE_MAXIMUM_VALUE(How do we determine this?)`</owcs:MaximumValue >
`               `</owcs:Range>
`           `</ows:AllowedValues>
`           `<owcs:DataType owcs:reference="http://link.to.dap.spec">`Int16`</owcs:DataType>
`           `<owcs:ValuesUnit owcs:reference="http://link.to.some.definition.of.meters.per.second.maybe.from.gml?" >`m s-1`</owcs:ValuesUnit>
`           `<ows:MetaData xlink:simpleLink="http://link.to.dataset.ddx?u" >
`               `<ows:AbstractMetaData>
`                   `<Attribute name="scale_factor" type="Float32">
`                       `<value>`0.009999999776`</value>
`                   `</Attribute>
`               `</ows:AbstractMetaData>
`               `<ows:AbstractMetaData>
`                   `<Attribute name="ancillary_variables" type="Float32">
`                       `<value>`DOPx`</value>
`                   `</Attribute>
`               `</ows:AbstractMetaData>
`           `</ows:MetaData>
`       `</Definition>

It may be beyond our scope to try to generate an ows:AllowedValues
element.

We may have to use to ows:AnyValues for the mandatory "PossibleValues"
group.

The other elements are important, I think...

The Metadata section is particularly confusing, as the schema types the
ows:AbstractMetaData element like this:

`   `<element name="AbstractMetaData" abstract="true">
`       `<annotation>
`           `<documentation>`Abstract element containing more metadata about the element `
`               that includes the containing "metadata" element. A specific server implementation, `
`               or an Implementation Specification, can define concrete elements in the `
`               AbstractMetaData substitution group. `</documentation>
`       `</annotation>
`   `</element>

The fact that this is an abstract type leads me to believe we are
intended to develop an additional schema content to describe our
metadata.

[Category:WCS](Category:WCS "wikilink")