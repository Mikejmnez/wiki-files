## Definitions

Catalog
A collection of data holdings on a DAP server. This collection may be
flat (a simple list) or it may be hierarchically organized.

<!-- -->

Navigation URL
A URL that returns an HTML document that when rendered allows a human to
navigate the catalog of data holdings on a DAP server.

<!-- -->

Catalog URL
Inventory URL
A URL that returns a machine readable catalog (aka inventory) of data
holdings on a DAP server. The most common of these is a THREDDS catalog
URL.

<!-- -->

Resource URL
The globally (like internet globally) unique address of a resource.

For example:

- <http://test.opendap.org:8080/opendap/> - refers to an HTML document
  that provides a human readable inventory of the top level of holdings
  in a Hyrax server.
- <http://test.opendap.org:8080/opendap/data/nc/fnoc1.nc.dds> - refers
  to a DDS, a machine readable document describing the syntactic
  structure of a DAP data source.
-

<!-- -->

Resource ID
The locally unique ID of a resource. Locally here means within a
particular server.



In the BES that means the catalog name followed by the path in that
catalog to the resource.

For example:

- */rdh/dataSourceOne* - is the resource ID of the resource called
  *dataSourceOne* in the catalog called *rdh*
- */catalog/data/nc/fnoc1.nc* - is the resource ID of the resource
  called *data/nc/fnoc1.nc* in the catalog called *catalog*
- */data/nc/fnoc1.nc* - is the resource ID of the resource called
  *data/nc/fnoc1.nc* in the default catalog (which is confusingly named
  *catalog*)



In Hyrax that means the all of the stuff after the protocol, server
name, and context name.

In the URL *http://test.opendap.org:8080/opendap/data/nc/fnoc1.nc.dds*
the resource ID is: */data/nc/fnoc1.nc.dds*

<!-- -->

Data access URL
A URL that returns DAP data product (DDS, DDX, DAS, DataDDS, etc.) from
a holding on a DAP server.

## RDH Catalog

The RDH will need to have it's own catalog in the BES catalog system, by
default it should be called **rdh**. It can be remapped to a different
name via the BES configuration file if needed (see below).

In order to populate its catalog the RDH needs to be able to provide a
representation of its data holdings. Since the RDH relies on a simple
list of ODBC Data Sources in its configuration file (See the [RDH
Configuration Use Case](Adding_the_RDH_to_the_BES "wikilink") and the
[RDH Catalog Use Case](RDH_handles_bes:showCatalog_request "wikilink"))
it's catalog is flat, just the simple list of Data Source names.

In BES speak that means that RDH catalog contains no sub collections,
aka containers, aka child catalogs. The RDH holdings can and should be
represented as a simple list of DAP data sets. Each of these data sets
has a DDS/DDX/DAS representation that can be accessed in the typical DAP
manner. When responding to a bes:showCatalog request the RDH should
return a catalog composed of a top level dataset that contains a list of
dataset elements each of which has a *node* attribute whose value is
false and (at minimum) a child bes:serviceRef element with a value of
"dap" :

` `<response ns="http://xml.opendap.org/ns/bes/1.0#" reqID="some_unique_value">
`   `<showCatalog>
`       `<dataset catalog="rdh" count="13" lastModified="2009-03-24T19:02:46" name="/" node="true" size="578">
`           `<dataset catalog="catalog" lastModified="-1" name="dataSourceOne" node="false" size="-1">
`               `<serviceRef>`dap`</serviceRef>
`           `</dataset>
`           `<dataset catalog="catalog" lastModified="-1" name="dataSourceTwo" node="false" size="-1">
`               `<serviceRef>`dap`</serviceRef>
`           `</dataset>
`           `<dataset catalog="catalog" lastModified="-1" name="dataSourceThree" node="false" size="-1">
`               `<serviceRef>`dap`</serviceRef>
`           `</dataset>
`       `</dataset>
`   `</showCatalog>
</response>

#### *size* Attribute

What does the size of the dataset mean in this context?

- Is it the aggregate size of all of the holdings in the Data Source?
- Can that be easily determined through the ODBC API?

If determining the size of the holding is a sensible activity (there is
a decent API, it's time efficient, etc) then we should do it. Otherwise
return a "-1" to indicate that the value is not known.

#### *lastModified* Attribute

What does the last modified date of the dataset mean in this context?

- Is it the the last time time that data was added to the Data Source?
- Is is the last time the Data Source definition was changed?
- What happens if the Data Source defines a subset of the available
  holdings in the underlying RDBMS?
  - Is last modified then the last time that one of the tables or views
    in the RDBMS made are available through the Data Source was changed?
- Can that be easily determined through the ODBC API?

If determining the last modified time of the holding is a sensible
activity (there is a decent API, it's time efficient, etc) then we
should do it. Otherwise we should return a "-1" to indicate that the
value is not known. (<font color=red>Is that right? What's the missing
value for this in the BES XML API?</font>)

## Hyrax/BES catalog integration issues

Currently the BES supports multiple catalogs. Hyrax does not. Hyrax
relies solely on the default BES catalog. In order for the RDH to
successfully integrate into Hyrax the various BES catalogs must be
exposed through the OLFS (and thus Hyrax).

<u>**[This work is being discussed and managed
here.](Fixing_the_(currently)_broken_Hyrax_catalogs "wikilink")**</u>