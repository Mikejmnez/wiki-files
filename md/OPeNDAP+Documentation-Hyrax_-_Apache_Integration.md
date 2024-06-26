## Overview

The problem of linking Tomcat with Apache has been greatly reduced as of
Apache 2.2. In previous incarnations of Apache & Tomcat, it was fairly
complex. To see how to solve the problem for older versions of Apace,
see the [old Apache Integration
instructions](old_Apache_Integration_instructions "wikilink"). What
follows are the instructions for Apache 2.2 and Tomcat 6.x.

## Prerequisites

- Apace 2.2 or greater
- Tomcat 6.x or greater
- mod_proxy_ajp installed in Apache (typically this is present in 2.2+)

## Connecting Tomcat to Apache

### Tomcat

You have to create the AJP connector in the *conf/server.xml* file:

``` xml
    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <Connector
        port="8009"
        protocol="AJP/1.3"
        redirectPort="443" <!-- Redirect to the Apache Web Server secure port -->
        scheme="https" <!-- Use TLS to connect -->
        address="127.0.0.1" <!-- Only allow connections from this host -->
        <!-- Setting tomcatAuthentication to 'false' will allow tomcat web applications
                to get user session information from Apache, such as uid and other user properties. -->
        tomcatAuthentication="true"
        />
```

This line will enable AJP connections to the 8009 port of your tomcat
server (localhost for example).

### Apache

In the example below, pay special attention to the *protocol* part of
the proxy URL - it uses **ajp://** and not 'http://'.
Add this to Apache's *httpd.conf* file:

``` apache
<Proxy *>
    AddDefaultCharset Off
    Order deny,allow
    Allow from all
</Proxy>

ProxyPass /opendap ajp://localhost:8009/opendap
ProxyPassReverse /opendap ajp://localhost:8009/opendap
```

NB: It's possible to embed these in a *VirtualHost* directive.

### How It Works

ProxyPass and ProxyPassReverse are classic reverse proxy directives used
to forward the stream to another location. *ajp://...* is the AJP
connector location (your tomcat's server host/port)

A web client will connect through HTTP to *http://localhost/* (supposing
your apache2 server is running on localhost), the *mod_proxy_ajp* will
forward you request transparently using the AJP protocol to the tomcat
application server on localhost:8009.

## Apache Compressed Responses

Many OPeNDAP clients accept compressed responses. This can greatly
increase the efficiency of the client/server interaction by diminishing
the number of bytes actually transmitted over "the wire". Compression
can reduce the number of bytes transmitted by an order of magnitude for
many datasets!

Tomcat provides native compression support for the GZIP compression
mechanism, however it is NOT turned on by default. More perversely, even
if you have configured Tomcat to provide compressed responses, if you
are using AJP to proxy Tomcat through the Apache web server compression
will not be enabled unless you configure the Apache web server to
compress responses. This is because Tomcat NEVER compresses responses
sent over AJP.

When you configure your Apache web server to provide compressed
responses you will probably want to make sure that Apache doesn't apply
compression to images (In general images are already compressed and
there is little to gain by attempting to compress them and a lot of CPU
cycles to burn if you try)

[Apache compression
module](http://httpd.apache.org/docs/2.0/mod/mod_deflate.html)

[AskApache Speed
Tips](http://www.askapache.com/htaccess/apache-speed-compression.html)

[BetterExplained explains Apache
Compression](http://betterexplained.com/articles/how-to-optimize-your-site-with-gzip-compression/)

### httpd.conf

You will need to add (something like) the following to your Apache web
server's httpd.conf file:

``` apache
#
# Compress everything except images.
#
<Location />
    # Insert filter
    SetOutputFilter DEFLATE

    # Netscape 4.x has some problems...
    BrowserMatch ^Mozilla/4 gzip-only-text/html

    # Netscape 4.06-4.08 have some more problems
    BrowserMatch ^Mozilla/4\.0[678] no-gzip

    # MSIE masquerades as Netscape, but it is fine
    # BrowserMatch \bMSIE !no-gzip !gzip-only-text/html

    # NOTE: Due to a bug in mod_setenvif up to Apache 2.0.48
    # the above regex won't work. You can use the following
    # workaround to get the desired effect:
    BrowserMatch \bMSI[E] !no-gzip !gzip-only-text/html

    # Don't compress images
    SetEnvIfNoCase Request_URI \
    \.(?:gif|jpe?g|png)$ no-gzip dont-vary

    # Make sure proxies don't deliver the wrong content
    Header append Vary User-Agent env=!dont-vary
</Location>
```

</font>

## Apache Authentication

Hyrax may deployed into service stacks in which *httpd* is expected to
handle the work of authenticating users. In order for Tomcat (and thus
Hyrax) to be able to receive the users login name and attributes from
*httpd* the following things need to be done to the Tomcat
configuration.

In the *\$CATALINA_HOME/conf/server.xml* file the default definition of
the AJP connector typically looks like:

``` xml
    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
```

This line may be "commented out," with on a line after. If so, remove
those lines. If you cannot find the AJP connector element, simply create
it from the code above. You will need to add several attributes to the
Connector element.

- Set the `tomcatAuthentication` attribute to "false", this must be done
  in order to receive authentication information from Apache.
- Configure the connector to use SSL - If your Apache web server is
  using SSL/HTTPS (and it should be), you need to tell Tomcat about that
  fact so that it can construct internal URLs correctly.
  - Set the `scheme` attribute to "https".
  - Set the `proxyPort` attribute to Apache httpd's secure socket,
    typically "443" (This ensures that secure traffic gets routed
    through Apache httpd and and then through the AJP connector to
    Tomcat, allowing httpd's authentication/authorization stack to be
    invoked on the request).
- Restrict access to the AJP Connector. By disabling access to the
  connector from anywhere but the local system you prevent system
  probing from the greater world. To do this, set the `address`
  attribute to "127.0.0.1".

When you are finished making changes, your connector should look
something like this:

``` xml
    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <Connector
        port="8009"
        protocol="AJP/1.3"
        redirectPort="443"
        scheme="https"
        address="127.0.0.1"
        tomcatAuthentication="false"
        />
```

Restart Tomcat to load the new configuration. Now Tomcat/Hyrax should
see all of the authentication attributes from *httpd*.

NB: You may wish review Tomcat documentation for the AJP Connector as
there many attributes/options that can be used to tune performance.
[Here's a link to the Tomcat 7 AJP Connector
docs](http://tomcat.apache.org/tomcat-7.0-doc/config/ajp.html)