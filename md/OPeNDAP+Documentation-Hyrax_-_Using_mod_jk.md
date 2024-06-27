**Request for Alternate Solutions**: By no means do I imagine that I
have outlined a best solution here. If these suggestions work for you
then we are both happy. If you have a better method for achieving the
same or similar goals then I would love to hear about it. If you're
stuck and can't figure out how to achieve your goals, get in touch and I
will try to help. - ndp (ndp at opendap dot org)

# Overview

## mod_jk

Most discussions of integrating Tomcat and Apache start (and often end
with) an Apache module called *mod_jk*. This module allows Tomcat to map
directly in to the Apache URI space. However it has limitations:

- When using *mod_jk* one must specify a "mount point", a place in the
  Apache servers URI space beneath which all requests get sent to
  Tomcat. As far as I can tell from using *mod_jk* the name of the mount
  point must be synonymous with the servlet context (and possibly
  servlet name, depending on the Tomcat configuration). This works well,
  and if your goal is to build a web site using Apache and Hyrax from
  the ground up it is probably a reasonable way to proceed. However, it
  does not address the goal of preserving the existing OPeNDAP data
  access URL's when migrating to Hyrax. For this you need to use
  *mod_rewrite*.

- It is not clear to me if using *mod_jk* is more efficient or in any
  other way preferable to using a reverse proxy.

------------------------------------------------------------------------

# Install Hyrax

Install Hyrax as described in the [installation
instructions](Hyrax_1.2:_Installation_Instructions "wikilink"). No
matter how you want things to end up you have to do this first.

------------------------------------------------------------------------

# Integrate Tomcat with Apache 2.x using mod_jk , mod_rewrite, and mod_proxy

(This section assumes that your Tomcat server is running on the same
system as your Apache server. If not you will need to modify the
instructions as needed. )

## Make sure you've got mod_rewrite, and mod_proxy

Depending on your Apache installation you may need to rebuild/compile
Apache to enable *rewrite* and *proxy*. You should first look at the
*http.conf* configuration file. Find the *LoadModule* directives and
look for the lines that load the proxy and rewrite modules. They should
look something like this:

`LoadModule rewrite_module modules/mod_rewrite.so`
`LoadModule proxy_module modules/mod_proxy.so`

If you see the modules loaded then your set to go, skip to the next
section.

If not then you will probably need to compile Apache from source and
enable the rewrite and proxy modules.

1.  Make sure you have the Apache source.
2.  Run the Apache configure script with the --enable-rewrite and
    --enable-proxy switches. Minimally your configure command should
    look like this:

    `./configure --enable-rerwrite --enable-proxy`

3.  (Re)compile.
4.  (Re)install.

## Install mod_jk

**Before proceeding make sure you have installed the *mod_jk* library in
your Apache *modules* directory.**

## Configuration Details

You will need to find an example of a *workers.properties* file. An
example may be distributed with your Tomcat bundle (I didn't get one
with 5.5.15, but I found one in an older - 5.5.9 - distribution). I
would use the "minimal" example to start. Get it into your
*\$CATALINA_HOME/conf* directory. It most likely it contains the
following 4 lines:

`worker.list=hyrax`
`worker.hyrax.type=ajp13`
`worker.hyrax.host=localhost`
`worker.hyrax.port=8009`

Now you need to edit the Tomcat *server.xml* file to enable the AJP 1.3
connector. Remember - the *workers.properties* file and the connector
definition must agree! Inside of *\$CATALINA_HOME/conf/server.xml* add
(or possibly uncomment) the AJP connector definition inside of the
service named Catalina:

        <Service name="Catalina">
        .
        .
        .
        <!-- Define an AJP 1.3 Connector on port 8009 -->
        <Connector port="8009"
        enableLookups="false" redirectPort="8443" protocol="AJP/1.3" />
        .
        .
        .
        </Service>

Now, follow the *mod_jk* instructions for using Tomcat auto-configure.
This should create the file *\$CATALINA_HOME/conf/**auto**/mod_jk.conf*.
I suggest you move this file to *\$CATALINA_HOME/conf/jk/mod_jk.conf* so
that you may edit it with the fear that it may be overwritten. Assuming
you have done that, add the following line to your Apache server's
*http.conf* file:

`Include /usr/local/apache-tomcat-5.5.15/conf/jk/mod_jk.conf`

Now you will need to edit the *\$CATALINA_HOME/conf/jk/mod_jk.conf*
file. It should be quite simple to begin with, mine contained this:

        # load mod_jk
        <IfModule !mod_jk.c>
            LoadModule jk_module "/usr/local/apache2/modules/mod_jk.so"
        </IfModule>

To this we need to add the stuff to turn on reverse proxies and rewrite.
Then we need to configure *mod_jk* and map the old OPeNDAP data URL's to
the new *mod_jk* service.

To turn on reverse proxies and rewrite. Add the following lines to the
file:

        # Enable the rewrite module
        RewriteEngine on
        # Target it's logging somewhere useful
        RewriteLog /usr/local/apache2/logs/rewrite.log
        # Turn on logging (Set to 0 to disable)
        RewriteLogLevel 2

        #Configure mod_proxy to disable everything except reverse proxies.
        ProxyRequests Off
        <Proxy *>
        Order deny,allow
        Allow from all
        </Proxy>

To configure *mod_jk*, add the following to the
*\$CATALINA_HOME/conf/jk/mod_jk.conf* file:

        # Set mod_jk for our servlet
        JkWorkersFile /usr/local/apache-tomcat-5.5.15/conf/workers.properties.minimal
        JkLogFile /usr/local/apache-tomcat-5.5.15/logs/mod_jk.log
        JkLogLevel error
        JKMount /opendap/* ajp13

To map the old OPeNDAP data URL's to the new service (assuming they were
located at *<http://your.server/cgi-bin/nph-dods/>...*) add this to the
*\$CATALINA_HOME/conf/jk/mod_jk.conf* file:

        # Use a secure reverse proxy to enable mapping the old URL's too tomcat.
        RewriteRule ^/cgi-bin/nph-dods(.*) http://localhost:8080/opendap/hyrax/$1 [P]

In the end my *\$CATALINA_HOME/conf/jk/mod_jk.conf* file looked like
this:

        # We need rewrite and proxy to map the old URL's to the new ones.
        RewriteEngine on
        RewriteLog /usr/local/apache2/logs/rewrite.log
        RewriteLogLevel 2
        ProxyRequests Off

        <Proxy *>
            Order deny,allow
            Allow from all
        </Proxy>

        # load mod_jk
        <IfModule !mod_jk.c>
            LoadModule jk_module "/usr/local/apache2/modules/mod_jk.so"
        </IfModule>

        # Set mod_jk for our servlet
        JkWorkersFile /usr/local/apache-tomcat-5.5.15/conf/workers.properties.minimal
        JkLogFile     /usr/local/apache-tomcat-5.5.15/logs/mod_jk.log
        JkLogLevel    error
        JKMount       /opendap/* ajp13

        # Use a secure reverse proxy to enable mapping the old URL's too tomcat.
        RewriteRule   ^/cgi-bin/nph-dods(.*) http://localhost:8080/opendap/hyrax/$1  [P]

All of this could be added directly to Apache's *http.conf* file, and is
included in it by the line that you added earlier that looked like this:

`   Include /usr/local/apache-tomcat-5.5.15/conf/jk/mod_jk.conf`

Save your files, restart Tomcat and Apache and, that's it...

Don't you think just using proxies to do the whole thing is simpler?