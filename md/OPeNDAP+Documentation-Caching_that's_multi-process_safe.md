## Problem

Develop a general procedure (or software) for implementing a cache that
can be shared by several processes. There seems to be no readily
available library that implements caching. It certainly needs to work
for Unix; Windows suport would be good, but is of less importance
because the OC library may take over or client role on that platform.

## Background

We have several cases where files need to be cached, both in our server
software and in the client code. These include at least: files that have
been decompressed and responses from the web that can be cached using
the HTTP/1.1 rules. We need for the caching to be both thread safe and
multi-process safe. That is, the cache needs to be shared between
processes that do not, otherwise, synchronize their operation.

Information:

- <http://stackoverflow.com/questions/1599459/optimal-lock-file-method>
- <http://perl.plover.com/yak/flock/>
- see open(2) using O_CREAT \| O_RDWR \| O_EXCEL (atomically creates a
  file or returns an error if it exists)

## Proposed solution

### Assumptions

1.  The consistency of objects in the cache is most important
2.  We do not want to lock the entire cache
3.  The cache must work with multiple processes
4.  we can tolerate some errors when computing the size of the cache
    (objects and their size, in the cache)
5.  The BES cache for compressed data will be one directory
6.  It should use some sort if LRU or similar scheme

### Definitions

shared lock: a synonym for a read-only lock; a program cannot acquire an exclusive lock when a shared lock exists, but it can acuire another shared lock. Thus, many programs can use a shared lock for reading and prevent a program from writing/deleting the file.
exclusive lock: synonym for a write lock; a program cannot acquire an exclusive lock when either a shared lock or another exclusive lock already exists.
semaphore file: a file that is used to serve as a proxy lock for another file. These can be use to indicate to cooperating processes that a file should not be touched - it is effectively locked - without actually locking the file. This can be used to establish overlapping locks, particularly so that files can be first exclusively locked and, without removing that lock, have a shared lock too. Once the shared (i.e., read) lock is in place, the 'exclusive lock' can be removed, thus ensuring that at no time in the sequence is the file left in the unprotected state.

### Releasing locks

The *[fcntl](http://linux.die.net/man/2/fcntl)* command makes locks that
are removed whenever *any* of the file descriptors to an open file are
closed. So long as we use advisory locking and program logic follows the
rules, we can use these locks and be sure that files will be unlocked
whenever a handler closes the file, without any need to actually close
the lock in the using the same file descriptor used to obtain the lock.
This is apparently not the case for
[flock](http://linux.die.net/man/2/flock) or
[lockf](http://linux.die.net/man/3/lockf). Both the BES and the HTTP
caching code in libdap have support for releasing resources, so it's
possible to use flock(2), et c., for this which also means we can use
the open(2) and creat(2) calls for lock management.

### Operations the cache must provide

1.  Create and lock a file so that data cam be decompressed
    - Determine the cache size
    - If it's too big, purge to 90% of max size
2.  Get a shared lock to decompressed data

### Cache design

In addition to the files themselves, maintain information about the time
each file was last used and their size. The cache should also probably
have information about its total size. Store this in the cache in files,
so that it's accessible for the processes accessing the cache.

The cache will be a directory.

Each file to be cached with have a name that is a function of its
pathname, so it is guaranteed to be unique. The BES decomp cache will
use the 'take the pathname and replace the slashes with hashes'
technique to make the names unique. Example:

`/usr/local/data/modis.hdf --> #usr#local#data#modis#hdf`

The leading hash can be used or not, but it's probably easier to include
it. This is a pretty easy transform; simply replace the characters
\[/.\] with \#

It would be best if the size and last use time for each file in the
cache was not recorded in a single file.

How to purge:

1.  Look at every data file
2.  Store the basename (i.e., data file name), size and last used
    (access) time
3.  Find the oldest files and remove until the total size of the cache
    falls below 90% of the max size.
    - if a file is locked when the cache tries to delete it, just skip
      it, since its access is clearly just become recent!
4.  with every delete, update the 'total size' file

How to track the total size of the cache's data:

1.  maintain a single 'global' cache control file
2.  store the total size there.
3.  for every file created, update that size
4.  for every file deleted, update that size
5.  use an exclusive rd/wr lock for those updates
6.  Note: *This is a compromise given our original goal of not ever
    locking the whole cace because it technically locks the entire cache
    for write operations, but does not lock it for read operations.
    Also, the time the file is locked can be much smaller than the time
    required to actually write one of the data files.*

##### Software we have

In the BES software, the class *BESContainer* provides a partially
abstract base class that holds two methods: *access()* and *release()*
that are used to access and release the 'container' that holds the data.
The class *BESFileContainer* has concrete implementations for these
methods that uncompress files if need be. The class
*BESUncompressManager* performs the decompression operation and uses the
*BESCache* class to store the result. BESCache handles the locking
operations.

To avoid making a new BESCache object on every call to
BESContainer::access(), maybe move BESCache into BESUncompressManager.