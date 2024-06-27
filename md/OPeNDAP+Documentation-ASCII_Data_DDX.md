## Proposal

Although inefficient, a pure XML data response could be very useful for
inferencing engines. I imagine a response in which variables contain
their values, much like Attributes. Consider this modified DDX:

<Dataset name="bears.nc"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 ns="http://xml.opendap.org/ns/DAP/3.3#"
 >
`   `<Grid name="bears">
`       `<Array name="bears">
`           < Float32/>`
`           `<dimension name="i" size="2"/>
`           `<dimension name="j" size="3"/>
`           `<value>`72.022`</value>
`           `<value>`72.184`</value>
`           `<value>`72.256`</value>
`           `<value>`72.745`</value>
`           `<value>`72.297`</value>
`           `<value>`72.367`</value>
`       `</Array>
`       `<Map name="i">
`           `<Int32/>
`           `<dimension name="i" size="2"/>
`           `<value>`1`</value>
`           `<value>`2`</value>
`       `</Map>
`       `<Map name="j">
`           `<Float32/>
`           `<dimension name="j" size="3"/>
`           `<value>`92`</value>
`           `<value>`93`</value>
`           `<value>`94`</value>
`       `</Map>
`   `</Grid>
</Dataset>

We would need to iron out the details of how the <value> elements could
be are for the various data types. Possible ideas:

- Simply use the same ordering and markers we use now in the XDR encoded
  binary content, and simply produce all of the values as their ASCII
  versions, suitably encoded for XML content.
  - Grids, and Structures could embed the variable values as above.
  - Sequences would have a a single list of values whose deserialization
    into the DAP data object would be governed by the template.

<Dataset name="bears.nc"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 ns="http://xml.opendap.org/ns/DAP/3.3#"
 >
`   `<Sequence name="bears">
`       `<Array name="bears">
`           `<Float32/>
`           `<dimension name="i" size="2"/>
`           `<dimension name="j" size="3"/>
`       `</Array>
`       `<Array name="i">
`           `<Int32/>
`           `<dimension name="i" size="2"/>
`       `</Map>
`       `<Aray name="j">
`           `<Float32/>
`           `<dimension name="j" size="3"/>
`       `</Map>
`       `<value>`0x5a`</value>
`       `<value>`72.022`</value>
`       `<value>`72.184`</value>
`       `<value>`72.256`</value>
`       `<value>`72.745`</value>
`       `<value>`72.297`</value>
`       `<value>`72.367`</value>
`       `<value>`1`</value>
`       `<value>`2`</value>
`       `<value>`92`</value>
`       `<value>`93`</value>
`       `<value>`94`</value>
`       `<value>`0x5a`</value>
`       `<value>`72.022`</value>
`       `<value>`72.184`</value>
`       `<value>`72.256`</value>
`       `<value>`72.745`</value>
`       `<value>`72.297`</value>
`       `<value>`72.367`</value>
`       `<value>`1`</value>
`       `<value>`2`</value>
`       `<value>`92`</value>
`       `<value>`93`</value>
`       `<value>`94`</value>
`       `<value>`0xa5`</value>
`   `</Sequence>
</Dataset>

:\* Alternately we could try embedding the values in the template
variables. Each variable would have a sequence of <value> elements
representing its value in each row of the parent Sequence. I think this
is sub optimal because of memory and response generation efficiency
concerns.

## Questions

What extension will we use to trigger this response?

Suggestions:

- .dodx (not in use as an extension)
- .axml (may be in use)
- .ascx (in use <http://www.fileinfo.com/extension/ascx>)
- .addx (not in use)