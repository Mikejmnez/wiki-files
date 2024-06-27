We see logging activities falling into two categories:

**Access Logging** - Is used to monitor server usage, server
performance, and to see which resources are receiving the most
attention. Tomcat has a very nice built in Access Logging mechanism, all
you have to do is turn it on.

**Informational and debug logging** - Most developers (myself included)
rely on a collection of imbedded "instrumentation" that allows them to
monitor their code and see what parts are being executed. Typically we
like to design this instrumentation so that it can be enabled/disabled
at runtime. Hyrax has this type of debugging instrumentation Hyrax ships
with it disabled, but you could enable it. If you were to encounter an
internal problem with Hyrax I would have you enable different aspects of
the instrumentation at you site so that we could review the output to
determine the issue.

------------------------------------------------------------------------

# Access Logging

Many people will want to record access logs for their Hyrax server.
**We** want you to keep access logs for your Hyrax server. The easiest
way to get a simple access log for Hyrax is to utilize the
Tomcat/Catalina [Valve
Component](http://tomcat.apache.org/tomcat-5.0-doc/config/valve.html)

## AccessLogValve

Since Hyrax's public facade is provided by the OLFS running inside of
the Tomcat servlet container you may utilize Tomcat's handy access
logging which relies on the [org.apache.catalina.valves.AccessLogValve
class](http://tomcat.apache.org/tomcat-5.0-doc/catalina/docs/api/org/apache/catalina/valves/AccessLogValve.html)
class. By default Tomcat comes with this turned off.

To turn it on:

**1.** Locate the file \$CATALINA_HOME/conf/servlet.html.

**2.** Find the commented out section for the access log inside the
<Host> element. The server.xml file contains a good deal of comments,
both for instruction and containing code examples. The part you are
looking for is nested inside of the <Service> and the <Engine> elements.
Typically it will look like:

    <Service ...>
        .
        .
        .
        <Engine...>
            .
            .
            .
            <Host name="localhost" appBase="webapps"
                unpackWARs="true" autoDeploy="true"
                xmlValidation="false" xmlNamespaceAware="false">
                .
                .
                .
                <!-- Access log processes all requests for this virtual host.
                     By default, log files are created in the "logs"
                     directory relative to $CATALINA_HOME.  If you wish, you can
                     specify a different directory with the "directory"
                     attribute.  Specify either a relative (to $CATALINA_HOME)
                     or absolute path to the desired directory. -->

                <!--
                <Valve className="org.apache.catalina.valves.AccessLogValve"
                       directory="logs"  prefix="localhost_access_log." suffix=".txt"
                       pattern="common" resolveHosts="false"/>
                --/>
                .
                .
                .
            </Host>
            .
            .
            .
        </Engine>
        .
        .
        .
    </Service>

You can uncomment the \<*Valve*\> element to enable it and you can
change the values of the various attributes to suite your localization.
For example:

                <Valve className="org.apache.catalina.valves.AccessLogValve"
                       directory="logs"
                       prefix="access_log."
                       suffix=".log"
                       pattern="%h %l %u %t &quot;%r&quot; %s %b %D"
                       resolveHosts="false"/>

**3.** Save the file.

**4.** Restart Tomcat.

**5.** Go read yer log files!

Of note is the *pattern* attribute which allows you to customize the
content of the access log entries. It is documented in the [javadocs for
Tomcat/Catalina](http://tomcat.apache.org/tomcat-5.0-doc/catalina/docs/api/index.html),
as part of the
[org.apache.catalina.valves.AccessLogValve](http://tomcat.apache.org/tomcat-5.0-doc/catalina/docs/api/org/apache/catalina/valves/AccessLogValve.html)
class, and here in the [Server Configuration
Reference](http://tomcat.apache.org/tomcat-5.0-doc/config/valve.html).
The pattern shown above will provide log output that looks like this:

            69.59.200.52 - - [05/Mar/2007:16:29:14 -0800] "GET /opendap/data/nc/contents.html HTTP/1.1" 200 13014 234
            69.59.200.52 - - [05/Mar/2007:16:29:14 -0800] "GET /opendap/docs/images/logo.gif HTTP/1.1" 200 8114 2
            69.59.200.52 - - [05/Mar/2007:16:29:51 -0800] "GET /opendap/data/nc/TestPatDbl.nc.html HTTP/1.1" 200 11565 137
            69.59.200.52 - - [05/Mar/2007:16:29:56 -0800] "GET /opendap/data/nc/data.nc.ddx HTTP/1.1" 200 2167 121

Where the last column is the time in milliseconds it took to service the
request and the next to the last column is the number of bytes returned.

------------------------------------------------------------------------

# Informational and Debug Logging (Using the Logback implementation of Log4j)

In general you shouldn't have to modify the default logging
configuration for Hyrax. It may become necessary, if you encounter
problems, but otherwise I suggest you leave it be.

Having said that: Hyrax uses the Logback logging package to provide an
easily configurable and flexible logging environment. All "console"
output is routed through the Logback package and can be controlled using
the Logback configuration file.

There are several logging levels available:

- **TRACE**
- **DEBUG**
- **INFO**
- **WARN**
- **ERROR**
- **FATAL**

Hyrax ships with a default logging level of **ERROR**.

Additionally Hyrax maintains it's own access log using Logback .

**We strongly recommend that you take the time to [read about Logback
and Log4j](http://logback.qos.ch/manual/index.html) before you attempt
to manipulate the Logback configuration.**

## Configuration File Location

Logback gets it's configuration from an XML file. Hyrax locates this
file in the following manner:

1.  Checks the <init-parameter> list for the hyrax servlet (in the
    web.xml) for a an <init-parameter> called "logbackConfig". If found,
    the value of this parameter is assumed to be a fully qualified path
    name for the file. This can be used to specify alternate Logback
    config files. Note that this configuration will not be persistent
    across new installations of Hyrax. We do **not** recommend setting
    this parameter as doing so is not persistent: It will be overridden
    the next time the Web ARchive file is deployed.

2.  Failing 1, Hyrax then checks in the persistent content directory
    ([set by either the OLFS_CONFIG_DIR environment variable or in
    /etc/olfs](Hyrax_-_OLFS_Configuration#OLFS_Configuration_Location "wikilink"))
    for the file "logback-test.xml". If this file is present then it
    will be used to configure logging, and new installations of Hyrax
    will detect ad use this logging configuration automatically.

3.  Failing 2, Hyrax then checks in the persistent content directory
    ([set by either the OLFS_CONFIG_DIR environment variable or in
    /etc/olfs](Hyrax_-_OLFS_Configuration#OLFS_Configuration_Location "wikilink"))
    for the file "logback.xml". If this file is present then it will be
    used to configure logging, and new installations of Hyrax will
    detect ad use this logging configuration automatically.

4.  Failing 3, Hyrax falls back to the logback.xml file shipped with the
    distribution which is located in the
    \$CATALINA_HOME/webapps/opendap/WEB-INF directory. Changes made to
    this file will be lost when a new version of Hyrax is installed or
    the opendap.war Web ARchive file is redeployed..


So - if you want to customize your Hyrax logging and have it be
persistent, do it by copying the distributed logback.xml file
(\$CATALINA_HOME/webapps/opendap/WEB-INF/logback.xml) to the in the
persistent content directory ([set by either the OLFS_CONFIG_DIR
environment variable or in
/etc/olfs](Hyrax_-_OLFS_Configuration#OLFS_Configuration_Location "wikilink"))
and editing that copy.

## Configuration

Did you [read about LogBack and
Log4j](http://logback.qos.ch/manual/index.html)? Great!

There are a number of *Appenders* defined in the Hyrax **log4j.xml**
file:

- **stdout** - Loggers using this Appender will send everything to the
  console/stdout - which in a Tomcat environment will get shunted into
  the file *\$TOMCAT_HOME/logs/catalina.out*
- **devNull** - Loggers using this Appender will not log. All messages
  will be discarded. This is the Log4j equivalent of piping your output
  into */dev/null* in a UNIX environment.
- **ErrorLog** - Loggers using this Appender will have their log output
  placed in the error log file in the persistent content directory:
  *\$TOMCAT_HOME/content/opendap/logs/error.log*
- **HyraxAccessLog** - Loggers using this Appender will have their log
  output placed in the access log file in the persistent content
  directory: *\$TOMCAT_HOME/content/opendap/logs/HyraxAccess.log*

The default configuration pushes **ERROR** level (and higher) messages
into the **ErrorLog**, and logs accesses using **HyraxAccessLog**. You
can turn on debugging level logging by changing the log level to
**DEBUG** for the software components you are interested in. All of the
OPeNDAP code is in the "opendap" package. Thus:


        <logger name="opendap" level="error"/>
            <appender-ref ref="ErrorLog"/>
        </logger>

Will cause all log messages of **ERROR** level or higher to be sent to
the error log.

This configuration:

        <logger name="opendap" level="info"/>
            <appender-ref ref="stdout"/>
        </logger>

Will cause all messages of level **INFO**' or higher to be sent to
**stdout**, which (in Tomcat) means that they will get stuck in the file
*\$TOMCAT_HOME/logs/catalina.out*

Be sure to get in touch if you have further questions about the logging
configuration.