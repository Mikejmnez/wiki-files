Building the BES and it's data handlers from source, or installing from
the Linux RPMs, will provide the default installation with data and a
valid configuration. This is suitable for testing. The following details
how you go about customizing it for your data.

## Location of the BES Configuation File

The BES configuration file is called *bes.conf* and can be found in
*\$prefix/etc/bes/* if you built the software from source or in
*/etc/bes/* if you used our RPM packages. By default *\$prefix* is
*/usr/local*.

## Basic format of parameters

Parameters set in the BES configuration file have the following format:

`Name=Value`

If you wish to add to the value of a parameter, then you would use +=
instead of =

`Name=Value1`
`Name+=Value2`

The above would return the values Value1 and Value2 in the software.

And if you would like to include another configuration file you would
use the following:

`BES.Include=/path/to/configuration/file/blee.conf`

The bes.conf file includes all .conf files in the modules directory with
the following:

`BES.Include=modules/.*\.conf$`

Note that regular expressions can be used in the Include parameter to
match a set of files.

## Administration & Logging

In the bes.conf configuration file, the *BES.ServerAdministrator*
parameter is the address used in various mail messages returned to
clients. Set this so that whoever gets the email will be able to fix
problems ad/or respond to user questions. Also set the log file and log
level. If the *BES.LogName* is set to a relative path it will be treated
as relative to the directory where the BES is started. That is, if the
BES is installed in /usr/local/bin but you start it in your home
directory using the parameter value below, the log file will be
`bes.log` in your home directory.

`BES.ServerAdministrator=webmaster@some.place.edu`
`BES.LogName=./bes.log`
`BES.LogVerbose=no`

Because the BES is a server in its own right, you will need to tell it
which network port and interface to use. Assuming you are running the
BES and OLFS (i.e., all of Hyrax) on one machine, here's what you should
do:

## User and Group parameters

In the bes.conf configuration file, the BES must be started as root. One
of the things that the BES does first is to start up a listener to
listen for requests to the BES. This listener is started as root, but
then the user and group of the process is set using parameters in the
BES configuration file.

`BES.User=user_name`
`BES.Group=group_name`

You can also set these to a user id and a group id. For example:

`BES.User=#172`
`BES.Group=#14`

## Setting the networking parameters

In the bes.conf configuration file, we have settings for how the BES
should listen for requests:

`BES.ServerPort=10022`
`# BES.ServerUnixSocket=/tmp/opendap.socket`

The *BES.ServerPort* tells the BES which TCP/IP port to use when
listening for commands. Unless you need to use a different port, use the
default. Ports with numbers less than 1024 are special, otherwise you
can use any number under 65536. That said, stick with the default unless
you know you need to change it.

In the default bes.conf file we have commented the *ServerUnixSocket*
parameter since doing so now disables I/O over that device. If you need
UNIX socket I/O, uncomment this line, otherwise leave it commented since
the fewer open network I/O ports, the easier it is to make sure the
server is secure.

If both *ServerPort* and *ServerUnixSocket* are defined, the BES listens
on both the TCP port and the Unix Socket. Local clients, on the same
machine as the BES, can use the unix socket for a faster connection.
Otherwise, clients on other machines will connect to the BES using the
*BES.ServerPort* value. Note that the OLFS always uses only the TCP
socket, even if the UNIX socket is present.

## Debugging Tip

In the bes.conf configuration file, use the *BES.ProcessManagerMethod*
parameter to control whether the BES acts like a normal Unix server or
not. The default value of `multiple` causes the BES to accept many
connections at once, like a typical server. The value `single` causes it
to accept a single connection, process the commands sent to it and exit,
greatly simplifying trouble shooting.

`BES.ProcessManagerMethod=multiple`

## Controlling how compressed files are treated

Compression parameters are configured in the bes.conf configuration
file.

The bz2, gz, and Z file compression methods are understood by the BES.
The BES will automatically recognize compressed files using the 'bz2',
'gzip' and Unix compress ('Z') compression schemes. However, you need to
configure the BES to accept those files types as valid data by making
sure that the file names are associated with a data handler. For
example, if you're serving netCDF files you would set
`BES.Catalog.catalog.TypeMatch` so that it includes
`nc:.*\.(nc|NC)(\.gz|\.bz2|\.Z)?$;` where the first part of the regular
expression matches the file name and the '.nc' extension and the second
part matches the suffix indicating the file is compressed (either '.gz',
'.bz2' or '.Z').

When the BES is asked to serve a file that has been compressed, it first
must decompress it and then pass it to the correct data handler (except
for those formats which support 'internal' compression such as HDF4).
The *BES.CacheDir* parameter tells the BES where to store the
uncompressed file. Note that the default value of /tmp is probably less
safe than a directory that's only used by the BES for just this purpose.
You might set this to <prefix>`/var/bes/cache`, for example.

The *BES.CachePrefix* parameter is used to set a prefix for the cached
files so that when a directory like /tmp is used, it's easy for the BES
to recognize which files are its responsibility.

The *BES.CacheSize* parameter sets the size of the cache in megabytes.
When the size of the cached files exceeds this value, the cache will be
purged using a least-recently-used (where the file's access time is the
'use time') approach. Because it's usually impossible to determine the
sizes of data files before decompressing them, there may be times when
the cache holds more data than this value. Ideally this value should be
several times the size of the largest file you plan to serve.

## Loading Software Modules

Virtually all of the BESs functions are contained in modules that are
loaded when the server starts up. Each module is a shared-object
library. The configuration for each of these modules is contained in its
own configuration file and is stored in a directory located in the same
directory as the bes.conf file called modules.
*\$prefix/etc/bes/modules/*

By default, all .conf files located in the modules are loaded by the BES
per this parameter in the bes.conf configuration file:

`BES.Include=modules/.*\.conf$`

So if you don't want one of the modules to be loaded, simply change it's
name to, say, nc.conf.sav and it won't be loaded.

For example, if you are installing the general purpose server module
(the dap-server module) then a dap-server.conf file will be installed in
the modules directory. Also, most installations will include the dap
module, allowing the BES to server OPeNDAP data. This configuration file
is also included in the modules directory and is called dap.conf. For a
data handler, say netcdf, there will be an nc.conf file located in the
modules directory.

Each module should contain within it a line that will tell the BES to
load the module at startup:

`BES.modules+=nc`
`BES.module.nc=/usr/local/lib/bes/libnc_module.so`

Module specific parameters will be included in its own configuration
file. For example, any parameters specific to the netcdf data handler
would be included in the nc.conf file.

## Pointing to data

There are two parameters that can be used to tell the BES where your
data are stored. Which one you use depends on whether you are setting up
the BES to work as part of Hyrax (and thus with THREDDS catalogs) or as
a standalone server. In either case set the value of the
*.RootDirectory* parameter to point to the root directory of your data
files (only one may be specified). Use
*BES.Catalog.catalog.RootDirectory* in the dap.conf configuration file
in the modules directory if the BES is being used as part of Hyrax, and
*BES.Data.RootDirectory* in bes.conf itself if not. So, if you are
setting up Hyrax, set the value of *BES.Catalog.catalog.RootDirectory*
but be **sure** to set *BES.Data.RootDirectory* to some value or the BES
will not start.

In bes.conf set the following:

`BES.Data.RootDirectory=/full/path/data/root/directory`

Also in bes.conf set the following if using Hyrax (usually the case)

`BES.Catalog.catalog.RootDirectory=/full/path/data/root/directory`

By default, the RootDirectory parameters are set to point to the test
data supplied with the data handlers.

Next configure the mapping between data source names and data handlers.
This is usually taken care of already for you, so you probably won't
have to set this parameter. Each data handler module (netcdf, hdf4,
hdf5, freeform, etc...) will have this set depending on the extension of
the data files for the data.

For example, in nc.conf, for the netcdf data handler module, you'll find
the line:

`BES.Catalog.catalog.TypeMatch+=nc:.*\.nc(\.bz2|\.gz|\.Z)?$;`

When the BES is asked to perform some commands on a particular data
source, it uses regular expressions to figure out which data handler
should be used to carry out the commands. The value of the
*BES.Catalog.catalog.TypeMatch* parameter holds the set of regular
expressions. The value of this parameter is a list of handlers and
expressions in the form handler:expression;. Note that these regular
expressions are like those used by `grep` on Unix and it's somewhat
cryptic, but once you see the pattern, it's not that bad. Below, the
*TypeMatch* parameter is being told that any data source with a name
that ends in `.nc` should be handled by the *nc* (netcdf) handler (see
*BES.module.nc* above), any file with a `.hdf`, `.HDF` or `.eos` suffix
should be processed using the HDF4 handler (note that case matters) and
that data sources ending in `.dat` should use the FreeForm handler.

Here's the one for the hdf4 data handler module:

`BES.Catalog.catalog.TypeMatch+=h4:.*\.(hdf|HDF|eos)(\.bz2|\.gz|\.Z)?$;`

And for the FreeForm handler:

`BES.Catalog.catalog.TypeMatch+=ff:.*\.dat(\.bz2|\.gz|\.Z)?$;`

If you fail to configure this correctly, the BES will return error
messages stating that the type information has to be provided. However,
it won't tell you this when it starts, only when the OLFS (or some other
software) actually makes a data request. This is because it's possible
to use BES commands in place of these regular expressions, although the
Hyrax won't.

## Including and Excluding files and directories

Finally, you can configure the types of information that the BES sends
back when a client requests catalog information. The *Include* and
*Exclude* parameters provide this mechanism, also using a list of
regular expressions (with each element of the list separated by a
semicolon). In the example below, files that begin with a dot are
excluded. These parameters are set in the dap.conf configuration file.

The Include expressions are applied to the node first, followed by the
Exclude expressions. For collections of nodes, only the Exclude
expressions are applied.

`BES.Catalog.catalog.Include=;`
`BES.Catalog.catalog.Exclude=^\..*;`

## Symbolic Links

If you would like for symbolic links to be followed when retrieving data
and for viewing catalog entries, then you need to set the following two
parameters. The *BES.FollowSymLinks* parameter is for non-catalog
containers and is used in conjunction with the *BES.RootDirectory*
parameter above. It is NOT a general setting. The
*BES.Catalog.catalog.FollowSymLinks* is for catalog requests and data
containers in the catalog and is used in conjunction with the
*BES.Catalog.catalog.RootDirectory* parameter above. The default is set
to No in the installed configuration file. To allow for symbolic links
to be followed you need to set this to Yes.

The following is set in the bes.conf file:

`BES.FollowSymLinks=No|Yes`

And this one is set in the dap.conf file in the modules directory:

`BES.Catalog.catalog.FollowSymLinks=No|Yes`

# Parameters for Specific Handlers

Parameters for specific modules can be added to the BES configuration
file for that specific module. No module-specific parameters should be
added to bes.conf.

# Sample Installation and Configuration

[Sample Installations Page](Hyrax_-_Sample_BES_Installations "wikilink")
shows how to download, build, install and configure for some sample
installations.