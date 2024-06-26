## How the WCS service works

<figure>
<img src="WCS_packages_high_level.png"
title="WCS_packages_high_level.png" width="400" />
<figcaption>WCS_packages_high_level.png</figcaption>
</figure>

Just enough information to be dangerous ...

### WCS

The WCS 1.1.2 software implements a WCS service that is close to
generic. It can work using a simple XML catalog that lists WCS coverages
('coverages' hereafter), at lest in theory. This software using the
*semantic.wcs* package to perform actions like access coverages and
rebuild the catalog of served coverages.

### semantics.wcs

The software in *semantic.wcs* is the interface to the 'semantic engine'
that the *IRISail* software implements. It doesn't know anything about
inferencing per se, but it implements an interface to the high-level
operations provided by the inferencing software. All of the code is
contained in one class called *StaticRdfCatalog* that has a number of
public methods that return coverages and other useful information about
coverages. It also contains an *update()* method used to (re)build the
contents of the semantic repository and then the coverages that can be
derived from the information in that repository. The end result of
running the update() method is that an XML file listing the known
coverage descriptions is written into the
*content/WCS/StaticRdfCatalog/coverageXMLfromRDF.xml* file. This file is
then used to build an in-memory representation of WCS coverages that the
other methods of the StaticRdfCatalog class use.

### IRISail

This is the semantic engine. It uses information about WCS and other
metadata standards to build coverages for WCS using whatever metadata is
available. It leverages ontologies that describe the different metadata
standards & conventions in general terms.

The entry point for the inferencing software is the class
*RespoitoryOps*.

The inferencing software uses the *sesame* software for both inferencing
and as a tripple store for information, both initial conditions and
derived information.

### Important classes

These are all *.java* classes and can be found in the
olfs/src/opendap/{semantics,wcs} directories.

#### StaticRdfCatalog

The interface used to access coverages and to build coverages using the
semantic engine.

> This class is used to retrieve coverage descriptions of a data set. A
> Sesame-OWLIM RDF store is initialized and populated and an xml
> document of coverages of the served data sets is generated. This xml
> document is used to answer request posted by a client. The RDF store
> is updated periodically to make sure the information served is up to
> date. Care is taken to make sure the RDF store can be used by multiple
> users at the same time.
> The RDF store is populated by importing and ingesting semantic
> inference rules and owl/xml schema files and rdf files describing data
> sets. The `doNotImportTheseUrls` is a string vector that holds URL of
> bad files that should not be imported. The `catalogCacheDirectory`
> holds the OWLIM persistent directory `owlim_storage_folder`, the
> coverage description document and the dump of the whole repository
> files in triple graph format.

#### RepositoryOps

The interface to the semantic engine.

> This class is the major class that manipulates and maintains the
> repository up to date. Using this class StartingPoint statements are
> introduced in the repository; documents or data sets no longer to be
> served anymore is deleted from the repository.
>
>
> `The repository consists of StartingPoint RDF document and RDF documents needed by those`
>
> StartingPoints. When introducing a StartingPoint into the repository,
> two things need to be added. One is the StartingPoint statement and
> the other is the content of the StartingPoint RDF document. e.g. "x
> rdf:type rdfcache:StartingPoint" is a StartingPoint statement. "x" is
> the URL of the RDF document of a StartingPoint. The startingpoint
> statement is added using this class then the actual content is added
> later using class `RdfImporter`.
> rdfcache =
> \<<http://iridl.ldeo.columbia.edu/ontologies/rdfcache.owl#>\>

#### CoverageDescription

The interface to the collection of coverages.

> An implementation of a wcs:CoverageDescription object. This
> implementation includes methods that assist in the creation of DAP
> constraint expressions to retrieve coverage data as NetCDF.

#### ConstructRuleEvaluator

This class is used to run external inference rules on an Sesame-OWLIM
repository. The inference rules are written in SeRQL. The inferred
statements are added into the repository. The inference rules are run
repeatedly until no rules generate any new statements.