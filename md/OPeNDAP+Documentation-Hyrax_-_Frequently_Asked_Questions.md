# OLFS

The OPeNDAP Lightweight Front-end Service

## Status 503 "application not currently available"

After starting up the tomcat server with the opendap.war file placed in
the webapps directory, I can see the tomcat splash page if I go to

    http://localhost:8080/

. But when I go to

    http://localhost:8080/opendap/

I get this error.

# BES

The OPeNDAP Hyrax Back-End Server

## Regular expression matches in BES

There are two regular expression matches in the BES, both configured in
the BES configuration file bes.conf.

- TypeMatch

The TypeMatch regular expression is used to match file names with their
type. For example, a file ending with the extension .hdf would match and
be handled by the hdf4 module.

***Note: The regular expression must match the file including the path
to the file.***

The TypeMatch parameter is a list of handler/module names and a regular
expression separated by a colon. If the regular expression matches an
item, then the BES uses the associated handler/module. Each
<handler>:<regular expression> pair is followed by a semicolon. This is
used when creating containers in the BES (the 'set container' command).
The example regular expression says to use the 'h4' handler for any file
with an extension of 'hdf', 'HDF' or 'eos' which may also end in '.gz'
or '.bz2' or '.Z'. In the latter case the file will be treated as a
compressed file.

` Example:`
` BES.Catalog.catalog.TypeMatch=h4.*\.(hdf|HDF|eos)(\.gz|\.bz2|\.Z)?$;`

- Include/Exclude directive

The Include/Exclude parameters in the BES configuration file tells the
BES what files and directories to include/exclude in its catalog
response. Normally, when a client asks for a data catalog, all files and
directories are shown. Use the following two parameters to customize
this behavior. Each parameter is a list of regular expressions, each
followed by a semicolon (last one must also end with a semicolon as in
the example below.) First, the Include parameter is applied to the node,
and then the Exclude parameter is applied. All collections of nodes are
shown. In the default values below, all nodes are included (the Include
parameter) except those that begin with a dot (the Exclude parameter).

` Example:`
` BES.Catalog.catalog.Include=;`
` BES.Catalog.catalog.Exclude=^\..*;`

- There is a binary included in the distribution that will allow you to
  test a regular expression. The besregtest binary is run with the type
  of regular expression (type, include or exclude), the regular
  expression enclosed in quotes, and then the list of things to match.
  Here is the output of the usage of besregtest:

`Usage: besregtest include|exclude|type `<regular_expression>` `<string_to_match>
`  samples:`
`    besregtest include "123456;" 01234567 matches 6 of 8 characters`
`    besregtest include "^123456$;" 01234567 does not match`
`    besregtest include "^123456$;" 123456 matches all 6 of 6 characters`
`    besregtest include ".*\.nc$;" fnoc1.nc matches`
`    besregtest include ".*\.nc$;" fnoc1.ncd does not matche`
`    besregtest type "nc:.*\.nc$;nc:.*\.nc\.gz$;ff:.*\.dat$;ff:.*\.dat\.gz$;" fnoc1.nc matches type nc`

- Examples of the TypeMatch parameter:

`% besregtest type "nc:.*\.nc(\.bz2|\.gz|\.Z)?$|.*/JA2_[OIG]P[NRS]_.*$;" jason2/gdr/gdr/cycle004/JA2_GPN_2PTP004_069_20080813_105815_20080813_115428`
`expression ".*\.nc(\.bz2|\.gz|\.Z)?$|.*JA2_[OIG]P[NRS]_.*$" matches exactly, type = nc`

`Matches anything that ends with .nc OR anything that ends with a file that begins with JA2_; then one of O, I, G; then P; then one of N, R, S;`
`followed by an underscore and anything else.`

## BES: unable to start properly because can not determine memory keys.

In release 1.4.1 of Hyrax, which includes release 3.6.0 of the BES, we
fixed a typo in the BES configuration file. Unfortunately, this produces
an error when you start up the BES if the configuration still contains
the typo. The error message is:

` BES: unable to start properly because can not determine memory keys.`

To correct this, please change the parameter
BES.Memory.GlobalArea.Maximu**n**HeapSize to
BES.Memory.GlobalArea.Maximu**m**HeapSize.

## The service dap has already been registered

Starting with Hyrax 1.6.0, the BES configuration has changed. It used to
be that each handler/module would modify the bes.conf file, or the user
would have to modify the bes.conf file, in order to enable that module.
Now, the handler/module installs a .conf file in the directory modules
under <prefix>/etc/bes/modules. The bes.conf file exists in
<prefix>/etc/bes, and includes any .conf files located in the modules
directory.

If you've installed the new BES for Hyrax 1.6, and then modify your
bes.conf file like you've done in the past, this could cause problems
like duplicate modules registered, duplicate services registered, etc...

So, in the new bes.conf file, there is a line:

BES.modules=

which is empty. Leave it empty. The .conf files in the modules
directory, such as nc.conf, will contain a line that looks like this:

BES.modules+=nc BES.module.nc=<prefix>/lib/bes/libnc_module.so

The only items that you will need to change in the bes.conf file is:

`BES.ServerAdministrator=`
`BES.User=`
`BES.Group=`
`BES.LogName=`<path_to>`/bes.log`
`BES.CacheDir=`<path_to_bes_cache_directory>

## I've installed the BES, but I'm not able to access any data

The BES is a software framework that allows data providers to serve
specific kinds of data in specific ways. Off the shelf the BES doesn't
provide any data access or any kinds of responses, not even DAP
responses like DAS, DDS, ASCII, etc...

In order to return DAP responses like DAS, DDS, DDX, DataDDS you have to
have a BES installed that is built against libdap. When building from
source you should first install, or build and install, libdap. When
installing from RPM you will not be able to install without first
installing the libdap RPM.

In order to have an ascii response for your data, or have the html form
presented, you must install the General Purpose Handlers, which can be
found on the Hyrax Download Page, as can any of the data handlers,
libdap, and the fileout module. Here is the official list of BES
modules.

In order to serve a particular type of data you must install one of the
data handlers. The current officially supported data handlers are
netcdf, hdf4, hdf5 and freeform. Again, these can be downloaded from
Hyrax Download Page.

If building from source or from SVN, just running 'make install' isn't
enough. You must also either run 'make bes-conf' or run the
bes-x-data.sh script for that data handler. For example, bes-nc-data.sh.
Building that target using make, or running those scripts, adds
information to the BES configuration file, bes.conf. This step is
important to installing the different modules and handlers.

## BES Fatal; cannot open log file ./bes.log.

Although the BES is started as root, it will be running as the user you
set in the BES configuration file. So be sure that that user has
permission to write to the current directory where you are starting the
BES.

Alternatively, edit the BES configuration file bes.conf and change the
location of the bes.log file.

## How do I add configuration parameters to my .conf file

Every module has its own configuration file, x.conf. For example, the
netcdf data handler module has a configuration file called nc.conf.
These files are located in the intall_dir/etc/bes/modules directory. So
if you install in /usr/local, it will be in /usr/local/etc/bes/modules.
If you are writing a module, you can add your own parameters to your
configuration file.

Please go to the [BES module
page](http://docs.opendap.org/index.php/Hyrax_-_Extending_BES_Module#Configuring_your_Module)
for more information on how to do this.