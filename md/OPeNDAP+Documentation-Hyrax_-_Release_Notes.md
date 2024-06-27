# [Release 1.4.0 27 June 2008](http://www.opendap.org/downloads/olfs/html#1.4.0)

This release contains:

- **[Bug fixes and feature
  additions](http://scm.opendap.org:8090/trac/query?status=new&status=assigned&status=reopened&status=closed&milestone=Hyrax+1.4&order=id)**
- Significant speed improvements for large data transfers (Details
  [Here](http://scm.opendap.org:8090/trac/wiki/BES_Chunking) and
  [Here](http://scm.opendap.org:8090/trac/wiki/Chunking_Performance).)
- libdap namespace added for all libdap classes and code
- Symbolic link control in the BES (Details
  [here](http://scm.opendap.org:8090/trac/wiki/BES_Symbolic_Links))
- Tested and approved for Tomcat 1.6.x
- Improved Error Messaging.
- XSLT based directory and error pages allow site administrators to
  customize Hyrax for visual integration with existing sites. ([More
  Here](http://docs.opendap.org/index.php/Hyrax_-_Customizing_Hyrax))
- Corrected misspelled parameter in BES configuration file
  MaximumHeapSize (was MaximunHeapSize). If you have an older
  installation of the BES and are keeping the BES configuration file,
  you will need to correct this parameter name for the BES to start.




# [Release 1.3.1 December 10, 2007](http://www.opendap.org/downloads/olfs/html#1.3.1)

The 1.3.1 release is a bug fix release:

- The memory footprint has been reduced to address issues on small or
  medium sized machines.
- Binaries installed from the RPM packages now store run-time files
  where most system administrators expect them to be stored.

# [Release 1.3.0 November 14, 2007](http://www.opendap.org/downloads/olfs/html#1.3.0)

This release includes:

- **[Bug
  Fixes](http://scm.opendap.org:8090/trac/query?status=closed&milestone=Hyrax+1.3)**
- **Hyrax control script** - Simplifies starting and stopping Hyrax when
  running the BES and the OLFS on the same machine.
- **Compression support**: Unix compress (.Z) is now supported.
- **Signed distributions**: Our software distributions are now signed
  using a public/private key pair.

See [Public Key](http://www.opendap.org/download/index.html#public_key)
for more information.

# [Release 1.2.1 April 27, 2007](http://www.opendap.org/downloads/olfs/html#1.2.1)

- **Improved support for compressed files**: The support for compressed
  data files has been completely revamped to be more efficient and
  safer. The cache used for the decompressed files has also be
  completely re-implemented.
- Bug fixes
- These fixes address important security issues which were found in the
  BES software. Sites running Hyrax should upgrade as soon as practical.

Updated (21 May) the BES and OLFS (to versions 3.5.1 and 1.2.3, resp.)
to include a fix for a transmission problem that affects some large 32
bit floating point arrays.

# [Release 1.2.0 April 23, 2007](http://scm.opendap.org:8090/trac/milestone/Hyrax%201.2)

Major changes made to the OLFS allow:

1\. Support for multiple BES's: Data providers may configure a single
Hyrax installation to work with multiple BES's.

2\. The OLFS dispatch code has been refactored to allow 3rd party
dispatch handlers to be added simply by modifying the configuration and
adding the java class files to the Tomcat library directory.

# [Release 1.1 March 15, 2007](http://scm.opendap.org:8090/trac/milestone/Hyrax%20Version%201.1%2C%20formal%20release)

First full public release!

All known bugs have been fixed. **Hyrax is ready!**

[Here's the stuff we did for this
milestone.](http://scm.opendap.org:8090/trac/query?status=new&status=assigned&status=reopened&status=closed&group=owner&milestone=Hyrax+Version+1.1%2C+formal+release&order=priority)

# [Release beta-1.0.2 January 02, 2007](http://scm.opendap.org:8090/trac/milestone/Hyrax%20Release%20beta%201.0.2)

2nd beta release. As of this release the name is changed from Server4 to
Hyrax.

This release features:

- Bug fixes for tickets
  [767](http://scm.opendap.org:8090/trac/ticket/767),
  [784](http://scm.opendap.org:8090/trac/ticket/784),
  [789](http://scm.opendap.org:8090/trac/ticket/789), and
  [793](http://scm.opendap.org:8090/trac/ticket/793).
- Partial logging implementation (Ticket
  [785](http://scm.opendap.org:8090/trac/ticket/785))
- Improved documentation regarding shared libraries. (Ticket
  [780](http://scm.opendap.org:8090/trac/ticket/780))
- The BES and and the data handlers come with test data and will work
  and serve data immediately after an install using the default
  configuration. (Ticket
  [764](http://scm.opendap.org:8090/trac/ticket/764))

# [Release beta-1.4 - January 02, 2007](http://scm.opendap.org:8090/trac/milestone/Server%204,%20release%201.4)

1st beta release.

This release adds support for:

- HTTP/1.1 persistent connections
- Improved OPeNDAP directory response.
- [Prototype SOAP
  Interface.](http://rsg.opendap.org:8090/server-4/templates/soapAPI.html)
- Server UUID on top level directory for improved web searchability
- Persistent content (configurations, user supplied documents/web pages,
  etc.)
- Improved internal efficency via BES client connection pooling.

# [Release 1.3 - September 26, 2006](http://scm.opendap.org:8090/trac/milestone/Server%204,%20release%201.3)

This was an internal release.

This release adds support for:

- HTTP/1.1 caching - Support for conditional GET using
  *If-Modified-Since* and *If-Unmodified-Since* headers.
- Persistent Configurations - Now local configurations will not be
  destroyed when installing new versions of the software. Configurations
  are stored in the *\$CATALINA_HOME/content/opendap* directory.
- Advanced THREDDS catalogs - These require thoughtful modification of
  the catalog.xml file located in the persistent content/configuration
  directory.

# [Release 1.2 - February 24, 2006](http://scm.opendap.org:8090/trac/milestone/Server%204,%20release%201.2)

This was an internal release.

This release adds simple THREDDS catalogs to the server. No special
configuration is required, simply add /catalog.xml to the end of a URL
that references a "collection" of data sets and a THREDDS catalog will
be returned.

# [Release 1.1 - February 17, 2006](http://scm.opendap.org:8090/trac/milestone/Server%204,%20release%201.1)

This was an internal release.

Represents the first prototype release of Hyrax. It implements the
feature set for releases 1.0 and 1.1, along with a smattering of
features due in coming releases. In particular prototype implementations
exist for F-23 and F-24 from release 1.2. At the time of this writing
the intended feature set is still growing.

# [Release 1.0 - January 15, 2006](http://scm.opendap.org:8090/trac/milestone/Server%204,%20release%201.0)

This was an internal release.