**When you install Hyrax for the first time it is pre-configured to
serve test data sets that come with each of the installed data handlers.
This will allow you to test the server and make sure it is functioning
correctly. After that you can customize it for your data.**

## [BES configuration](Hyrax_-_BES_Configuration "wikilink")

## [OLFS configuration](Hyrax_-_OLFS_Configuration "wikilink")

## [THREDDS Configuration](Hyrax_-_THREDDS_Configuration "wikilink")

## [Logging configuration](Hyrax_-_Logging_Configuration "wikilink")

## Robots

To correctly deploy a robots.txt file for Hyrax, you must correctly
deploy it for Tomcat. This means that your robots.txt file must be
accessible here:

`   `[`http://you.host:port/robots.txt`](http://you.host:port/robots.txt)

For example:

`   `[`http://www.opendap.org/robots.txt`](http://www.opendap.org/robots.txt)

Placing it lower in the URL path doesn't seem to work.

In order to get Tomcat to serve the file from that location you must
place it in **\$CATALINA_HOME/webapps/ROOT**

If you still find that you system is being cruelly burdened with robot
traffic then you might want to try the [BotBlocker handler for the
OLFS](Hyrax_-_OLFS_Configuration#BotBlocker_.28optional.29 "wikilink").