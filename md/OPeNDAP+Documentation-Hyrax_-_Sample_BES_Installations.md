__TOC__

This page will show some sample installations and configurations for the
BES. All of these will include libdap and the dap modules.

## Installing from SVN

Here we will show the installation of the BES, including libdap, from
the SVN repository. We will use the trunk, which is the latest and
greatest development being done on the software. In this example
installation, we'll include the netcdf data handler module.

If you'll be working on the code, we recommend that you install the
software not in the standard location, but in your own directory. This
way you can have multiple installations of the code if you need.

If you're just installing the software from SVN and won't be doing any
development, then installing in the standard location is just fine.

We're going to say for this installation that you are a developer,
contributing to this open source project. You're developing on a Mac or
Linux machine.

Create a directory in your home called opendap. Under this directory you
can check out different versions of the code. Let's say
/home/bob/opendap/version1. In this directory, I find it useful to
create a file that you can source. This way, when you want to work on
this version of the code, you can source this file to set up your
environment to use this version. For bash or ksh it would look like
this.

`export OPENDAP_ROOT=/home/bob/opendap/version1`
`export PATH="${OPENDAP_ROOT}/bin:${PATH}"`
`export LD_LIBRARY_PATH="${OPENDAP_ROOT}/lib:${LD_LIBRARY_PATH}"`

Now, in this terminal, you'll be able to develop and run this version of
opendap without impacting the rest of your machine.

Check out the code:

`% svn co `[`http://scm.opendap.org/svn/trunk/libdap`](http://scm.opendap.org/svn/trunk/libdap)` libdap`
`% svn co `[`http://scm.opendap.org/svn/trunk/bes`](http://scm.opendap.org/svn/trunk/bes)` bes`
`% svn co `[`http://scm.opendap.org/svn/trunk/dap-server`](http://scm.opendap.org/svn/trunk/dap-server)` dap-server`
`% svn co `[`http://scm.opendap.org/svn/trunk/netcdf_handler`](http://scm.opendap.org/svn/trunk/netcdf_handler)` netcdf_handler`

Now it's time to build the code.

`% cd libdap`
`% autoreconf --force --install`
`% ./configure --prefix=${OPENDAP_ROOT}`
`% make`
`% make check`
`% make install`

That finishes the build of the libdap, runs all of the tests to make
sure it all works properly, and installs it into
/home/bob/opendap/version1. Now, on to the BES.

`% cd ../bes`
`% autoreconf --force --install`
`% ./configure --prefix=${OPENDAP_ROOT} --enable-developer`
`% make`
`% make check`
`% make install`

In the build of the BES, if you're a developer, then you'll want to
enable the developer mode. This way you don't always have to run the BES
as root, and it enables so additional requests to help with development.

NOTE: This option should only be used in development. If you are
installing the software in a production environment then you should not
use this option. Installing this software into a production environment
with this option turned on is a security risk.

With the installation of libdap and the bes, we can now run the BES. It
won't be able to serve any data, or even run with the OLFS. But it
should still run. Let's test it.

`% cd ..`
`% besctl start`
`BES install directory: /home/bob/opendap/version1`
`BES configuration file: /home/bob/opendap/version1/etc/bes/bes.conf`
`Starting the BES`
`Developer Mode: not testing if BES is run by root`
`Developer Mode: not testing if BES is run by root`
`Developer Mode: Not setting group or user ids`
`OK: Successfully started the BES`
`PID: xxxx UID: yyy`

So, as you can see, starting the BES tells you where it things the
installation directory is, which BES configuration file it's going to
use, then starts the BES. Note that developer mode is turned on.

`% bescmdln -h localhost -p 10022`

`Type 'exit' to exit the command line client and 'help' or '?' to display the help screen`

`BESClient>show version;`

<?xml version="1.0" encoding="ISO-8859-1"?>

<response reqID="some_unique_value" ns="http://xml.opendap.org/ns/bes/1.0#">
`   `<showVersion>
`       `<library name="bes">`3.8.3`</library>
`       `<library name="libdap">`3.10.2`</library>
`       `<serviceVersion name="dap">
`           `<version>`2.0`</version>
`           `<version>`3.0`</version>
`           `<version>`3.2`</version>
`       `</serviceVersion>
`   `</showVersion>
</response>

`BESClient>show status;`

<?xml version="1.0" encoding="ISO-8859-1"?>

<response reqID="some_unique_value" ns="http://xml.opendap.org/ns/bes/1.0#">
`   `<showStatus>
`       `<status>`EDT Wed May  5 02:43:18 2010`</status>
`   `</showStatus>
</response>

`BESClient> exit`

Running bescmdln will show us that the client is able to connect to the
BES. And we run some quick commands to make sure that it's all installed
properly and accepting commands.

`% besctl stop`
`BES install directory: /home/bob/opendap/version1`
`BES configuration file: /home/bob/opendap/version1/etc/bes/bes.conf`
`Shutting down the BES daemon`
`Successfully shut down the BES`

This stops the BES. Again, it shows us where it things the BES is
installed, which configuration file, and then stops the BES process.

Installing the modules is the same process.

`% cd dap-server`
`% autoreconf --force --install`
`% ./configure --prefix=${OPENDAP_ROOT}`
`% make`
`% make check`
`% make install`

`% cd ../netcdf_handler`
`% autoreconf --force --install`
`% ./configure --prefix=${OPENDAP_ROOT}`
`% make`
`% make check`
`% make install`

And now we have everything installed and avaialable. And now you can run
commands against the BES to retrieve data. But first, make sure that
everything is installed properly. I won't show all the responses to the
commands here, but some of it.

`% besctl start`
`% bescmdln -h localhost -p 10022`
`BESClient> show version;`

<?xml version="1.0" encoding="ISO-8859-1"?>

<response reqID="some_unique_value" ns="http://xml.opendap.org/ns/bes/1.0#">
`   `<showVersion>
`       `<library name="bes">`3.8.3`</library>
`       `<module name="dap-server/ascii">`4.0.0`</module>
`       `<library name="libdap">`3.10.2`</library>
`       `<serviceVersion name="dap">
`           `<version>`2.0`</version>
`           `<version>`3.0`</version>
`           `<version>`3.2`</version>
`       `</serviceVersion>
`       `<module name="netcdf_handler">`3.9.1`</module>
`       `<module name="dap-server/usage">`4.0.0`</module>
`       `<module name="dap-server/www">`4.0.0`</module>
`   `</showVersion>
</response>

`BESClient>set container in catalog values c,data/nc/fnoc1.nc;`
`BESClient>define d as c;`
`BESClient>get dds for d;`
`Dataset {`
`   Structure {`
`       Int16 u[time_a = 16][lat = 17][lon = 21];`
`       Int16 v[time_a = 16][lat = 17][lon = 21];`
`       Float32 lat[lat = 17];`
`       Float32 lon[lon = 21];`
`       Float32 time[time = 16];`
`   } c;`
`} fnoc1.nc;`
`BESClient> exit`
`% besctl stop`

And there, you have a successful installation of the BES. And this
version can interact with the OLFS as well.

Of course, this BES is pointing to the default installed test data. To
point it at your data you'll need to edit the bes.conf file in
/home/bob/opendap/version1/etc/bes/. Change the value of
BES.Catalog.catalog.RootDirectory= to point to the root directory of
your data.

## Installing from Source

Installing the BES from a source distribution is quite similar to
installing from SVN. The only difference is that instead of checking out
the code from SVN, you download the source distribution for the
components that you want to install, build, test, and install from those
source distributions.

The source distributions are downloaded as tar balls. In other words,
they have a .tgz extension in them and you need to use tar to unpack
them all.

This installation we're going to say that you're building for production
and installing into the standard location, which is /usr/local. Make
sure /usr/local/bin is on your path.

And we're going to say that you want to serve hdf4 data, and provide the
ability to return the data as netcdf. For this installation you'll need
libdap, bes, dap-server (General purpose handler), hdf4_handler,
hdf5_handler and fileout_netcdf.

You can download the latest versions of these from the [Hyrax download
page](http://opendap.org/download/hyrax.html).

Let's create a directory /home/bob/packages/opendap, and work from
there.

`% mkdir /home/bob/packages/opendap`
`% cd /home/bob/packages/opendap`
`% cp download_dir/libdap-3.10.2.tar.gz .`
`% cp download_dir/bes-3.8.3.tar.gz .`
`% cp download_dir/dap-server-4.0.0.tar.gz .`
`% cp downlaod_dir/hdf4_handler-3.8.1.tar.gz .`
`% cp download_dir/fileout_netcdf-1.0.1.tar.gz .`

`% tar zxvf libdap-3.10.2.tar.gz`
`% tar zxvf bes-3.8.3.tar.gz`
`% tar zxvf dap-server-4.0.0.tar.gz`
`% tar zxvf hdf4_handler-3.8.1.tar.gz`
`% tar zxvf fileout_netcdf-1.0.1.tar.gz`

Now it's time to build. Another difference with downloading a source
distribution is that you don't have to use autoreconf. Just need to run
configure and build.

`% cd libdap-3.10.2`
`% ./configure`
`% make`
`% make check`
`% make install`

And then the BES

`% cd ../bes-3.8.3`
`% ./configure`
`% make`
`% make check`
`% make install`

Here, you should be able to run the BES. Even though there are no
modules installed yet for the BES to load, other then the DAP modules,
you should be able to execute simple commands. And this is a good way to
make sure everything is set up correctly before you continue. Here,
we'll run the BES and the bescmdln to test the server. Because this is a
production version of the server, you'll need to edit the bes.conf file
before running the BES, and run the BES as root.

In the bes.conf file, find the lines:

`BES.User=user_name`
`BES.Group=group_name`

Change user_name to whatever user will be running the BES. We recommend
creating a new user, called bes, with a group, called bes, that has
permission only to write to specific locations. And, while you're in
there, set the email address of the administrator of the BES.

`BES.ServerAdministrator=admin.email.address@your.domain.name`

Now we're ready to run the BES.

`% cd ..`
`% sudo besctl start`
`BES install directory: /usr/local`
`BES configuration file: /usr/local/etc/bes/bes.conf`
`Starting the BES`
`OK: Successfully started the BES`
`PID: xxxx UID: yyy`

Running the BES using besctl tells us where the BES is installed and
which configuration file it is using. Now we run bescmdln to make sure
we can talk with the BES.

`% bescmdln -h localhost -p 10022`

`Type 'exit' to exit the command line client and 'help' or '?' to display the help screen`

`BESClient>show version;`

<?xml version="1.0" encoding="ISO-8859-1"?>

<response reqID="some_unique_value" ns="http://xml.opendap.org/ns/bes/1.0#">
`   `<showVersion>
`       `<library name="bes">`3.8.3`</library>
`       `<library name="libdap">`3.10.2`</library>
`       `<serviceVersion name="dap">
`           `<version>`2.0`</version>
`           `<version>`3.0`</version>
`           `<version>`3.2`</version>
`       `</serviceVersion>
`   `</showVersion>
</response>

`BESClient>show status;`

<?xml version="1.0" encoding="ISO-8859-1"?>

<response reqID="some_unique_value" ns="http://xml.opendap.org/ns/bes/1.0#">
`   `<showStatus>
`       `<status>`EDT Wed May  5 02:43:18 2010`</status>
`   `</showStatus>
</response>

`BESClient> exit`

Now that we know that the BES is running successfully, time to add some
modules.

`% cd dap-server-4.0.0`
`% ./configure`
`% make`
`% make check`
`% make install`
`% cd ../hdf4_handler-3.8.1`
`% ./configure`
`% make`
`% make check`
`% make install`
`% cd ../fileout_netcdf-1.0.1`
`% ./configure`
`% make`
`% make check`
`% make install`

The BES is now ready to serve hdf4 data and return data as netcdf files.

`% besctl start`
`% bescmdln -h localhost -p 1000\22`
`BESClient> show version;`

<?xml version="1.0" encoding="ISO-8859-1"?>

<response reqID="some_unique_value" ns="http://xml.opendap.org/ns/bes/1.0#">
`   `<showVersion>
`       `<library name="bes">`3.8.3`</library>
`       `<module name="dap-server/ascii">`4.0.0`</module>
`       `<library name="libdap">`3.10.2`</library>
`       `<serviceVersion name="dap">
`           `<version>`2.0`</version>
`           `<version>`3.0`</version>
`           `<version>`3.2`</version>
`       `</serviceVersion>
`       `<module name="fileout_netcdf">`1.0.1`</module>
`       `<module name="hdf4_handler">`3.8.1`</module>
`       `<module name="dap-server/usage">`4.0.0`</module>
`       `<module name="dap-server/www">`4.0.0`</module>
`   `</showVersion>
</response>

`BESClient>set container in catalog values c,data/hdf4/S2000415.HDF;`
`BESClient>define d as c;`
`BESClient>get dds for d;`
`Dataset {`
`   Structure {`
`       Structure {`
`           Int16 WVC_Lat[row = 458][WVC = 24];`
`           UInt16 WVC_Lon[row = 458][WVC = 24];`
`           Byte Num_Sigma0[row = 458][WVC = 24];`
`           Byte Num_Beam_12[row = 458][WVC = 24];`
`           Byte Num_Beam_34[row = 458][WVC = 24];`
`           Byte Num_Beam_56[row = 458][WVC = 24];`
`           Byte Num_Beam_78[row = 458][WVC = 24];`
`           Byte WVC_Quality_Flag[row = 458][WVC = 24];`
`           UInt16 Mean_Wind[row = 458][WVC = 24];`
`           UInt16 Wind_Speed[row = 458][WVC = 24][position = 4];`
`           UInt16 Wind_Dir[row = 458][WVC = 24][position = 4];`
`           UInt16 Error_Speed[row = 458][WVC = 24][position = 4];`
`           UInt16 Error_Dir[row = 458][WVC = 24][position = 4];`
`           Int16 MLE_Likelihood[row = 458][WVC = 24][position = 4];`
`           Byte Num_Ambigs[row = 458][WVC = 24];`
`           Sequence {`
`               Structure {`
`                   Int16 begin__0;`
`               } begin;`
`           } SwathIndex;`
`           Sequence {`
`               Structure {`
`                   String Mean_Time__0;`
`               } Mean_Time;`
`               Structure {`
`                   UInt32 Low_Wind_Speed_Flag__0;`
`               } Low_Wind_Speed_Flag;`
`               Structure {`
`                   UInt32 High_Wind_Speed_Flag__0;`
`               } High_Wind_Speed_Flag;`
`           } NSCAT%20L2;`
`       } NSCAT%20Rev%2020;`
`   } c;`
`} S2000415.HDF;`
`BESClient> get dods for d return as netcdf; (NOT A PRETTY SITE, MIGHT NOT WANT TO DO THIS)`
`BESClient> exit`
`% besctl stop`

And there, you have a successful installation of the BES. And this
version can interact with the OLFS as well.

Of course, this BES is pointing to the default installed test data. To
point it at your data you'll need to edit the bes.conf file in
/home/bob/opendap/version1/etc/bes/. Change the value of
BES.Catalog.catalog.RootDirectory= to point to the root directory of
your data.

## Installing from Binary

In this sample installation, we will install libdap, the BES, the
dap-server General Purpose library modules, and the freeform data
handler module. You will need to have root access to perform these
tasks, either with root login information or sudo privileges.

Download the RPM packages from the Hyrax download page. Let's say that
you have your browser set up to download files to your Desktop
directory. And then we'll move these RPM packages to a new directory in
your home directory called rpmdir.

`% mkdir ~/rpmdir`
`% cd ~/rpmdir`
`% mv ~/Desktop/*.rpm .`

Install these new RPM packages using the rpm utility. You will need to
have root access to install this software. We install the development
packages in case you are wanting to develop your own modules for the BES
to serve some new kind of data. You don't have to install them if you
don't plan on doing any development.

`% sudo rpm --install ./libdap-3.10.2-1.i386.rpm `
`% sudo rpm --install ./libdap-devel-3.10.2-1.i386.rpm `
`% sudo rpm --install ./bes-3.8.3-1.i386.rpm `
`% sudo rpm --install ./bes-devel-3.8.3-1.i386.rpm `
`% sudo rpm --install ./dap-server-4.0.0-2.i386.rpm `
`% sudo rpm --install ./freeform_handler-3.8.1-1.i386.rpm `

Start the BES. Again, for this you will need to have root access,
probably with sudo privileges. And, without any modifications to the BES
configuration files, you should be able to retrieve some of the test
data.

`% sudo besctl start`
`Password:`
`BES install directory: /usr`
`BES configuration file: /etc/bes/bes.conf`
`Starting the BES`
`bescmdln -h OK: Successfully started the BES`
`PID: 5469 UID: 0`

Connect to the new BES using the command line client

`% bescmdln -h localhost -p 10022`


`Type 'exit' to exit the command line client and 'help' or '?' to display the help screen`

`BESClient>`

First, verify that all of the components have been installed

`BESClient> show version;`
<response ns="http://xml.opendap.org/ns/bes/1.0#" reqID="some_unique_value">
`   `<showVersion>
`       `<library name="bes">`3.8.3`</library>
`       `<module name="dap-server/ascii">`4.0.0`</module>
`       `<library name="libdap">`3.10.2`</library>
`       `<serviceVersion name="dap">
`           `<version>`2.0`</version>
`           `<version>`3.0`</version>
`           `<version>`3.2`</version>
`       `</serviceVersion>
`       `<module name="freeform_handler">`3.8.1`</module>
`       `<module name="dap-server/usage">`4.0.0`</module>
`       `<module name="dap-server/www">`4.0.0`</module>
`   `</showVersion>
</response>

And let's try and retrieve some data:

`BESClient> set container in catalog values c,data/ff/avhrr.dat;`
`BESClient> define d as c;`
`BESClient> get dds for d;`
`Dataset {`
`   Structure {`
`       Sequence {`
`           Int32 year;`
`           Int32 month;`
`           Int32 day;`
`           Int32 hours;`
`           Int32 minutes;`
`           Int32 seconds;`
`           String DODS_URL;`
`       } URI_Avhrr;`
`   } c;`
`} avhrr.dat;`
`BESClient> exit`

Shutdown the BES ... a successful test.

`sudo besctl stop`
`Password: `
`BES install directory: /usr`
`BES configuration file: /etc/bes/bes.conf`
`Shutting down the BES daemon`

Now that we know that you can run the BES and retrieve the test data, we
can modify the bes.conf file in /etc/bes/modules directory, setting the
RootDirectory for your data to wherever on your machine your data is
located:

`BES.Catalog.catalog.RootDirectory=/path/to/your/data/directory`

And once you have this set, you should be able to restart the BES and
use the bescmdln to see your data.