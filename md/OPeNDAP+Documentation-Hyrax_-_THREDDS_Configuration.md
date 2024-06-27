## Overview

Hyrax now uses its own implementation of the THREDDS catalog services
and supports most of the THREDDS catalog service stack. The
implementation relies on two DispatchHandlers in the OLFS and utilizes
XSLT to provide HTML versions (presentation views) for human
consumption.

1.  Dynamic THREDDS catalogs for holdings provided by the BES are
    provided by the opendap.bes.BESThreddsDispatchHandler.
2.  Static THREDDS catalogs are provided by the
    opendap.threddsHandler.StaticCatalogDispatch. The static catalogs
    allow catalog "graphs" to be decoupled from the filesystem "graph"
    of the data holdings, thus allowing data providers the ability to
    present and organize data collections independently of how they are
    organized in the underlying filesystem.

Static THREDDS catalogs are "rooted" in a master catalog file,
*catalog.xml*, located in the (persistent) content directory for the
OLFS (Typically \$CATALINA_HOME/content/opendap). The default
*catalog.xml* that comes with Hyrax contains a simple catalogRef element
that points to the dynamic THREDDS catalogs generated from the BES
holdings. The default catalog example also contains a (commented out)
datasetScan element that provides (if enabled) a simple demonstration of
the datasetScan capabilities. Additional catalog components may be added
to the *catalog.xml* file to build (potentially large) static catalogs.

- THREDDS datasetScan elements are now fully supported and can be used
  as a tool for altering the catalog presentation of any part of the BES
  catalog. These alterations include (but are not limited too) renaming,
  auto proxy generation, filtering, and metadata injection.

[More details about the handlers, their configuration options, and other
information can be found here.](THREDDS_using_XSLT "wikilink")

### THREDDS Catalog Documentation

Rather than provide an exhaustive explanation of the THREDDS catalog
functionality and configuration I will appeal to the existing documents
provided by our fine colleagues at
[UNIDATA](http://www.unidata.ucar.edu/projects/THREDDS/):

- [Catalog
  Basics](http://www.unidata.ucar.edu/projects/THREDDS/tech/TDS.html#Catalogs)
- [Client Catalog
  Specification](http://www.unidata.ucar.edu/projects/THREDDS/tech/catalog/InvCatalogSpec.html)
  Describes what THREDDS catalog components should be produced by
  servers.
- [Catalog
  Primer](http://www.unidata.ucar.edu/software/thredds/current/tds/tutorial/CatalogPrimer.html)
- [Server catalog
  specification](http://www.unidata.ucar.edu/software/thredds/v4.6/tds/catalog/InvCatalogServerSpec.html#datasetScan_Element)
  can help you understand the rules for constructing proper datasetScan
  elements in your catalog.
- [*dataset*
  Element](http://www.unidata.ucar.edu/projects/THREDDS/tech/catalog/InvCatalogSpec.html#dataset)
- [*datasetScan*
  configuration](http://www.unidata.ucar.edu/software/thredds/v4.6/tds/reference/DatasetScan.html)
  (applies to Hyrax as well).

------------------------------------------------------------------------

## Configuration Instructions

- The current default
  ([olfs.xml](Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File "wikilink"))
  file comes with THREDDS configured correctly.
- The THREDDS master catalog is stored in the file
  **\$CATALINA_HOME/content/opendap/catalog.xml** it can be edited to
  provide additional static catalog access.

## datasetScan Support

The **datasetScan** element is a powerful tool that can be used to
sculpt the catalog's presentation of the BES catalog content. The Hyrax
implementation has a couple of key points that need to be considered
when developing an instance of the **datasetScan** element in your
THREDDS catalog.

### location attribute

The **location** attribute specifies the place in the BES catalog graph
that the **datasetScan** will be rooted. This value *must be* expressed
relative to the BES catalog root (BES.Catalog.catalog.RootDirectory) and
not in terms of the underlying BES host file system.

Example
If *BES.Catalog.catalog.RootDirectory=/usr/share/hyrax* and the data
directory to which you wish to apply the **datasetScan** is (in
filesystem terms) located at */Users/share/hyrax/data/nc* then the
associated **datasetScan** element's **location** attribute would have a
value of */data/nc*

<div  style="padding-left: 20px;width: 95%;">

``` xml
<datasetScan name="DatasetScanExample" path="hyrax" location="/data/nc">
```

</div>

### name attribute

The **name** attribute specifies name that will be used to in the
presentation (HTML) view for the catalog containing the **datasetScan**
is viewed.

### path attribute

The **path** attribute specifies the place in the THREDDS catalog graph
that the **datasetScan** will be rooted. In effect it is a relative URL
for the service. If **path** begins with a "/" then it is an absolute
path - rooted at the server and port of the web server. The values of
the **path** attribute should NEVER contain "catalog.xml" or
"catalog.html". The service will create these endpoints dynamically.

Relative path example
Consider a catalog accessed with the URL:
<http://localhost:8080/opendap/thredds/v27/Landsat/catalog.xml> and that
contains this **datasetScan** element:

<div  style="padding-left: 20px;width: 95%;">

<datasetScan name="DatasetScanExample" path="hyrax" location="/data/nc" />

</source>
</div>


In the client catalog the **datasetScan** becomes this **catalogRef**
element:

<div  style="padding-left: 20px;width: 95%;">

``` xml
<thredds:catalogRef
    name="DatasetScanExample"
    xlink:title="DatasetScanExample"
    xlink:href="hyrax/catalog.xml"
    xlink:type="simple"
/>
```

</div>


And the top of **datasetScan** catalog graph will be found at the URL


<http://localhost:8080/opendap/thredds/v27/Landsat/hyrax/catalog.xml>

<!-- -->

Absolute path examples
Consider a catalog accessed with the URL:
<http://localhost:8080/opendap/thredds/v27/Landsat/catalog.xml> and that
contains this **datasetScan** element:

<div  style="padding-left: 20px;width: 95%;">

``` xml
<datasetScan name="DatasetScanExample" path="/hyrax" location="/data/nc" />
```

</div>


In the client catalog the **datasetScan** becomes this **catalogRef**
element:

<div  style="padding-left: 20px;width: 95%;">

``` xml
<thredds:catalogRef
     name="DatasetScanExample"
     xlink:title="DatasetScanExample"
     xlink:href="/hyrax/catalog.xml"
     xlink:type="simple"
/>
```

</div>


Then the top of **datasetScan** catalog graph will be found at the URL


<http://localhost:8080/hyrax/catalog.xml>

***Which is probably not what you want!*** This **catalogRef** directs
the catalog crawler away from the Hyrax THREDDS service and to an
undefined (as far as Hyrax is concerned) endpoint, one that most likely
will generate a 404 (Not Found) response from the Web Server.

<!-- -->


When using absolute paths you must be sure to prefix the path with the
Hyrax THREDDS service path or you will direct the clients away from the
service. In these examples the Hyrax THREDDS service path would be
*/opendap/thredds/" (look at the URLs in the above examples) If we
change the **datasetScan** path attribute value
to*/opendap/thredds/myDatasetScan'':

<div  style="padding-left: 20px;width: 95%;">

``` xml
<datasetScan name="DatasetScanExample" path="'/opendap/thredds/myDatasetScan" location="/data/nc" />
```

</div>


In the client catalog the **datasetScan** becomes this **catalogRef**
element:

<div  style="padding-left: 20px;width: 95%;">

``` xml
<thredds:catalogRef
    name="DatasetScanExample"
    xlink:title="DatasetScanExample"
    xlink:href="/opendap/thredds/myDatasetScan/catalog.xml"
    xlink:type="simple"
/>
```

</div>


Now the top of **datasetScan** catalog graph will be found at the URL


<http://localhost:8080/opendap/thredds/myDatasetScan/catalog.xml>

which keeps the URL referencing the Hyrax THREDDS service and not some
other part of the web service stack.

### useHyraxServices attribute

The Hyrax version of the **datasetScan** element employs the extra
attribute **useHyraxServices**. This allows the **datasetScan** to
automatically generate Hyrax data services definitions and access links
for datasets in the catalog. The **datasetScan** can be used to augment
the list of services (when **useHyraxServices** is set to true) or it
can be used to completely replace the Hyrax service stack (when
**useHyraxServices** is set to false).

- If no services are referenced in the **datasetScan** and
  **useHyraxServices** is set to true, then Hyrax will provide catalogs
  with service definitions and access elements for all the datasets that
  the BES identifies as data.
- If no services are referenced in the **datasetScan** and
  **useHyraxServices** is set to false, then the catalogs generated by
  the **datasetScan** will have *no service definitions or access
  elements*.

By default **useHyraxServices** is set to true.

### Functions

[DatasetScan allows you to apply the following functions to the names of
the datasets in the datasetScan catalog
graph.](http://www.unidata.ucar.edu/software/thredds/v4.6/tds/reference/DatasetScan.html)

#### Filter


A datasetScan element can specify which files and directories it will
include with a filter element (also [see THREDDS server catalog
spec](http://www.unidata.ucar.edu/software/thredds/v4.6/tds/catalog/InvCatalogServerSpec.html)
for details). The filter element allows users to specify which datasets
are to be included in the generated catalogs. A filter element can
contain any number of include and exclude elements. Each include or
exclude element may contain either a wildcard or a regExp attribute. If
the given wildcard pattern or regular expression matches a dataset name,
that dataset is included or excluded as specified. By default, includes
and excludes apply only to atomic datasets (regular files). You can
specify that they apply to atomic and/or collection datasets
(directories) by using the atomic and collection attributes.

<div  style="padding-left: 20px;width: 95%;">

``` xml
<filter>
    <exclude wildcard="*not_currently_supported" />
    <include regExp="/data/h5/dir2" collection="true" />
</filter>
```

</div>

#### Sort


Datasets at each collection level are listed in ascending order by name.
With a sort element you can specify that they are to be sorted in
reverse order:

<div  style="padding-left: 20px;width: 95%;">

``` xml
<sort>
    <lexigraphicByName increasing="false" />
</sort>
```

</div>

#### Namer


If no namer element is specified, all datasets are named with the
corresponding BES catalog dataset name. By adding a namer element, you
can specify more human readable dataset names.

<div  style="padding-left: 20px;width: 95%;">

``` xml
<namer>
    <regExpOnName regExp="/data/he/dir1" replaceString="AVHRR" />
    <regExpOnName regExp="(.*)\.h5" replaceString="$1.hdf5" />
    <regExpOnName regExp="(.*)\.he5" replaceString="$1.hdf5_eos" />
    <regExpOnName regExp="(.*)\.nc" replaceString="$1.netcdf" />
</namer>
```

</div>

#### addTimeCoverage


A datasetScan element may contain an addTimeCoverage element. The
addTimeCoverage element indicates that a timeCoverage metadata element
should be added to each dataset in the collection and describes how to
determine the time coverage for each dataset in the collection.

<div  style="padding-left: 20px;width: 95%;">

``` xml
<addTimeCoverage
    datasetNameMatchPattern="([0-9]{4})([0-9]{2})([0-9]{2})([0-9]{2})_gfs_211.nc$"
    startTimeSubstitutionPattern="$1-$2-$3T$4:00:00"
    duration="60 hours"
/>
```

</div>


for the dataset named **2005071812_gfs_211.nc**, results in the
following timeCoverage element:

<div  style="padding-left: 20px;width: 95%;">

``` xml
 <timeCoverage>
    <start>2005-07-18T12:00:00</start>
    <duration>60 hours</duration>
  </timeCoverage>
```

</div>

#### addProxies


For real-time data you may want to have a special link that points to
the "latest" data in the collection. Here, latest is simply means the
last filename in a list sorted by name, so its only the latest if the
time stamp is in the filename and the name sorts correctly by time.

<div  style="padding-left: 20px;width: 95%;">

``` xml
<addProxies>
    <simpleLatest name="simpleLatest" />
    <latestComplete name="latestComplete" lastModifiedLimit="60.0" />
</addProxies>
```

</div>