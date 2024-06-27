## Background

When we designed Hyrax the BES supported multiple catalogs. At that time
the BES had file system based catalog (a.k.a. the catalog called
"catalog"), and a catalog implementation for the CEDAR ([Coupling,
Energetics and Dynamics of Atmospheric
Regions](http://cedarweb.hao.ucar.edu/wiki/index.php/Main_Page)).

## Problem addressed

At the time we designed Hyrax we chose (consciously or not) to ignore
the facility of the BES to support multiple catalogs by not making space
in the URL scheme for them.

The URL scheme defines a path through a directed graph. And in this
graph the Hyrax server presents it holdings. THe OLFS provides a
facility to map secondary BES's into the graph based on a path prefix
that allows these secondary BES's to be injected into any part of the
graph.

<figure>
<img src="Hyrax-1.8-Catalog.png" title="Hyrax-1.8-Catalog.png"
width="800" />
<figcaption>Hyrax-1.8-Catalog.png</figcaption>
</figure>

However, the system relies entirely on the use of the BES catalog called
"catalog" which in turn relies on a filesystem to build it's holdings.
Since that time other BES catalogs have been developed (gateway catalog,
relational database catalog, etc.) , but there is no simple facility for
mapping them into the directed graph of the Hyrax holdings. Each one
requires the OLFS be extended to provide a mapping into the graph, and
to instruct the BES which catalog to use.

## Proposed solution

Create a BES meta-catalog (the catalog called "root" or the "root
catalog"??) which subsumes all of the catalogs in a particular BES.
(Should the root catalog be an extension of the catalog "catalog"??)

<figure>
<img src="Hyrax-2.0-Catalog.png" title="Hyrax-2.0-Catalog.png"
width="800" />
<figcaption>Hyrax-2.0-Catalog.png</figcaption>
</figure>

The root catalog should behave as follows -

1.  At it's top level it should reveal to the world the contents of the
    file system catalog ("catalog") PLUS
2.  It should inject into the top level catalog a "node" for each of the
    other catalogs in use.
3.  The name of these child catalog nodes should be the name of the
    catalog (or be configurable better yet)
4.  If the child catalog name conflicts with one of the file system
    holdings then the catalog should take precedence, and the BES should
    write an ~~error~~([Jimg](User:Jimg "wikilink")) warning message
    regarding this to it's logs.
5.  Access to the child catalogs should be seamless. In other words
    using the root catalog should allow one to exercise this command:


    <font size="2"><bes:setContainer name="catalogContainer" space="root">`/rdh/CTD`</bes:setContainer></font>

    Which should be setting the container to use the CTD dataset in the
    RDH catalog in the above example.

How do we determine what catalogs are present?
I am thinking that although run time discovery from some registry would
be cool, that's probably more practical in Java land. We probably should
use the configuration of the root catalog to define all of the catalogs
that it will subsume.

## Rationale for the solution

Doing this will change the current situation where adding a new catalog
to the BES requires additional coding in the OLFS to provide access to
that catalog. This is usually a fairly cumbersome process and it is
anticipated that making this change will reduce the overall OLFS code
volume and make the server more flexible.

## Discussion

[ndp](User:Ndp "wikilink") 19:03, 11 April 2012 (PDT)

- It seems like it might be pretty cool to allow the meta catalog to use
  any of the catalogs as "root" and map the others into the top level of
  that. Or maybe even map the others into not just the top level (maybe
  that's a default) but to any path specified in a configuration of the
  root catalog...
- We might consider making the root catalog be configurable to build the
  root node as a collection of the root nodes of it's catalogs. Or it
  could be configured to use any one of it's catalogs as a root catalog
  and then map the others into it's root node.