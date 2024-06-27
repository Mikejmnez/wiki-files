- Use a multipart MIME document to hold the DDX and one *data blob* per
  DDX. Adopt the same use of Multipart MIME as WCS uses.

<!-- -->

- consider the following design idea for embedding type information
  within the data stream:

> In DAP2, the DDS is used as a descriptive header for data. For gridded
> data that works OK but for point data it doesn't work so well. When
> the data are read as from a stream, it's OK, but when the data are
> read and stored, then used, they need to be in a linked structure.
> Because the DDS contains the data type definition and not the value,
> it does not hold a structure suitable for holding values. Look at how
> the d_values field in Sequence works (see Sequence::print_val(),
> deserialize()). A better approach would be to encode the type of a
> variable in the data stream itself and have the reader (client most of
> the time) build instances as needed. For arrays, structures and simple
> types this is mostly a wash, but for Sequences it would be a major
> plus because the protocol could support sequences of complex objects
> more easily. It would also get rid of the odd situation where a DDS
> holds a type definition for a nested sequence while the top-most
> sequence holds the tree of objects which hold values. (I.E. the child
> sequences in the DDS don't hold data at all).

> As an alternative, suppose we build a data response using the DDX in a
> multipart document and then encoded type information in the data
> stream as well? <s>This would provide a way to bundle attributes with
> variables in the data response and locate type information with the
> data values (for sequences mostly).</s> <font color="red">It's not
> possible to return the Attributes with a constrained DDX because the
> constraint can alter the attributes in ways that cannot be computed
> without semantic knowledge about the attribute.</font>

[See also the Dap 4.0 Design for the
DDX](DAP_4.0_Design#DataDDX "wikilink") Note that there are some
contradictions between the two documents.

## Questions to consider

1.  Will we be able to implement it efficiently?
2.  How can we get those pesky reliable error messages/objects in there?
3.  What will trigger our server to send this response?

## Normative References

[Multipart MIME](http://tools.ietf.org/html/rfc2112)

[Xlink](http://www.w3.org/TR/xlink/)

## Using Multipart MIME for the DataDDX response

The DataDDX response's network representation will be as a Multipart
MIME document with two parts: One part that contains a DDX that contains
zero or more variables; and one part that contains zero or more bytes of
XDR-encoded data which corresponds to the variables declared in the DDX.
The *Data* element in the DDX holds an xlink reference to this second
part.

The DataDDX may be empty to account for cases where a dataset contains
only type definitions, something that never happens now but which is an
emerging feature of both HDF5 and NetCDF4.

The DataDDX will always have two parts, even if the second 'data' part
is empty so that processing software can always assume that a DataDDX
will occupy two parts of a multipart MIME document.

The *Data* element is used to link the DDX, in one part, to the data
values, in another part, so that other protocols (e.g., DAP-SOAP) can
package several responses in one document easily. That is, while this
design does not provide for that capability, it is easily extensible to
one that does.

### Adding the *Data* element

In addition to the multipart MIME document that holds the two parts of
the data response, the *Data* element holds a reference to the 'data
part' of the response. Here's a sample *Data* element:

    <Data xmlns:xlink="http://www.w3.org/XML/1999/xlink"
          xlink:href="cid:6efa6ea4:98eda872192:-1ed1" xlink:type="simple"/>

### Example DataDDX response Sent via HTTP 1.1

Note that this example shows the DataDDX being returned using HTTP/1.1.
In past versions of DAP important information was encoded in the HTTP
response headers. In this example the key information, that this
response conforms to DAP version 3.2 is encoded both in the response
header and the DDX response element using the *dap-version* attribute.
This makes the DataDDX more friendly toward applications which use
non-HTTP transport protocols.

<font size="2">

``` xml
 HTTP/1.1 200 OK
 Server: Apache-Coyote/1.1
 Content-Type: multipart/related; type="text/xml"; start="<080B6DC4AC8AF0C43041C57CE8DE9646>"; boundary="--mimepart_7_9651610.1145395859678"
 Date: Tue, 18 Apr 2006 21:30:59 GMT
 XDAP: 3.2
 Connection: close

 --mimepart_7_9651610.1145395859678
 Content-Type: text/xml; charset=UTF-8
 Content-Transfer-Encoding: binary
 Content-Id: <080B6DC4AC8AF0C43041C57CE8DE9646>

 <?xml version="1.0" encoding="UTF-8"?>

     <Dataset
              xmlns:xml="http://www.w3.org/XML/1998/namespace"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://xml.opendap.org/ns/DAP/3.2#  http://xml.opendap.org/dap/dap/3.2.xsd"
              ns="http://xml.opendap.org/ns/DAP/3.2#"
              xmlns:dap="http://xml.opendap.org/ns/DAP/3.2#"
              xml:base="http://test.opendap.org/dap/data/nc/fnoc1.nc.ddx"
              dap_version="3.2"
              name="fnoc1.nc">

        <Data xmlns:xlink="http://www.w3.org/XML/1999/xlink"
              xlink:href="cid:6efa6ea4:98eda872192:-1ed1" xlink:type="simple"/>

        <Attribute name="NC_GLOBAL" type="Container">
         <Attribute name="base_time" type="String">
             <value>"88- 10-00:00:00"</value>
         </Attribute>
         <Attribute name="title" type="String">
             <value>" FNOC UV wind components from 1988- 10 to 1988- 13."</value>
         </Attribute>
     </Attribute>
     <Attribute name="DODS_EXTRA" type="Container">
         <Attribute name="Unlimited_Dimension" type="String">
             <value>"time_a"</value>
         </Attribute>
     </Attribute>
     <Array name="v">
         <Attribute name="units" type="String">
         <value>"meter per second"</value>
         </Attribute>
             <Attribute name="long_name" type="String">
         <value>"Vector wind northward component"</value>
         </Attribute>
         <Attribute name="missing_value" type="String">
         <value>"-32767"</value>
         </Attribute>
         <Attribute name="scale_factor" type="String">
         <value>"0.005"</value>
         </Attribute>
         <Int16/>
         <dimension name="time_a" size="16"/>
         <dimension name="lat" size="17"/>
         <dimension name="lon" size="21"/>
     </Array>
     </Dataset>
 </xml>

 --mimepart_7_9651610.1145395859678
 Content-Type: application/octet-stream
 Content-Transfer-Encoding: binary
 Content-Id: 6efa6ea4:98eda872192:-1ed1

   Here be the XDR encoded binary stuff that is the data from the GetDATA request

 --mimepart_7_9651610.1145395859678--
```

</font>

## Encoding the binary data

How we encode the binary data will determine if a client can reasonably
be expected to recognize when the data serialization code has stumbled
and been forced to send an error message in place of the data. Another
issue we might look at is the current cumbersome way of encoding
Sequence data - it places a heavy burden on the server and has always
had significant issues (i.e., it's broken).

### Reliable Errors in the Hyrax Response

The BES and OLFS communicate using data transmissions that are
*chunked*. That means that the BES first sends a seven-byte ASCII-HEX
*byte count* and *control character* of *d* for data (for a total of
eight bytes) followed by that number of bytes of data, zero or more
times followed by a *byte count/control character* sequence of
0x0000000d (seven ASCII digits plus a *d*). If the BES encounters an
error it sends a *byte count* and *control character* that is out of
band control information and then the error in a following data block.
The chunking scheme is described on the Trac site in [BES
Chunking](https://scm.opendap.org/trac/wiki/BES_Chunking).

What currently happens in the OLFS to suppoprt DAP2 responses is that
the *byte count* is read, then the following block of data is read and
passed onto the client, then the next *byte count* is read, ..., until
the 0x0000000d is read signaling the end of the document. That is the
chunked nature of the data transmission is stripped so that the data
Hyrax now (DAP 2.0 to 3.2) sends is not chunked.

I would propose that for DAP 3.2 (or 3.3?) we make it so that those byte
counts are passed onto the client in the data part of the DataDDX
response. This will allow Hyrax to send errors and other out-of-band
information to clients in a way that client can actually use. See
[Chunking](Hyrax_-_BES_PPT#PPT_Chunking "wikilink") for a description;
it's fairly simple.

### Encoding Sequence Data

Adopt the suggestion that type information be included in the data
stream. This will duplicate some (small) amount of information, but make
the software to decode the data easier to write, including particularly,
nested sequences.

[DataDDX](Category:Development "wikilink")
[DataDDX](Category:DAP4 "wikilink")