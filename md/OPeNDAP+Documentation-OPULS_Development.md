## OPULS Process

[Design Proposal Process](DAP4:_Design_Proposal_Process "wikilink")

Short version: Each proposal is in one of the following categories:

- New;
- Under Consideration;
- Accepted;
- Declined;
- Under Revision; or
- Obsolete

## DAP4 Specification

This section is deprecated in favor of the top-level [DAP4 Specification
page](DAP4_Specification "wikilink").

The draft specification has two volumes.

1.  [Overview: New Features Introduced in
    DAP4](DAP4:_Overview "wikilink")
2.  [Volume 1: Data Model, Persistent Representation, and
    Constraints](DAP4:_Specification_Volume_1 "wikilink")
    - [DAP4: Specification Volume 1
      Deltas](DAP4:_Specification_Volume_1_Deltas "wikilink")
3.  [Volume 2: Web Services
    Specification](DAP4:_Specification_Volume_2 "wikilink")
4.  [Volume 3: DAP4 Extensions](DAP4:_Specification_Volume_3 "wikilink")
5.  DAP4 Extensions
    - [DAP4 Extension: CSV Data Encoding and
      Response](DAP4_Extension:_CSV_Data_Encoding_and_Response "wikilink")
    - [DAP4 Extension: NetCDF Data
      Response](DAP4_Extension:_NetCDF_Data_Response "wikilink")
    - [DAP4 Extension: Asynchronous
      Response](DAP4_Extension:_Asynchronous_Response "wikilink")
    - [DAP4 Extension: JSON Data and Metadata
      Encoding](DAP4_Extension:_JSON_Data_and_Metadata_Encoding "wikilink")

## DAP Use Cases

As part of the DAP2 --\> DAP4 transition process, we are soliciting *use
cases regarding DAP client application development* from the community.
If you'd like to submit a use case, you can send it to any of the OPULS
developers (e.g., [James Gallagher](mailto:jgallagher@openadp.org)) or
the [OPeNDAP technical discussion
list](mailto:opendap-tech@opendap.org). If you'd like you use case to be
anonymous, that's fine, just let us know.

We will use these use cases to spot likely problems that may be
encountered by members of the community who have developed specific
client applications that interact with DAP servers.

### Use cases from GSFC

These use cases were submitted by Chris Lynnes:

Use Case \#1: Spatial/Variable Subsetter. Our Simple Subset Wizard (SSW) works by constructing URLs to download spatial / variable subsets from the server as netCDF files (http://disc.gsfc.nasa.gov/SSW). This has allowed us to stop writing custom subsetter programs for each dataset or group of datasets.

<!-- -->

Use Case \#2: Giovanni (http://giovanni.gsfc.nasa.gov/giovanni/) provides data exploration capabilities for datasets from GES DISC as well as a few select other providers. Data are acquired from servers via OPeNDAP, saved as netCDF/CF1, which is the Giovanni internal standard. This allows the deployment of many netCDF-capable tools for analysis and data manipulation purposes.

<!-- -->

Use Case \#3: MapServer provides WMS services for GES DISC data. Rather than site individual instances on each data server, we use the .vrt (Virtual files) capability in MapServer to set up the OPeNDAP connections needed to acquire data for the MapServer.

<!-- -->

Use Case \#4: We are currently experimenting with the NcML aggregation capability to see if it is feasible to offer the ability to generate time series at a point using just Hyrax and the NcML handler.

## DAP4 Features Straw man

This is a straw man design, broken down into sections. It is incomplete
and its content is subject to revision. <font color="red">This was our
foil; don't use it.</font> The two documents about under 'DAP
Specification' are the current state of the DAP4 specification.

1.  ~~[Versions](DAP4:_DAP_Versions "wikilink")~~
2.  ~~[Checksum](DAP4:_Checksum "wikilink")~~
3.  ~~[DAP Service Terminus](DAP_Service_Terminus "wikilink")~~
4.  ~~[Data Model](DAP4:_Data_Model "wikilink") and a [XML
    Schema](http://scm.opendap.org/trac/browser/trunk/xml/dap/dap4.xsd)
    that provides one representation for the data model.~~
5.  ~~[Requests](DAP4:_Requests "wikilink")~~
6.  ~~[Responses](DAP4:_Responses "wikilink")~~
7.  ~~[Web Services (Starting
    Point)](DAP4_Web_Services_-_StartingPoint "wikilink")~~

## Proposals

### Data model

| Proposal                                                                                                                        | Status                                                                                            | Author                                          | Date Proposed          |
|---------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|-------------------------------------------------|------------------------|
| [Grids Ideas](DAP4:_DAP4_Grids_Proposal "wikilink")                                                                             | **Accepted**                                                                                      | Dennis, et al.                                  | (Cleaned up 4/10/2012) |
| [XML Use in DAP4](DAP4:_DAP4_XML "wikilink")                                                                                    | **Accepted**                                                                                      | Dennis                                          | 2/25/2012              |
| [Top Level Group](DAP4:_Top_Level_Group "wikilink")                                                                             | **Accepted**                                                                                      | Dennis                                          | 4/12/2012              |
| [Proposal for Supporting Keys in Sequences](DAP4:_Proposal_for_Keys_in_Sequences "wikilink")                                    | **Revised**                                                                                       | Dennis                                          | 5/28/2012              |
| [Constraints and Shared Dimensions](DAP4:_Constraints_and_Shared_Dimensions "wikilink")                                         | **Under Consideration**, *closely related to [Grids Ideas](DAP4:_DAP4_Grids_Proposal "wikilink")* | James & Dennis                                  | Modified 04/12/2012    |
| [Ideas for Defining VLens (and Sequences)](DAP4:_VLens_(and_Sequences) "wikilink")                                              | **Under Consideration**                                                                           | Dennis                                          | 02/25/2012             |
| [Path Specification in DAP4](DAP4:_DAP4_Paths "wikilink")                                                                       | **New**                                                                                           | Dennis                                          | 3/30/2012              |
| [Coordinate Systems Element](DAP4:_Coordinate_Systems_Element "wikilink")                                                       | **New**                                                                                           | Dennis                                          | 4/10/2012              |
| [Nested Attributes](DAP4:_Nested_Attributes "wikilink")                                                                         | **New**                                                                                           | Dennis                                          | 4/12/2012              |
| [Subsetting Arrays and Grids By Value](DAP4:_Subsetting_Arrays_and_Grids_By_Value "wikilink")                                   | **New**                                                                                           | Nathan                                          | 4/27/2012              |
| [Proposal for a Constraint Notation](DAP4:_Proposal_for_a_Constraint_Notation "wikilink")                                       | **New**                                                                                           | Dennis                                          | 4/30/2012              |
| [Proposal for an Abstract Constraint Specification](DAP4:_Proposal_for_an_abstract_Constraint_specification "wikilink")         | **New**                                                                                           | James, ed.                                      | 5/22/2012              |
| [Proposal for Structure Projection](DAP4:_Proposal_for_Structure_Projection "wikilink")                                         | **New**                                                                                           | Dennis                                          | 5/1/2012               |
| [Old Version of Grids Proposal](DAP4:_DAP4_Grids_Ideas_(Old_Version) "wikilink")                                                | **Obsolete**                                                                                      | Dennis                                          | 4/10/2012              |
| [Filter Constraints](DAP4:_DAP4_Filter_Constraints "wikilink")                                                                  | **new**                                                                                           | Dennis, John, Ethan                             | 9/17/2012              |
| [Constraint Expressions](DAP4:_DAP4_Constraint_Expressions "wikilink")                                                          | **new**                                                                                           | Everyone; original note by Dennis, ed. by James | 10/29/2012             |
| [DAP4 Projection Syntax](DAP4:_DAP4_Projection_Syntax "wikilink")                                                               | **withdrawn**                                                                                     | Dennis                                          | 2/8/2013               |
| [Proposed Changes to Checksumming](DAP4:_DAP4_Checksum_Changes "wikilink")                                                      | **accepted with modifications 3/19/2013**                                                         | Dennis                                          | 3/1/2013               |
| [Proposal for new VLEN representation](DAP4:_VLEN_proposal "wikilink")                                                          | **New**                                                                                           | Dennis                                          | 7/21/2013              |
| [Proposal for a Comprehensive Constraint Expression Syntax](DAP4:_Constraint_Expressions,_v2 "wikilink")                        | **New**                                                                                           | James, Dennis, et al.                           | 7/29/2013              |
| [Alternate Proposal for a Constraint Expression Syntax](DAP4:_Alternate_Proposal_for_a_Constraint_Expression_Syntax "wikilink") | **New**                                                                                           | Dennis                                          | 10/16/2013             |
| [Type 1 Server Side Functions Proposal](DAP4:_Type_1_Server_Side_Functions "wikilink")                                          | **New**                                                                                           | Dennis                                          | 1/4/16                 |

DAP4 Data Model Proposals

### Responses Specific to DAP4

<table>
<caption>Persistent Representation</caption>
<thead>
<tr class="header">
<th><p>Proposal</p></th>
<th><p>Status</p></th>
<th><p>Author</p></th>
<th><p>Date Proposed</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><a href="DAP4_Error_Response" title="wikilink">Error
Response</a></p></td>
<td><p><strong>new</strong></p></td>
<td><p>Nathan</p></td>
<td><p>11/19/2013</p></td>
</tr>
<tr class="even">
<td><p><a href="DAP4:_DAP4_Using_Multi-part_Mime"
title="wikilink">Proposal to use Multi-part Mime</a></p></td>
<td><p><strong>new 3/19/2013</strong></p></td>
<td><p>Dennis</p></td>
<td><p>3/1/2013</p></td>
</tr>
<tr class="odd">
<td><p><a href="DAP4:_Inclusion_of_response_metadata_in_the_DMR"
title="wikilink">DAP4: Inclusion of response metadata in the
DMR</a></p></td>
<td><p><strong>New</strong></p></td>
<td><p>James</p></td>
<td><p>7/23/2013</p></td>
</tr>
<tr class="even">
<td><p><a href="DAP4:_DDX_Grammar" title="wikilink">DAP4 DDX
Grammar</a></p></td>
<td><p><strong>Under Consideration</strong></p></td>
<td><p>Dennis</p></td>
<td></td>
</tr>
<tr class="odd">
<td><hr/></td>
<td><hr/></td>
<td><hr/></td>
<td><hr/></td>
</tr>
<tr class="even">
<td><p><a href="DAP4:_DDX_Lexical_Elements" title="wikilink">DAP4: DDX
Lexical Elements</a></p></td>
<td><p><strong>Accepted</strong></p></td>
<td><p>Dennis</p></td>
<td><p>2/28/2012</p></td>
</tr>
<tr class="odd">
<td><p><a href="DAP4:_DAP4_Escapes" title="wikilink">Character Escape
Mechanisms in DAP4</a></p></td>
<td><p><strong>Accepted</strong></p></td>
<td><p>Dennis</p></td>
<td><p>4/5/2012</p></td>
</tr>
<tr class="even">
<td><p><a href="DAP4:_Encoding_for_the_Data_Response"
title="wikilink">DAP4: Encoding for the Data Response</a></p></td>
<td><p><strong>Accepted</strong></p></td>
<td><p>James, based on <a href="DAP4:_DAP4_On_the_Wire_Format"
title="wikilink">Proposed DAP4 On-The-Wire Format</a> by Dennis</p></td>
<td><p>6/5/2012, updated 8/31/12</p></td>
</tr>
<tr class="odd">
<td><p><a href="DAP4:_Chunked_encoding" title="wikilink">DAP4: Chunked
encoding</a></p></td>
<td><p><strong>Accepted</strong></p></td>
<td><p>James</p></td>
<td><p>6/8/2012, updated 8/31/12</p></td>
</tr>
<tr class="even">
<td><hr/></td>
<td><hr/></td>
<td><hr/></td>
<td><hr/></td>
</tr>
<tr class="odd">
<td><p><a href="DAP4:_DAP4_On_the_Wire_Format" title="wikilink">Proposed
DAP4 On-The-Wire Format</a></p></td>
<td><p><strong>Obsolete</strong></p></td>
<td><p>Dennis</p></td>
<td><p>4/8/2012</p></td>
</tr>
<tr class="even">
<td><p><a href="DAP4:_DAP4_Multipart_Mime_Format"
title="wikilink">Proposed Multipart Mime Format</a></p></td>
<td><p><strong>Obsolete</strong></p></td>
<td><p>Dennis</p></td>
<td><p>4/8/2012</p></td>
</tr>
<tr class="odd">
<td><p><a href="DAP4:_DAP4_Error_Responses" title="wikilink">Proposed
Error Response Format</a></p></td>
<td><p><strong>Obsolete</strong></p></td>
<td><p>Dennis</p></td>
<td><p>5/8/2012</p></td>
</tr>
<tr class="even">
<td><p><a href="DAP4:_DAP4_Replacing_Chunking" title="wikilink">Proposal
to Replace Chunking</a></p></td>
<td><p><strong>Obsolete</strong></p></td>
<td><p>Dennis</p></td>
<td><p>8/23/2012</p></td>
</tr>
</tbody>
</table>

Persistent Representation

### Web & HTTP

<table>
<caption>Web Service Proposals</caption>
<thead>
<tr class="header">
<th><p>Proposal</p></th>
<th><p>Status</p></th>
<th><p>Author</p></th>
<th><p>Date Proposed</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><a href="DAP4:_Constraint_and_Query" title="wikilink">DAP4
Constraint expressions and query strings.</a></p></td>
<td><p><strong>Under Consideration</strong></p></td>
<td><p>Nathan Potter</p></td>
<td><p>09/24/2012</p></td>
</tr>
<tr class="even">
<td><p><a href="DAP4:_Possible_Notation_for_Server_Commands"
title="wikilink">Possible Notation for Server Commands</a></p></td>
<td><p><strong>New</strong></p></td>
<td><p>Dennis</p></td>
<td><p>Modified 04/12/2012</p></td>
</tr>
<tr class="odd">
<td><hr/></td>
<td><hr/></td>
<td><hr/></td>
<td><hr/></td>
</tr>
<tr class="even">
<td><p><a href="DAP4:_Asynchronous_Request-Response_Proposal_v3"
title="wikilink">DAP4 Asynchronous Request-Response Proposal (new new
version)</a></p></td>
<td><p><strong>Accepted</strong></p></td>
<td><p>Ethan Davis and Nathan Potter and James Gallagher</p></td>
<td><p>6/15/2012; updated 1 Aug 2012</p></td>
</tr>
<tr class="odd">
<td><p><del><a href="DAP4_Web_Services_v3" title="wikilink">DAP4 Web
Services Proposal version 3</a></del><ins><a
href="DAP4:_Specification_Volume_2" title="wikilink">Volume 2: Web
Services Specification</a></ins></p></td>
<td><p><strong>Accepted</strong></p></td>
<td><p>Nathan Potter</p></td>
<td><p>6/18/2012</p></td>
</tr>
<tr class="even">
<td><p><a href="DAP4_Dataset_Services_Response" title="wikilink">DAP4
Dataset Services Response</a></p></td>
<td><p><strong>Accepted</strong></p></td>
<td><p>Nathan Potter</p></td>
<td><p>6/19/2012</p></td>
</tr>
<tr class="odd">
<td><hr/></td>
<td><hr/></td>
<td><hr/></td>
<td><hr/></td>
</tr>
<tr class="even">
<td><p><del><a href="DAP4:_Asynchronous_Responses"
title="wikilink">Asynchronous Responses</a></del></p></td>
<td><p><del><strong>Obsolete</strong></del></p></td>
<td><p><del>Nathan</del></p></td>
<td><p><del>4/5/2012</del></p></td>
</tr>
<tr class="odd">
<td><p><del><a href="DAP4:_Asynchronous_Request-Response_Proposal"
title="wikilink">DAP4 Asynchronous Request-Response
Proposal</a></del></p></td>
<td><p><del><strong>Obsolete</strong></del></p></td>
<td><p><del>EthanDavis</del></p></td>
<td><p><del>4/10/2012</del></p></td>
</tr>
<tr class="even">
<td><p><del><a href="DAP4:_Capabilities_and_Versioning"
title="wikilink">DAP4 Capabilities and Versioning</a></del></p></td>
<td><p><del><strong>Obsolete</strong></del></p></td>
<td><p><del>EthanDavis</del></p></td>
<td><p><del>4/10/2012</del></p></td>
</tr>
</tbody>
</table>

Web Service Proposals

## Commentary on DAP4 Related Topics

These are not intended as specific proposal, but rather as commentary on
some issues with DAP4 that need addressing with specific proposals.

1.  [Characterization of URL
    Annotations](DAP4:_URL_Annotations "wikilink") (Modified: 2/26/2012)
2.  [Constructing a DDX from a
    Query](DAP4:_Constructing_a_DDX_from_a_Query "wikilink") (new:
    04/12/2012)
3.  [Notes on Constraints](DAP4:_Notes_on_Constraints "wikilink") (new:
    04/29/2012)
4.  [DAP4: An Essay on Domain Specific
    Models](DAP4:_An_Essay_on_Domain_Specific_Models "wikilink") (new:
    05/05/2012)

## OPULS Implementation Thoughts

### Unidata's Current Thoughts

Unidata needs:

` 1) DAP4 C client library for use in netCDF-C library`
` 2) DAP4 Java client library for use in netCDF-Java library`
` 3) DAP4 Java server library for use in TDS`
` 4) Conformance test service against which a DAP4 client library can`
`    be tested`
` 5) Conformance test service against which a DAP4 server can be`
`    tested`

The current CDM and TDS use of opendap-java library has performance
problems due to extra API layers and extra copying of data. We would
prefer direct serialization between CDM objects and the DAP on-the-wire
data and would recommend that the OPeNDAP Java library leverage the
netCDF-Java libraries.

If there are needs that the DAP4 opendap-java library be independent of
the netCDF-Java library, then in order for CDM and TDS to use the
opendap-java library, we would need the performance concerns mentioned
above addressed. A reasonable metric would be within some percentage of
cdmremote\[1\], say within 20-25%.

Unidata's performance requirements for Java libraries:

` 1) The netCDF-Java library performance reading DAP4 through the DAP4`
`    Java client library must perform within 20-25% of cdmremote [1].`
` 2) TDS performance when handling DAP4 requests using the DAP4 Java`
`    server library should perform within 20-25% of cdmremote [1].`

We believe this means that the DAP4 Java libraries must provide a
sufficiently generic API that can be used directly with the CDM/TDS IOSP
interface.

Unidata will:

` 1) Write the DAP4 C client library.`
` 2) ??? some part of Java server library`
` 3) ??? some parts of conformance testing services`

NOTES:

\[1\] cdmremote is an experimental CDM streaming service. It is
basically a direct serialization of the CDM.

## OPULS references

- <http://www.mnot.net/blog/2011/10/25/web_api_versioning_smackdown>

## Old DAP4 Design and Implementation

- [DAP3/4](DAP3/4 "wikilink")
- [DAP 4.0 Design](DAP_4.0_Design "wikilink") and [DAP 4.0 Essential
  Features](DAP_4.0_Essential_Features "wikilink") The *design* is meant
  to be a complete document while *essential features* are the minimal
  see we want to release. Since this task has been stalled for nearly a
  year, doing the latter seems like a noble goal.