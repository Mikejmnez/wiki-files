## Description

The CEDAR data handler module was written for the [Coupling, Energetics
and Dynamics of Atmospheric Regions
(CEDAR)](http://cedarweb.hao.ucar.edu/) community to be able to read in
CEDAR data files (cbf) and return data products known within the OPeNDAP
community, as well as provide data products that the CEDAR community
uses (FLAT, TAB, and INFO return types)

CEDAR data access is provided via the [Virtual Solar Terrestrial
Observatory data portal](http://www.vsto.org).

## Status

Stable release 2.3.0 in production

## Author

- [Patrick West](http://tw.rpi.edu/wiki/Patrick_West)

## Services Handled

- dap
  - DAS
  - DDS
  - DDX
  - Data
  - Data return as netcdf
  - ascii
  - html_form
  - info_page

## Services Provided

- cedar
  - FLAT
  - TAB
  - INFO (different from info_page)

## Download

The latest release of this module is cedar_handler 2.3.0 and can be
retrieved from the [OPeNDAP Subversion
repository](http://scm.opendap.org/svn/tags/cedar/cedar_3.1.0/cedar_handler-2.3.0/)

## How to use the module

Once installed, the cedar.conf file is added to the etc/bes/modules
directory to be loaded by the BES. The configuration file must first be
edited to be able to access the CEDAR Catalog (MySQL relational
database), the CEDAR reporting database (MySQL relational database), and
the CEDAR user database (for authentication and authorization purposes).

    #-----------------------------------------------------------------------#
    # OPeNDAP Cedar Data Handler BES Module Configuration file              #
    #-----------------------------------------------------------------------#

    #-----------------------------------------------------------------------#
    # modules to load, includes data modules and command modules            #
    #-----------------------------------------------------------------------#

    BES.modules+=cedar
    BES.module.cedar=/Users/westp/opendap/opendap/lib/bes/libcedar_module.so

    #-----------------------------------------------------------------------#
    # Setting the data information
    #-----------------------------------------------------------------------#

    # Cedar Handler specific parameters"
    # Cedar.LogName - file to store cedar access information
    # Cedar.BaseDir - base directory where the cedar data resides
    # Cedar.LoginScreen.XML - file that contains the html page to
    #   display if login is needed
    # Cedar.Help.TXT - location of the text version of cedar help
    # Cedar.Help.HTML - location of the html version of cedar help
    # Cedar.Help.XML - location of the xml version of cedar help
    # Cedar.Authenticate.Mode=on|off - should the server authenticate
    # Cedar.DB.Authenticate.Type=mysql - type of database for authentication database
    # Cedar.DB.Authenticate.Server= - MySQL server (i.e. localhost)
    # Cedar.DB.Authenticate.User= - MySQL user name to connect to db
    # Cedar.DB.Authenticate.Password= - MySQL password for user to connect
    # Cedar.DB.Authenticate.Database= - MySQL database with session table
    # Cedar.DB.Authenticate.Socket= - MySQL unix socket used to connect
    # Cedar.DB.Authenticate.Port= - MySQL TCP Port used to connect, socket typically used
    # Cedar.DB.Catalog.Type=mysql - type of database for CEDARCATALOG
    # Cedar.DB.Catalog.Server= - MySQL server (i.e. localhost)
    # Cedar.DB.Catalog.User= - MySQL user name to connect to db
    # Cedar.DB.Catalog.Password= - MySQL password for user to connect
    # Cedar.DB.Catalog.Database= - MySQL database with catalog tables
    # Cedar.DB.Catalog.Socket= - MySQL unix socket used to connect
    # Cedar.DB.Catalog.Port= - MySQL TCP Port used to connect, socket typically used
    # Cedar.DB.Reporter.Type=mysql - type of database to use for CEDARREPORTER
    # Cedar.DB.Reporter.Server= - MySQL server (i.e. localhost)
    # Cedar.DB.Reporter.User= - MySQL user name to connect to db
    # Cedar.DB.Reporter.Password= - MySQL password for user to connect
    # Cedar.DB.Reporter.Database= - MySQL database with reporter table
    # Cedar.DB.Reporter.Socket= - MySQL unix socket used to connect
    # Cedar.DB.Reporter.Port= - MySQL TCP Port used to connect, socket typically used

    Cedar.LogName=./cedar.log
    Cedar.BaseDir=/path/to/cedar/data
    Cedar.LoginScreen.XML=./screen.xml

    Cedar.Help.TXT=/Users/westp/opendap/opendap/share/cedar_handler/cedar_help.txt
    Cedar.Help.HTML=/Users/westp/opendap/opendap/share/cedar_handler/cedar_help.html
    Cedar.Help.XML=/Users/westp/opendap/opendap/share/cedar_handler/cedar_help.txt

    Cedar.Authenticate.Mode=on
    Cedar.DB.Authenticate.Type=mysql
    Cedar.DB.Authenticate.Server=localhost
    Cedar.DB.Authenticate.User=root
    Cedar.DB.Authenticate.Password=
    Cedar.DB.Authenticate.Database=
    Cedar.DB.Authenticate.Socket=/tmp/mysql.sock
    Cedar.DB.Authenticate.Port=

    Cedar.DB.Catalog.Type=mysql
    Cedar.DB.Catalog.Server=localhost
    Cedar.DB.Catalog.User=root
    Cedar.DB.Catalog.Password=
    Cedar.DB.Catalog.Database=
    Cedar.DB.Catalog.Socket=/tmp/mysql.sock
    Cedar.DB.Catalog.Port=

    Cedar.DB.Reporter.Type=mysql
    Cedar.DB.Reporter.Server=localhost
    Cedar.DB.Reporter.User=root
    Cedar.DB.Reporter.Password=
    Cedar.DB.Reporter.Database=
    Cedar.DB.Reporter.Socket=/tmp/mysql.sock
    Cedar.DB.Reporter.Port=

The Password parameters are encoded in the configuration file, not plain
text.

The CEDAR module includes its own container storage, so containers do
not need to be created within the BES. The base name of the actual
dataset can be used as the container name, as the example below shows
using mfp9205040a.

If not using the OLFS to serve your data, for example if using the
bescmdln, you would run a command file that would look something like
this:

    <?xml version="1.0" encoding="UTF-8"?>
    <request reqID="some_unique_value" >
        <define name="d">
        <container name="mfp920504a" />
        </define>
        <get type="dods" definition="d"/>
    </request>

[CEDAR Handler](Category:BES_Modules "wikilink")