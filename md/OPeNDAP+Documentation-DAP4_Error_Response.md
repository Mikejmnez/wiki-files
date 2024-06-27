The DAP4 protocol returns error information using an Error response. If
a request for any of the three basic responses cannot be completed then
an Error response is returned in its place.

### Schema

The normative XML representation for the Error Response is defined by
the following RELAX-NG schema.

``` xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<grammar ns="http://relaxng.org/ns/structure/1.0" xmlns:doc="http://www.example.com/annotation" datatypeLibrary="http://xml.opendap.org/datatypes/dap4" ns="http://xml.opendap.org/ns/DAP/4.0#">
    <start>
        <ref name="errorresponse"/>
    </start>
    <define name="errorresponse">
        <element name="Error">
            <optional>
                <element name="code">
                    <attribute name="protocol">
                        <data type="text"/>
                    </attribute>
                    <data type="dap4_integer"/>
                </element>
            </optional>
            <optional>
                <interleave>
                    <element name="Message">
                        <text/>
                    </element>
                    <element name="Context">
                        <text/>
                    </element>
                    <element name="OtherInformation">
                        <text/>
                    </element>
                </interleave>
            </optional>
        </element>
    </define>
</grammar>
```

The body of the <Error> element may contain any or all of the following
inner elements:

1.  <code> — An numerically valued error code. This code must be
    associated with a protocol, such as HTTP, via the *protocol*
    attribute. It is not a requirement that the protocol over which the
    originating request and subsequent Error response where transmitted
    be the same as the protocol identified by the value of the
    *protocol* attribute.
2.  <Message> — A short informative text message describing the error.
3.  <Context> — Textual information describing the context in which the
    error occurred: position of a parse error in a constraint
    expression, for example.
4.  <OtherInformation> — Arbitrary additional text information: a Java
    stack trace, for example.

### Error Response Resource Role

DAP4 Error Responses are identified by the resource role:


**`http://services.opendap.org/dap4/error`**

### Normative Encoding of the Error Response

The normative XML representation for the Error Response is defined in
Appendix x "Normative XML Encoding of the Error Response". The media
type for the normative XML representation is:


<font size="2">**`application/vnd.opendap.dap4.error.xml`**</font>

### Examples

Not Found

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<dap4:Error xmlns:dap4="http://xml.opendap.org/ns/DAP/4.0#">
    <code protocol="http">404</code>
    <Message>Unable to locate requested resource</Message>
</dap4:Error>
```

Parse Error

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<dap4:Error xmlns:dap4="http://xml.opendap.org/ns/DAP/4.0#">
    <code protocol="http">400</code>
    <Message>Bad DAP4 Request Syntax</Message>
    <Context>
        The constraint expression "?u[3][lat<66]" failed to parse at the sub expression "lat<66".
         Relational syntax expressions are not supported for array subsetting operations.
    </Context>
</dap4:Error>
```