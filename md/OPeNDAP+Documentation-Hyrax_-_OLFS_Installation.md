**The OLFS comes with a default configuration that is compatible with
the default configuration of the BES. If you do a default install of
each one you should get a running Hyrax server that will be
pre-populated with test data suitable for running the integrity tests.**

# [Install The BES](Hyrax_-_BES_Installation "wikilink")

# Download

If you haven't already got it, go [get the latest OLFS distribution from
here](http://www.opendap.org/download/olfs.html). You should get the
binary jar file which will be named something like:
***olfs-x.x.x-webapp.tgz***

# Unpack

Unpack the jar file with the command:

`tar -xvf olfs-x.x.x-webapp.tgz`

which will unpack a directory called olfs-x.x.x-webapp

# Install

Inside the that directory find the opendap.war file, and copy it into
the Tomcat's webapps directory:

`cp olfs-x.x.x-webapp/opendap.war /usr/local/apache-tomcat-6.x.x/webapps/`

(Assuming the your Tomcat server is in /usr/local)

If you're replacing an older version of the OLFS you may:

- Need to remove the directory `$CATALINA_HOME/webapps/opendap` before
  restarting Tomcat.
- Discover that some of the existing configuration information for the
  OLFS may need to be updated. If after you start Tomcat things don't
  work you should compare the `$CATALINA_HOME/content/opendap/olfs.xml`
  file that contains your exisiting configuration to the new default
  configuration located in
  `$CATALINA_HOME/webapps/opendap/intitalContent/olfs.xml` Look at the
  lists of DispatchHandlers and adjust your configuration to use the
  same ones.

# Setup Tomcat

Configure the Tomcat environment by setting the environment variable
CATALINA_HOME to the full path for the Tomcat distribution.

In bash:

`export CATALINA_HOME = /usr/local/apache-tomcat-6.x.x`

# Start Tomcat

In the top level tomcat directory (apache-tomcat-6.x.x on my machine)
issue the command:

`bin/startup.sh; tail -f logs/catalina.out`

Wait a few seconds while it all starts up.

When Tomcat starts up it will unpack your *'OLFS* and install the
webapp.

**Usage Note**: If you use *ctrl-c* to stop watching the tail of the
servers output, make sure to run the command:

`bin/shutdown.sh`

to shutdown Tomcat. If you don't, you may get errors when you next try
to start the Tomcat server.

------------------------------------------------------------------------