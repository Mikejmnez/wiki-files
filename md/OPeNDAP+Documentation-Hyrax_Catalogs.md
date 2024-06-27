## Hyrax/BES catalog integration issues

Up until now Hyrax has been relying on only the default catalog in the
BES. The default catalog is named **catalog** (Unfortunately for those
of us reading and writing about it the word is both a proper noun and a
regular noun in this context - confusing to say the least) The BES
supports any number of other catalogs, each possibly representing a
different repository or collection of data. For example, one catalog
could be the jgofs repository, another catalog a basic directory listing
of data files (default catalog does this), another could represent the
CEDAR Catalog of data (based on a relational database).

Unfortunately when the OLFS was developed it was written to rely on only
the default BES catalog. This manifests in the Hyrax URLs as follows:

The catalog URL *http://localhost:8080/opendap/* refers to the top level
in the default BES catalog. Accessing this URL issues the BES command:

<bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">
`  `<bes:setContext name="errors">`xml`</bes:setContext>
`  `<bes:showCatalog/>
</bes:request>

Notice that:

- The bes:showCatalog element does not specify a particular catalog,
  thus it implicitly specifies the default catalog **catalog**.
- The catalog URL has been reduced to a BES resource ID of */*

Which is equivalent to the BES command:

<bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">
`  `<bes:setContext name="errors">`xml`</bes:setContext>
`  `<bes:showCatalog node="catalog:/" />
</bes:request>

If the OLFS had been written to support the implicit catalog
organization in the BES then the correct catalog URL would have been:
*http://localhost:8080/opendap/catalog/data/nc*

Changing this presents backward compatibility issues for Hyrax in that
it is important that Hyrax not change the access URL's to existing data
sets, catalogs, or inventories.

## Solution

The following is the agreed upon solution by James Gallagher, Nathan
Potter, and Patrick West on April 20, 2011.

The key is to maintain backward compatibility.

So let's say we have three catalogs.

1.  The default catalog named **catalog**
2.  A jgofs catalog named **jgofs**
3.  A netcdf catalog named **nc**

Under the default catalog (which say points to \$prefix/share/hyrax) has
a single subdirectory called **data**.

The request:

<bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">
`  `<bes:setContext name="errors">`xml`</bes:setContext>
`  `<bes:showCatalog/>
</bes:request>

Would have the result:

<?xml version="1.0" encoding="ISO-8859-1"?>

<response reqID="some_unique_value" ns="http://xml.opendap.org/ns/bes/1.0#">
`   `<showCatalog>
`       `<dataset count="3" name="/" node="true">
`           `<dataset count="6" lastModified="2010-05-13T04:35:17" name="data" node="true" size="102"/>
`           `<dataset count="2" lastModified="2011-04-18T18:42:44" name="jgofs" node="true" size="136"/>
`           `<dataset count="9" lastModified="2011-04-20T21:10:51" name="nc" node="true" size="374"/>
`       `</dataset>
`   `</showCatalog>
</response>

Points to bring out:

- The default catalog root is returned as part of the result. The data
  directory has 6 elements under it (let's say), it is a node (has sub
  directories)
- The other two catalogs (jgofs and nc) are both listed here as part of
  the root of the default catalog

A subsequent request to show the data node would look like:

<bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">
`  `<bes:setContext name="errors">`xml`</bes:setContext>
`  `<bes:showCatalog node="data"/>
</bes:request>

Whereas a request to show the root node of the jgofs catalog would look
like:

<bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">
`  `<bes:setContext name="errors">`xml`</bes:setContext>
`  `<bes:showCatalog node="jgofs"/>
</bes:request>

### So what about name clashes

The default catalog always wins. So, if the root of the default catalog,
in this case, had a directory called jgofs, then the request:

<bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">
`  `<bes:setContext name="errors">`xml`</bes:setContext>
`  `<bes:showCatalog node="jgofs"/>
</bes:request>

would return the contents of the jgofs directory in the default catalog,
not the root node of the jgofs catalog.

### What does the OLFS do differently

- Nothing

### What does the BES do differently

- Drop the node="catalog:dir" notation and use node="catalog/dir" or, in
  the case of the default catalog node="dir"
- If the first part of the node (before a slash, or whole thing if no
  slash) matches the name of a catalog, then use the catalog
- Unless where the name matches an element in the default catalog root,
  at which point the default catalog wins
- in a setContainer request, store/space="catalog" still needs to be
  used
  - the value of the setContainer element needs to also be parsed to see
    if there is a catalog name at the front

### What does the Patrick do differently

- Currently, in doing ncml and jgofs work, modified the request to
  include a catalog="" attribute in a showCatalog request
  - remove this attribute
- Somewhere (perhaps in the XML request parsing) grab the node attribute
  in showCatalog, grab the first part (if there is one), and see if a
  catalog by that name exists
  - also need to see if it's something in the root of the default
    catalog (SUCKS)
- When showing the root, instead of displaying the names of the
  catalogs, display the root of the default catalog, and the names of
  the other catalogs
  - if there's a duplicate between the root of the default and a catalog
    name, leave it in. Make the admins change this
- When requesting a node, if the node is in the root of the default
  catalog AND there's a catalog with that name, then log an error
  message using BESLog
- Might want to move the catalog stuff into the bes_dispatch library
  instead of the bes_dap library
- In the setContainer request, look at the value of the element (the
  node) and see if it starts with a catalog name (if
  store/space="catalog")

### Questions

- Should the request for the root node include both the jgofs directory
  and the jgofs catalog? Would be confusing to user, so would say no.
  But might be a good thing if users send messages to the data admins to
  point this out.
  - \[<font color="red">JG: Can you remind me what the JGOFS directory
    holds - the catalog is the collection of served data sets,
    yes?</font>\]
  - This is just an example. Change the name from jgofs to foobar. What
    do I do if there's a catalog names foobar and a directory in the
    root of the default catalog named foobar? In the response to a
    showCatalog, do I include them both? I say yes.
  - As for your jgofs question, directories are shown and can be
    traversed, just like the default catalog. But no files are listed.
    The jgofs objects are listed in the .remoteobjects file, if there is
    one in that directory.
- <font color="red">JG: If we do this, will the SQL handler work?
  Almost? (and we can change the color or these questions once you all
  see them... I don't wan to make it all ugly.</font>
  - I'm afraid I have not yet had time to look at the SQL handler, so am
    not sure.

## Old Notes with Comments

### Possible Solutions

In order to acheive this compatibility the following schemes have been
proposed:

#### OLFS DispatchHandler for each catalog

For each catalog that gets added to the BES a new OLFS DispatchHandler
is written. This really would entail making a copy of all the classes in
the java package *opendap.bes* and modifying them slightly to allow for
the different catalog names. It's a crummy solution because it grows the
code base in a crazy way - lot's of replicate functionality that becomes
a real problem to maintain as changes to the BES API will have to be
implemented in multiple places in the OLFS code base. This, I predict
won't happen and things will constantly broken.

It also means that every time someone writes a BES module that provides
a complete DAP services stack and a new catalog that Hyrax will be
unable to take advantage of it without having a java program written to
support it. That's a bloody waste of resources.

#### BES interprets resource ID's

In lieu of specifying the catalog name explicitly in the various BES
requests, the BES will look at the first part of the resource ID and
compare it to its list of catalogs. If the first part of the resource ID
matches a catalog name then the BES will pass the request to that
catalog. Otherwise it will pass the request to the default catalog.

##### Keep existing URLs working

For example, in the catalog URL *http://localhost:8080/opendap/data/nc*
Would produce a resource ID of */data/nc* Unless the BES actually
contains a catalog named *data* then a catalog request for the resource
ID */data/nc* will be routed to the default catalog.

##### Make the top level catalog include all of the catalogs

When a request is made for the default top-level catalog:

<bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">
`  `<bes:setContext name="errors">`xml`</bes:setContext>
`  `<bes:showCatalog node="/" />
</bes:request>

The BES will return a catalog containing an merging of the catalogs by
name (as catalog *nodes*) and a listing of the top-level of the default
catalog. For example if the BES has the *rdh*, *cedar*, and *jgofs*
catalogs installed, and the default catalog contains data sets, regular
files, and nodes the response might look like this:

<response ns="http://xml.opendap.org/ns/bes/1.0#" reqID="some_unique_value">
`   `<showCatalog>
`       <dataset `<font color=red>`catalog="catalog" (What would this be in this example?)`</font>`  count="13" lastModified="2009-03-24T19:02:46" name="/" node="true" size="578">`
`           `<dataset catalog="rdh" lastModified="2003-09-11T16:27:59" name="rdh" node="true" size="-1" />
`           `<dataset catalog="jgofs" lastModified="2001-05-29T23:32:04" name="jgofs" node="true" size="-1" />
`           `<dataset catalog="cedar" lastModified="2017 -12-30T21:47:58" name="cedar" node="true" size="-1" />

`           `<dataset catalog="catalog" lastModified="2008-11-19T20:56:15" name="Test" node="true" size="918" />
`           `<dataset catalog="catalog" lastModified="2008-05-29T23:32:34" name="Trunk Monkey" node="true" size="238" />
`           `<dataset catalog="catalog" lastModified="2008-05-29T23:31:56" name="bears.nc" node="false" size="852">
`               `<serviceRef>`dap`</serviceRef>
`           `</dataset>
`           `<dataset catalog="catalog" lastModified="2008-09-11T16:27:59" name="coverage" node="true" size="102" />
`           `<dataset catalog="catalog" lastModified="2009-01-30T20:10:16" name="data" node="true" size="510" />
`           `<dataset catalog="catalog" lastModified="2009-03-25T17:58:54" name="fanfang" node="true" size="306" />
`           `<dataset catalog="catalog" lastModified="2009-03-24T19:01:22" name="fanfang.tar.gz" node="false" size="9055684" />
`           `<dataset catalog="catalog" lastModified="2008-12-05T19:23:30" name="namespaceAttributesTest.nc" node="false" size="3396">
`               `<serviceRef>`dap`</serviceRef>
`           `</dataset>
`           `<dataset catalog="catalog" lastModified="2009-02-12T21:25:11" name="netcdf" node="true" size="136" />
`           `<dataset catalog="catalog" lastModified="2008-09-17T21:45:19" name="robots.txt" node="false" size="25" />
`           `<dataset catalog="catalog" lastModified="2008-05-29T23:32:04" name="sst" node="true" size="68" />
`           `<dataset catalog="catalog" lastModified="2008-12-30T21:47:58" name="thredds" node="true" size="68" />
`           `<dataset catalog="catalog" lastModified="2008-05-29T23:32:04" name="wcs" node="true" size="68" />
`       `</dataset>
`   `</showCatalog>
</response>

If there is a conflict between catalog name(s) and holdings in the
default catalog **catalog** the offending catalog name(s) can be
remapped using the BES configuration.

**GAH!**

**I still think that this is totally busted and I don't see how to fix
it with out simply rewriting the OLFS to correctly work with the
different catalogs in the BES which WILL change all of the URL's. The
fact is that we (well ok, I) didn't get it right. And this is a pretty
intractable problem otherwise. Fixing it in one of these half-baked ways
just begs for the whole thing to come crashing down around us, not to
mention the burden of creating an even MORE COMPLEX install and
configuration procedure for our users. Maybe one of you can see a way
out of this box... [ndp](User:Ndp "wikilink") 13:35, 7 May 2009 (PDT)**

### Patrick seez

#### In the BES

instead of looking for the catalog as part of the node name, look for an
attribute of the showCatalog element called catalog. If not present and
( the node attribute is not present or is empty or is slash ) then I
return the list of catalogs.

If not present and the node is present and the node is not empty and the
node is not slash then I default the catalog name to the default catalog
specified in the BES configuration file.

If present then I use that catalog name.

In the response for <showCatalog /> I give you the list of the catalogs
where the default catalog is marked with default="true".

Or perhaps there's a new command <showCatalogs /> that gives the list of
catalogs?

#### In the OLFS

When the OLFS starts up make a <showCatalog /> request to get the list
of catalogs.

When someone browses to www.example.com/opendap/ then you show them the
list of catalogs.

When someone browses to www.example.com/opendap/catalogname then you
show them the root node of that catalog

When someone browses to www.example.com/opendap/data/nc/ (no catalog
name is present) then this is an old URL, you pass this off to the BES
without a catalog name, and the BES will use the default catalog.

When someone browses to www.example.com/opendap/data/nc/fnoc1.nc.das (no
catalog name is present) then, again this is an old URL. Given the list
of catalogs in the beginning you use the default catalog name.

How can you tell if the catalog name is not present ... when the OLFS
started up you asked for the list of catalogs. If data is not the name
of a catalog then you know we are using the default catalog.

Or ... if possible ... redirect the old URLs to one with the catalog
name. Not sure if you can do this. So, if someone browses to
www.example.com/opendap/data/nc then you redirect that to
www.example.com/opendap/catalog/data/nc, and the same for
www.example.com/opendap/data/nc/fnoc1.nc.das you redirect to
www.example.com/opendap/catalog/data/nc/fnoc1.nc

What do you think?

I don't like the idea of when asked for the root node, if no catalog is
specified then I give you the root for the default catalog and the names
of the other catalogs. I think that if you ask for no catalog and no
node then you should get the list of catalogs, including the default.

<showCatalog />
` `<response ns="http://xml.opendap.org/ns/bes/1.0#" reqID="some_unique_value">
`   `<showCatalog>
`       `<dataset catalog="root"  count="4" lastModified="2009-03-24T19:02:46" name="/" node="true" size="-1">
`           `<dataset catalog="catalog" lastModified="2003-09-11T16:27:59" name="catalog" node="true" size="-1" default="true" />
`           `<dataset catalog="rdh" lastModified="2003-09-11T16:27:59" name="rdh" node="true" size="-1" />
`           `<dataset catalog="jgofs" lastModified="2001-05-29T23:32:04" name="jgofs" node="true" size="-1" />
`           `<dataset catalog="cedar" lastModified="2017 -12-30T21:47:58" name="cedar" node="true" size="-1" />
`      `</dataset>
`   `</showCatalog>
</response>

### Comments

Why does this matter? Do we need to use multiple catalogs for something?
[jimg](User:Jimg "wikilink") 10:43, 12 August 2009 (PDT)


Yes. As we grow Hyrax we will need to support additional catalogs and
services. Because both the BES and the OLFS can independently (and as
part of their regular usage pattern) modify the catalog by adding either
services which are connected to a URL or catalog content that is mapped
into the URL space.

<!-- -->


I guess one way to think of this is that the catalog for a particular
instance of Hyrax is the collection of all URLs that are valid. (Where
valid means that they return an HTTP status of 200.) Can't we consider
the collection of URL stings to represent a tree? Doesn't it define a
collection of named nodes? Some which are leaves and some which contain
more stuff?

--[ndp](User:Ndp "wikilink") 09:21, 7 March 2010 (PST)

[Patrick](User:pwest "wikilink")

[Fixing the Broken Hyrax Catalogs](Category:Development "wikilink")
[Fixing the Broken Hyrax
Catalogs](Category:Hyrax_Development "wikilink")