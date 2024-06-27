<font size="5">

This page is deprecated
These instructions apply to Hyrax version 1.10 and older (BES 3.13.x and
earlier)

</font>



<font size="4">For Hyrax 1.11 and newer, please see the [Hyrax release
page](http://www.opendap.org/download/hyrax) for information about the
binary installation or see the [github
page](http://docs.opendap.org/index.php/Hyrax_GitHub_Source_Build) for
information about source builds.</font>

The BES comes with a default configuration that is compatible with the
default configuration of the OLFS. If you do a default install of each
one you should get a running Hyrax server that will be pre-populated
with test data suitable for running the integrity tests.

# Download

If you haven't already got it, go [Hyrax get the latest BES distribution
from the distribution page](http://www.opendap.org/download/hyrax).

# Install

General installation tip: Since you're running the BES as part of Hyrax,
there's no need to take advantage of the ability to communicate directly
with the BES, except possibly for diagnosing certain installation
problems. Once you have the BES installed, you should seriously consider
closing access to the port on which it listens. This is easy to do by
simply configuring your firewall to block access to the port (port 10022
by default, see your bes.conf file).

## Installing from RPMs

Get the RPMs from the [Hyrax distribution
page](http://www.opendap.org/download/hyrax). You will need the full
complement of RPMs, which include libdap, bes, dap-server, and probably
some of the data handlers. You can install them with the command

`sudo rpm -Uvh `<list the RPMs>

Once you have installed the individual handler packages, you can install
sample data and configure the Back End Server (BES) componenet of Hyrax
for those handlers using scripts provided in the handler's rpm packages.
These scripts are all named **bes-<handler>-data.sh** and are installed
in the **bin** directory so you should be able to run them at any
command line prompt. For example, bes-nc-data.sh will add netcdf_handler
configuration to the BES configuration file.

Here's a list of the required RPM packages for a typical Hyrax
installation.

1.  libdap: The DAP implementation used by the handlers
2.  bes: The BES component of Hyrax
3.  dap-server: These handlers provide basic services for Hyrax

Plus you must have at least one data handler:

- netcdf_handler: A handler for netCDF 2.x and 3.x files
- hdf4_handler: A handler for HDF4 files, including HDF-EOS stored in
  HDF4
- freeform_handler: A handler for text and binary files where you can
  tell the handler how those files are formatted
- hdf5_handler: A handler for HDF5 files.
- csv_handler: A handler for data stored as **c**oma
  *s**eperated**a*'scii values.
- fits_handler: A handler for serving data stored in FITS format.

There is also a set of handlers that can be installed that increase the
scope of the services provided by Hyrax. We recommend you install them
all!

- fileout_netcdf: A handler that enables Hyrax to return data in
  netCDF-3 format.
- xml_data_handler: A handler that enables Hyrax to return data in an
  XML document based on the DAP DDX.
- ncml_handler: A handler that provides Hyrax with the ability to
  aggregate and to add/change/remove metadata for datasets.
- gateway_handler: A handler that enables Hyrax to provide DAP services
  for netCDF, hdf4, and hdf5 files stored at remote locations..

In addition to these RPM packages you will need the OLFS Java jar file.

- OLFS: This is the Java Servlet front end component of Hyrax

## Installing from DMG for Mac OSX

Get the DMGs from the [Hyrax distribution
page](http://www.opendap.org/download/hyrax). You will need the full
complement of DMGs, which include libdap, bes, dap-server, and probably
some of the data handlers. Be sure to get the DMG for your particular
system, either Intel based (i386) or PowerPC (ppc) DMG files.

You will need to install the DMGs in a particular order:

1.  libdap: The DAP implementation used by the handlers
2.  bes: The BES component of Hyrax
3.  dap-server: These handlers provide basic services for Hyrax

Plus you must have at least one data handler:

- netcdf_handler: A handler for netCDF 2.x and 3.x files
- hdf4_handler: A handler for HDF4 files, including HDF-EOS stored in
  HDF4
- freeform_handler: A handler for text and binary files where you can
  tell the handler how those files are formatted
- hdf5_handler: A handler for HDF5 files.
- csv_handler: A handler for data stored as **c**oma
  *s**eperated**a*'scii values.
- fits_handler: A handler for serving data stored in FITS format.

There is also a set of handlers that can be installed that increase the
scope of the services provided by Hyrax. We recommend you install them
all!

- fileout_netcdf: A handler that enables Hyrax to return data in
  netCDF-3 format.
- xml_data_handler: A handler that enables Hyrax to return data in an
  XML document based on the DAP DDX.
- ncml_handler: A handler that provides Hyrax with the ability to
  aggregate and to add/change/remove metadata for datasets.
- gateway_handler: A handler that enables Hyrax to provide DAP services
  for netCDF, hdf4, and hdf5 files stored at remote locations..

To install a DMG, download the DMG to your local disk. Double click on
the .dmg file. This will mount the disk image and open up a finder
window. Read the README file included in the disk image. Double click on
the .pkg file and follow the instructions for installation. This will
install the package under /usr.

In addition to these RPM packages you will need the OLFS Java jar file.

- OLFS: This is the Java Servlet front end component of Hyrax

# [BES Configuration](Hyrax_-_BES_Configuration "wikilink")

You will need to configure the BES next, pointing the BES to your data
and setting up some other options.

# [Starting and stopping the BES](Hyrax_-_Starting_and_stopping_the_BES "wikilink")

Once you have the BES installed and configured you will want to start
it.

# [Testing the BES with the command line client](Hyrax_-_Running_bescmdln "wikilink")

Let's make sure your installation and configuration are working
properly.

# [Sample installations and configurations of the BES](Hyrax_-_Sample_BES_Installations "wikilink")

The page will show a few different methods of installing the BES and
configuring it, with a sample installation with some modules

------------------------------------------------------------------------