<font color="red">These are old instructions for integrating Apache and
Tomcat.</font> If you're working with Apache 2.2 or newer, see [Hyrax -
Apache Integration](Hyrax_-_Apache_Integration "wikilink").

## Overview

If you installing a DAP server for the first time, or if you are not
concerned about the URL's for your data changing when you install Hyrax,
then just run Tomcat as a standalone server. It can even be configured
to operate on port 80, just like Apache. If however you want to

- Keep your existing data access URL's intact
- Use Apache security ( SSL authentication etc.) from within Tomcat
- Work with the load balancing features found in Apache and Tomcat
- Block direct access to Hyrax/Tomcat via a firewall and Use Apache to
  manage access.

Then, read on.

Many people deploying Hyrax have been using previous versions of the
OPeNDAP servers with their data. These OPeNDAP servers required the
Apache web server and utilized CGI to deliver their functionality. This
is no longer the case, as Hyrax is a Java servlet designed to run in
conjunction with Tomcat. However many OPeNDAP administrators may wish to
keep the URL hierarchies of their existing systems unchanged and simply
replace their existing implementation of the OPeNDAP server with the new
server. To this end Tomcat needs to be integrated with the Apache
installation. The directions here offer methods for integrating Hyrax
with Apache version 2.x. If you are using earlier versions of Apache
(1.x or older) you might wish to consider upgrading Apache to a more
current version (version 2.x really is better...), otherwise these
instructions may or may not be helpful.

There are 2 basic steps to integrating Hyrax (and Tomcat) with Apache
and Tomcat. The first step is to integrate Tomcat with Apache using the
[Apache Tomcat
Connector](http://tomcat.apache.org/connectors-doc/index.html), the
second step is to map your old OPeNDAP (or even DODS) URLs to the new
service using the Apache rewrite module (mod_rewrite).

Tomcat must be running and listening on a port (which one depends on how
you set things up) for it to interact with Apache.

### mod_jk

Most discussions of integrating Tomcat and Apache start (and often end
with) an Apache module called mod_jk, which allows Tomcat to work as
part of Apache. However, mod_jk is generally not installed by default
with Apache (while the rewrite and proxy modules are) so you will need
to get (possibly compile) and install mod_jk .

### mod_rewrite

The simplest method for integrating Tomcat with Apache while preserving
preexisting URL hierarchies is to use a mod_rewrite to map your old data
URL's into the new service.

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

## Install Software Components

### Install Hyrax

Install Hyrax as described in the [installation
instructions](Hyrax_-_Installation_Instructions "wikilink"). No matter
how you want things to end up you have to do this first.

### Install mod_jk

**Before proceeding make sure you have installed the *mod_jk* library in
your Apache *modules* directory.** Please read and follow the [Apache
instructions to install
mod_jk](http://tomcat.apache.org/connectors-doc/index.html) into your
instance of Apache.

##### OS-X Tip

If you end up building mod_jk from source for an installation on OS-X
10.5.x (Leopard) you may find that the configure script fails to
correctly detect the system architecture. This will cause Apache to
complain when you try to use mod_jk:


*httpd: Syntax error on line 512 of /private/etc/apache2/httpd.conf:
Cannot load /usr/libexec/apache2/mod_jk.so into server:
dlopen(/usr/libexec/apache2/mod_jk.so, 10): no suitable image found. Did
find:\n\t/usr/libexec/apache2/mod_jk.so: mach-o, but wrong architecture*

This can be cured by adding the architecture designation via a compiler
flags switch on the configure script:


*./configure CFLAGS="-arch x86_64" ...*

(Thanks to:
[lo-fi](http://blog.lo-fi.net/2007/10/leopard-for-web-developer-installing.html)
for the thread with the tip!)

### Install mod_rewrite

Depending on your Apache installation you may need to rebuild/compile
Apache to enable *rewrite*. You should first look at the *http.conf*
configuration file. Find the *LoadModule* directives and look for the
lines that load the proxy and rewrite modules. They should look
something like this:

`LoadModule rewrite_module modules/mod_rewrite.so`

If you see the module loaded then you're set to go, skip to the next
section.

If not then you will probably need to compile Apache from source and
enable the rewrite and proxy modules.

1.  Make sure you have the Apache source.
2.  Run the Apache configure script with the --enable-rewrite and
    --enable-proxy switches. Minimally your configure command should
    look like this:

    `./configure --enable-rewrite --enable-proxy`

3.  (Re)compile.
4.  (Re)install.

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

## Configure Software Components

### Configure Tomcat

Most distributions of Tomcat come with the AJP connector enabled. You
should check your instance of Tomcat and confirm this.

Look in the Tomcat *server.xml* file to confirm the enablement of the
AJP 1.3 connector. Inside of *\$CATALINA_HOME/conf/server.xml* you may
need to uncomment (or even add) the AJP connector definition inside of
the service named Catalina:

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

### Configure mod_jk

The Apache Tomcat Connector (aka mod_jk) has a large list of features
(such as load balancing options) that are beyond the scope of this
discussion. We will review a simple configuration to get things started.

#### workers.properties file

Mod_jk uses a **workers.properties** file that defines the workers that
are connecting to Tomcat. Here is a minimum example of a
workers.properties file for Hyrax:

`# Define 1 real worker`
`worker.list=hyrax`
`#`
`# Set properties for hyrax worker`
`#`
`# Define it as an AJP1.3 protocol worker.`
`worker.hyrax.type=ajp13`
`# Hostname or IP address for the tomcat instance that is running Hyrax`
`worker.hyrax.host=localhost`
`# Define the port for the AJP connector for the Tomcat instance`
`worker.hyrax.port=8009`

Remember - the *workers.properties* file must direct your mod_jk worker
to the AJP port being used by your Tomcat instance as defined in the
*\$CATALINA_HOME/conf/server.xml* file. This is typically NOT port 8080
but port 8009.

The workers.properties can be placed anywhere on the Apache host system,
but is typically located in */etc/httpd/conf/workers.properties*

#### httpd.conf

Once you have saved the workers.properties file you will need to edit
the Apache configuration, http.conf. The httpd.conf file is typically
located in one of:

- /etc/httpd/conf/
- /etc/httpd2/conf/
- /usr/local/apache/conf/
- /etc/apache2/ (on my OS-X 10.5 system)

At the bottom of **httpd.conf** you will need to add the following,
localized to you specific system:

`   # Load mod_jk module. This location was determined when you installed mod_jk `
`   LoadModule jk_module   `*`location/of/`*`mod_jk.so`
`   # Where to find workers.properties`
`   JkWorkersFile /etc/httpd/conf/mod_jk/workers.properties`
`   # Where to put jk shared memory`
`   JkShmFile     /var/log/httpd/mod_jk.shm`
`   # Where to put jk logs`
`   JkLogFile     /var/log/httpd/mod_jk.log`
`   # Set the jk log level [debug/error/info]`
`   JkLogLevel    info`
`   # Select the timestamp log format`
`   JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "`
`   # Send servlet for context /opendap to worker named hyrax`
`   JkMount  /opendap* hyrax`

- You may not need to the line:



`LoadModule jk_module `*`location/of/`*`mod_jk.so''`

If it already exists elsewhere in httpd.conf

- The last line of the *mod_jk* configuration:

`   JkMount  /opendap/* hyrax`


Maps all incoming requests whose local URL matches **opendap/\*** to the
*hyrax* worker defined in the **workers.properties** file.

This should complete the configuration of mod_jk. Restart Apache
(*apachectl -k restart*) and see if in fact hitting your Apache server
at the opendap context brings you to your Hyrax service:


http://my.host/opendap/

If not then you will need to trouble shoot your mod_jk installation and
configuration.

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

## Preserving old DAP/DODS data URL's

Using the Apache rewrite module (mod_rewrite) we can map olds data URL's
that were serviced by early versions of the DAP server to the new Hyrax
installation.

### mod_rewrite

**Advice**: *If you are not familiar with [Apache mod_rewrite go read
about it now](http://httpd.apache.org/docs/2.2/mod/mod_rewrite.html),
before you do anything else.*

Once you have installed Apache with the *rewrite* module (mod_rewrite)
enabled you will need to edit Apache's httpd.conf file. Add the
following lines:

        # Enable the rewrite module
        RewriteEngine on
        # Target it's logging somewhere useful
        RewriteLog /var/log/httpd/rewrite.log
        # Turn on logging (Set to 0 to disable)
        RewriteLogLevel 2

        # Uses a reverse proxy to enable mapping old OPeNDAP URL's to Tomcat.
        RewriteRule ^/cgi-bin/nph-dods(.*) /opendap/$1 [P]

Assuming that your old OPeNDAP server was accessed via
*<http://your.server/cgi-bin/nph-dods/>* this will map it to the AJP
Connector (mod_jk) that is tied (via the *hyrax* worker defined in the
**workers.properties** file) to the Hyrax service running in the Tomcat
engine. You will probably need to rewrite rule to suit your previous
server configuration. If you used *Alias* or *AliasMatch* with your old
server, add more *RewriteRule* directives to get that same behavior.

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

## Apache AddEncoding directives

**Note**: If you have *AddEncoding* directives in your Apache
configuration, those will likely need to be replaced with *AddType*. If
present, the *AddEncoding* directives will cause Apache 2.x to report
that any page, such as the HTML form interface, is compressed, even
though it is not. This problem can be very hard to track down.

        # AddEncoding allows you to have certain browsers uncompress
        # information on the fly. Note: Not all browsers support this.
        # Despite the name similarity, the following Add* directives have nothing
        # to do with the FancyIndexing customization directives above
        #
        # AddEncoding x-compress .Z
        # AddEncoding x-gzip .gz .tgz
        #
        # If the AddEncoding directives above are commented-out, then you
        # probably should define those extensions to indicate media types:
        #
        AddType application/x-compress .Z
        AddType application/x-gzip .gz .tgz

Restart Apache (assuming Tomcat is already running) and you should be on
your way.