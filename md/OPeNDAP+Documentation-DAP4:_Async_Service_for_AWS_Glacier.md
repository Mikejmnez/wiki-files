# Background

As part of the DAP4 specification we have defined a mechanism through
which clients and servers can negotiate access to data resources that
are "near-line". These data resources may be held in a tape drive system
or some other system that takes an intermediate amount of time (a few
hours to a few days) to extract a stored resource. The Amazon Web
Services Glacier system is an excellent example of such a near-line
system and we started developing a prototype service to provide DAP4
client access to Glacier holdings.

# Overview

Here are some Glacier axioms (things which we hold to be true, even if
they make life hard):

1.  The Glacier service is organized into "vaults" (a named container
    similar to the AWS S3 bucket) into which things (data resources, or
    in Glacier speak "archives" which are similar to the AWS S3
    "object"s) are put
2.  The Glacier service typically takes about 4 hours to respond to a
    request for an "archive".
3.  The Glacier service builds an "inventory" of each vault's contents
    every 24 hours.
4.  Requests for the current vault inventory also take 4 hours to
    complete.
5.  When an archive is uploaded to Glacier the name (aka archive-id)
    under which it is stored is determined by Glacier and returned when
    the upload is finished. So if you ever want to get that archive back
    you either have to remember the archive-id locally or be prepared to
    find it in the not so easy to get inventory.
6.  When you upload an archive you can provide a description of it. The
    AWS documentation suggests that you use your own resourceId (for
    example a local path/file name string) so that if you do need to
    rebuild your local representation of the archive you can do so from
    a Glacier inventory (which contains both the archive-ids and the
    descriptions).
7.  Glacier will happily allow you to upload the same resource over and
    over again. Each time you upload the resource you will get a new
    archive-id for it. The previously uploaded copies *will remain in
    the vault associated with their previously assigned archive-ids*
    unless you actively delete them.
8.  Archives can be deleted.
9.  Vaults can be deleted.
10. Uploading archives, deleting archives, and deleting vault operations
    do not require asynchronous behavior on the part of the client. In
    other words these operations happen in "real time".
11. Retrieving archives and vault inventories require asynchronous
    behavior capabilities on the part of the client

(And the Real Numbers are defined with only 16 axioms)

# Design Issues

In order to utilize Glacier as part of a DAP4 service several things
need to be in place: A catalog system, some kind of database that
associates glacier holdings with resources identified in the catalog,
and a system for holding retrieved glacier archives/resources for use by
the service.

## Catalog

catalog
A directed graph that embodies a hierarchical representation of a
collection of data resources.

Some abstract representation of the Glacier holdings will be required.
While catalogs are not part of the DAP4 spec we know that in general DAP
servers return catalogs in the form of THREDDS catalogs or as HTML pages
that represent catalog nodes and that contain links to data resources
and other catalog nodes. While a Glacier vault might simply be
represented by a list of archive-ids this is not a representation that
users of data resources would find very useful, especially when coupled
with the delay in discovering anything about any single resource.

The catalog should be close to the server and quickly accessible.

## Archive Records

ArchiveId
The string which is returned by Glacier to identify an uploaded archive
and through which said archive may be retrieved from Glacier. These
appear to be GUIDs. Example:
*XIfGXl0qWkfDVbKL3Ibb6XQ8OBC44YwOBGl9ds-yA18HN8zDOBi1A2ZugWprFHgwL-8LRQLSfz8BqoJLaMdaflPgM-J-_D0gHQwS_CDHXp5R0vHUADuZKd49oP4eir2qw508EJ57kQ*

ResourceId
A string that is used by a DAP server to identify a particular (data)
resource. Typically a path/file name, but in practice this would appear
as a local path fragment in a DAP URL after the domain-port-service part
of the URL. For example in the url
http://test.opendap.org:8080/opendap/hyrax/data/nc/fnoc1.nc the
**resourceId** would be *data/nc/fnoc1.nc*

Archive Record
A record (as in a file or database entry) that contains at minimum a
resource's **resourceId** and **archiveId**

If all that was required was to associate the resourceId and archiveId
it might seem reasonable to just include this small set of information
in the persisted catalog for the Glacier vault. However because
retrieving anything from Glacier costs time (and money) it is probably
important to capture some kind of information about the each archived
resource locally and in such a way that users can inspect resource
metadata without requesting that the archived resource be retrieved just
to find out that the resource is of little or no interest.

Suggestion: At minimum store the dap4:DMR (or dap2:DDX) document in the
local Archive Record. Consider also storing the names and values of any
MAP arrays found in the dataset. Maybe use the XML data response to
generate this content as part of the uploading to Glacier process.

Example: [Glacier Archive Record](Glacier_Archive_Record "wikilink")

## Resource Cache

Resources that have been retrieved from Glacier must be put somewhere so
that the DAP (and possibly other) services can utilize them. I call this
a cache because I think it's implied that the volume of things in a
Glacier vault may be much larger than any reasonable machine running
this service may be able to hold. Therefore:

1.  The retrieved Glacier resources must be cached.
2.  Cached resources should be retrievable by utilizing their
    resourceId.
3.  The cache must have a size limit.
4.  There must be software that manages the cache contents by applying a
    metric to determine which resources should be purged from the cache
    and when.

# Implementation (Partial + Ideas)

All access to Glacier must be authenticated and this argues for using
one of Amazon's APIs instead of 'direct' access with HTTP GET, PUT, etc.
Amazon provides Java and Python APIs (as well as some others) but
notably not C/C++.

I have written about half of a prototype for this using the OLFS and the
local file system. I create a directory for each vault and underneath
this are 3 subdirectories: cache, index, archive.

## cache directory

This is where I plan to store the resources retrieved from glacier. The
intention is to store the retrieved resource content in a file whose
name is the file system compatibility encoded resourceId. This way there
is only a single directory to look in and when a request for a resource
is being handled by the server the server only has to encoded the
resourceId and check here to see if it's already got the thing.

An attractive alternative to this would be to use S3 bucket as the
cache:

- No encoding the resourceId's
- Really big :)
- The BES already knows how to read things from S3 via HTTP and the
  gateway.
- The OLFS could handle managing the Glacier and S3 interactions and
  only forward DAP requests to the BES once the resource was available
  in S3.

## index directory

(*Note: Index and catalog may be synonymous here.*)

The index directory is where I am keeping the index/catalog files. I am
using as the index scheme developed by Jeff Orgata at NOAA NODC. I think
it's probably important for for server performance for the index to be
kept 'close by' - as in a locally mounted filesystem. (In an AWS
instance I could see using an EBS volume). The index files are saved to
the index directory using their file system encoded resourceId as the
filename.

Thoughts:

- Keeping all of the index files in a single directory with no other
  content allows the server to ingest the entire index by simply
  grabbing all the files in the directory. This can useful if the entire
  index is to held in a memory cache which may or may not unreasonable
  depending on the size on the index and the server resources that may
  be available. Certainly memory caching the index would increase
  traversal performance.

<!-- -->

- If other files are intermingled in the directory with the index files
  then a mass ingest is not possible and the top level index must be
  loaded and the graph traversed in order to load the index. If the
  index has multiple trees/roots then there would necessarily need to be
  multiple starting points for the traversal.

<!-- -->

- Saving the index files using their encoded resourceIds allows the
  server to lazy evaluate the catalog, simply loading each index as
  requested. The ingested index can be held in some kind of managed
  memory cache or discarded if the index traversal performance is not an
  issue.

## archive directory

The archive directory is where I store the the archive records as
described above. As with the index and cache directories, these records
are stored in a file whose name is the file system compatibility encoded
resourceId. These files represent things that could/may/will end up in
the cache and the name they are stored under is *the same name as the
downloaded resource in the cache directory*. That would be intentional:
This way the server can use the resourceId to quickly determine if a
resource exists (by attempting to retrieve an archive record), and then
if it's already cached (by locating the cache file).

## Thoughts

I made a choice to separate the cache, index, and archive contents. I
could used an alternate scheme in which all these files get placed in
the same directory on the file system - the resourceId's for the index
and the data resources will be, be definition, different. The archive
and cache files could be combined into a single directory by using a
naming extension applied to the resourceId (ex: resourceId.cache &
resourceId.arcrec) . I separated them into their own directories to help
my own human eyes and brain, I think it's worth noting because by
combining all of these things into a single directory you would be using
the file systems as a convenient, heterogeneous, no-sql database. In
fact one might see some utility in replacing the use of the filesystem
here with a *real* no-sql database - but that's a story for another day.