# Slide Templates From IPT

## Slide 1 Template

**PROJECT/WORKING GROUP DESCRIPTION**

- Describe project/working group overview. How does your project/working
  group compliment or link with DIF activities?
- What is the current status? Provide brief update.

## Slide 2 Template

**MILESTONES AND CHALLENGES**

- Identify success stories, project accomplishments, benefits.
- What have been the challenges (technical or strategic) to this
  project/working group? How were challenges resolved?

## Slide 3 Template

**NEXT STEPS/ RECOMMENDATIONS**

- What is the next phase of your project/working group?
- Provide recommendations for going forward.

# SLIDE 1

## OPeNDAP OGC Gateway In Support Of Regional IOOS

The OPeNDAP component of IOOS primary objective is to provide automated
capabilities to serve data via an OGC WCS interface. The automation
component is of high value, since have people produce WCS documents by
hand is too labor intensive.

Many of the data providers in the DIF are already providing OPeNDAP
services (either with OPeNDAP software or other software). Layering
primary IOOS services through the existing OPeNDAP infrastructure will
allow for quicker adoption and deployment of thoses services within the
community.

**Current Status**

- *Semantics* migrating from standalone app in perl/Sesame to
  Java/Sesame by Columbia, we're migrating the Java into Hyrax, to
  provide automated coverage catalog generation at server startup. Added
  'rdf' response to Hyrax and enhancements (new versions) of the DDX in
  support of the RDF activities.
- *NcML* handler developed for Hyrax to augment data sources for
  CF-compliance where necessary for semantic mapping to WCS.
- *File-Out* to NetCDF for WCS coverage payload generation.
- *Aggregation* development underway.

# SLIDE 2

### Prototype Hyrax WCS Service

==== <http://test.opendap.org:8090/opendap/WCS====>
<img src="HyraxWcsPrototype.png" title="HyraxWcsPrototype.png"
width="640" alt="HyraxWcsPrototype.png" />

- **The 'catalog' issue:** Current prototype requires human management
  of the coverage catalog. 'Semantic Mapping' component removes that
  requirement.

<!-- -->

- **The 'aggregation' issue:** Reduction in funding in year-2 removed
  aggregation but we've juggled our effort to restore that work because
  we feel it's required to make WCS useful.

# SLIDE 3

- Developing automated semantic operations against the suite of WCS
  schema.

<!-- -->

- WCS implementation targeting WCS 1.1.2 revision, incompatibility with
  earlier revisions, and potential incompatibility with future revisions
  given WCS evolving schema documents.

<!-- -->

- Difficulty identifying CITE-like test harness to validate service
  responses against.

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

# Draft Text Below

### Current Status

## MIlestones

- Prototype OPeNDAP WCS service developed.
  (http://test.opendap.org:8090/opendap/WCS)
- Progress made on ontology development, semantic inferencing/mapping.
- NcML functionality in support of injecting metadata into exisiting DAP
  data sets (Ancillary Information service - AIS) in beta.
- Relational Database Handler design documents completed.

## Challenges

### WCS

#### GetCapabilities: Semantic Mapping and Aggregation

The OGC GetCapabilites response returns (among other things) a listing
of the Coverages available from the service. Providing this listing for
an OPeNDAP server presents a couple of challenges:

*Identifying which DAP data sets represent wcs:Coverages and mapping their metadata into wcs:CoverageDescription and wcs:CoverageSummary elements*
In our prototype service we have isolated this activity through a
programming API and temporarily implemented it to use user developed
wcs:CoverageDescriptions stored on the local filesystem. These are
connected to data sets available on the server via their ID's. We are
developing a semantic web engine that will ingest RDF representations of
the DAP data sets that conform to the CF-1.0 convention and using OWL
inferencing and semantic query languages to map these representations
into wcs:CoverageDescriptions for the data sets. This will allow the
server to "discover" and generate it's own list o WCS Coverage holdings.

<!-- -->

*Aggregation: Providing a concise view of the available holdings*
OPeNDAP servers often contain hundreds, if not thousands, of data sets
that could be mapped to a wcs:Coverage. Generating an ows:Capailities
document with an ows:Contents section that details each one is
problematic. The resulting document would be 100's of megabytes in size
and would potentially exhaust the available memory for clients
attempting to retrieve and parse it. However, in many circumstances,
these large groups of data sets represent could be aggregated on a
common instance variable such as time, elevation, or depth. Providing
the mechanism for such an aggregation in the OPeNDAP servers would
provide a more useful view of the available data, and also decrease the
sheer size of the owc:Capabiities document. In addition adding an
aggregation feature to the OPeNDAP servers has been one of the most
requested by users. This enhances the value of this activity even
further. The aggregations will defined using the same NcML files and
architecture described below.

#### Missing metadata in CF-1.0/DAP provided via NcML

The CF-1.0 convention and other metadata available through the DAP may
be incomplete with respect to providing a complete metadata set for WCS.
In order to address this we have added an Ancillary Information Service
to the OPeNDAP servers through which users may add missing metadata to
data sets in the system. It is implemented to use NcML files to provide
this information as the NcML is already heavily in use in the THREDDS
Data Server community and many of the existing OPeNDAP users are already
familiar with the syntax and use model.

#### WCS schema issues

WCS schema documents as provided by the OGC cannot be used for
validation, as they themselves will not pass through a validating
parser. This blocks the primary use of the schema as a mechanism of
"sanitizing" inputs to software through validation. Extensive procedural
code was written to validate incoming and outgoing WCS documents. In
addition these problems block the use of common software automation
tools such as as JAXB which can be used to speed software development,
and provide mechanisms for developing more flexible clients.

#### WCS protocol issues (version negotiation)

The current OGC specification for WCS states that the client and server
negotiate the version based on a 2 part version (1.0 vs 1.1 vs 1.2 etc.)
and the 3 part version numbers refer to versions of the schema.

While this simplifies version negotiation, it places unreasonable
burdens on the authors of both clients and servers. Essentially this
versioning scheme means clients and servers must "silently" support all
known versions of the schema. If different versions of the schemas
differ by more than whitespace (which they must or there would be no
material change) then the structure/content of the documents described
by the schema must change. Which means that the software that consumes
the documents must be able to cope with the variations.

This issues has been sent to OGC (March 16, 2009), but no reply has been
forthcoming beyond a statement saying that they are debating the issue.
In the meantime, the prototype WCS service has been implemented to
support 3 part version negotiation.

### SOS

Most data served by DAP is gridded and is semantically closest to the
OGC coverage data model. While the SOS specification indicates that it
can (and should?) be used to serve all types of data from all types of
sensors, the available examples focus solely on in situ style
observations. NOAA-IOOS have indicated that they intend to use SOS for
these in situ type measurements, and have developed the required
extensions to the SOS O&M schemas. However the both the SOS
specification and NOAA-IOOS's implementation of it create serious
challenges for OPeNDAP. We feel that while it may be possible to write a
custom OPeNDAP server to work directly with the data held in a
particular frame work, such as the NDBC, the resulting software would be
so site specific that it would be of little use to other partners.

## Next Steps

- Develop WCS catalog based on semantic inferencing of CF-1.0 compliant
  DAP data sets.
- Replace WCS prototype catalog with semantic inferencing catalog.
- Extend core DAP/Hyrax capabilities in general to facilitate DAP use
  with IOOS, and potentially as a element's of NOAA's contribution to
  OOI.
  - Aggregation functionality.
  - NcML functionality to add new metadata (AIS feature)
- Relational Database Handler for Hyrax in support of in situ data sets.

[Category:WCS](Category:WCS "wikilink")