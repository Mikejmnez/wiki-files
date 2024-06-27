## Description

**Implemented in DAP 3.2**

Based on this recommendation: <http://www.w3.org/TR/xmlbase/#syntax>

Add the attribute *xml:base* to the *dap:Dataset* element.

The namespace associated with the *xml* prefix is:
*<http://www.w3.org/XML/1998/namespace>* and the prefix MUST be declared
in the *dap:Dataset* element:

<Dataset
    ns="http://xml.opendap.org/ns/DAP/3.2#"
    xmlns:xml="http://www.w3.org/XML/1998/namespace"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://xml.opendap.org/ns/DAP/3.2#  http://xml.opendap.org/dap/dap3.2.xsd"
    name="200803061600_HFRadar_USEGC_6km_rtv_SIO.nc"
    xml:base="http://dev1.opendap.org:8080/opendap/netcdf/examples/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.ddx"
 >

## Hyrax Implementation

To cause the BES to pass the *xml:base* to libdap for inclusion in the
DDX we need to Implement a new context for **setContext**:

`   `<setContext name="xml:base">*`valueOf(xml:base)`*</setContext>

Thus, when asking for a DDX the OLFS woudl send:

<?xml version="1.0" encoding="UTF-8"?>

<request ns="http://xml.opendap.org/ns/bes/1.0#" reqID="###">
`    `<setContext name="xdap_accept">`3.2`</setContext>
`    `<setContext name="dap_explicit_containers">`no`</setContext>
`    `<setContext name="errors">`dap2`</setContext>
`    `**<setContext name="xml:base">[`http://localhost:8080/opendap/bears.nc.ddx`](http://localhost:8080/opendap/bears.nc.ddx)</setContext>**
`    `<setContainer name="catalogContainer" space="catalog">`/bears.nc`</setContainer>
`    `<define name="d1" space="default">
`        `<container name="catalogContainer" />
`    `</define>
`    `<get type="ddx" definition="d1" />
</request>

And although the html_form response also needs a request URL, it wants
the request URL sans request suffix. So an html_form request remains
unchanged:

<?xml version="1.0" encoding="UTF-8"?>

<request ns="http://xml.opendap.org/ns/bes/1.0#" reqID="###">
`    `<setContext name="xdap_accept">`2.0`</setContext>
`    `<setContext name="dap_explicit_containers">`no`</setContext>
`    `<setContext name="errors">`xml`</setContext>
`    `<setContainer name="catalogContainer" space="catalog">`/bears.nc`</setContainer>
`    `<define name="d1" space="default">
`        `<container name="catalogContainer" />
`    `</define>
`    <get type="html_form" definition="d1" `**`url="`[`http://localhost:8080/opendap/bears.nc`](http://localhost:8080/opendap/bears.nc)`"`**` />`
</request>

[DDXXMLBase](Category:Development "wikilink")
[DDXXMLBase](Category:DAP4 "wikilink")