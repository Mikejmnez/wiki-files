# Installing the OPeNDAP Relational Database Server

The OPeNDAP Relational Database Server (DRDS) allows data managers to
serve data stored in relational DBMS systems, such as Oracle, SQL
Server, or mySQL. The DRDS is written in Java, and uses the Java
Database Connectivity API (JDBC) to connect with the DBMS.

As of version 1.1, the DRDS cannot issue table join commands. This means
it cannot serve multiple tables connected by common keys. If your DBMS
supports views, you can use these to allow users to query multiple
tables. If your DBMS does not support views, you need to put your data
into a single table to use DRDS.

Here is a brief description of how to set up the DRDS. These
instructions are for DRDS version 1.1 and later.

There are a certain number of prerequisites. The variety of
installations and software make it impossible to describe here how to
install these things and figure out the relevant parameters.
Nonetheless, they must be installed. Though we can't offer instruction,
we can at least offer a checklist. Here is a list of the software that
must be installed, and the parameters you must determine:

1.  Java. You must have at least Java version
    1.4.([10](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink"))
2.  Data in a database that supports Java Database Connectivity (JDBC).
    If your data is in multiple tables, you will need to prepare a view
    of the data to be served.
3.  A web server that can execute servlets. DRDS has been extensively
    tested with the [\| Tomcat](http://jakarta.apache.org) servlet
    engine, which can be run either as a module to Apache, or by
    itself.\indc{Tomcat!Java servlet engine}
4.  The JDBC client library (also called the "driver") for your DBMS.
    This is specific to the kind of DBMS. That is, an Oracle JDBC driver
    won't necessarily work with a MySQL DBMS.
5.  You must know the name of the driver. This is a string of characters
    identifying the particular JDBC driver in use. For Oracle, it reads
    something like this:
    <font color='green'>oracle.jdbc.driver.OracleDriver</font>.
6.  Your DBMS must have a user with enough privilege to read data from
    the network. The DRDS is a read-only application, and cannot issue
    commands that will try to change the data.
7.  You must know the "Connection URL" that will be used to contact your
    DBMS. This is a long and awkward-looking string that identifies the
    machine, the driver, the port, and some other information used by
    the JDBC driver to contact your DBMS. For a recent Oracle
    installation, the Connection URL looked like this:
         jdbc:oracle:thin:@dods.org:1521:orcl

    Figuring out the Connection URL will probably be the most
    challenging part of installing DRDS. It's not a great deal of help
    to say so, but we're hoping to make up in sympathy what we cannot
    offer in concrete assistance.

After you've checked off the items in this list, download the DRDS
distribution from the \[\[<http://opendap.org>\| OPeNDAP Home\] or the
\[\[<http://opendap.org/home/swJava1.1>\| OPeNDAP Java\]. For a basic
installation, you want the file called
<font color='green'>dods.war</font>. To install the server, put this
entire file into the "webapps" directory of your servlet engine (e.g.
Tomcat). When you restart the servlet engine, it will unpack the archive
into the directory <font color='green'>webapps/dods</font>. If you have
installed version 1.1.7 or newer, then the DODS Test Servlet (DTS)
should be running after you get your servlet engine restarted. You
should be able to access it at:
<font color='green'>[http://yourserver:port/dods/dts/](http://yourserver:port/dods/dts/)</font>

To configure the servlets, you'll need to edit the file
<font color='green'>webapps/dods/WEB-INF/web.xml</font>. This is
described in the next two sections. After you finish editing the
configuration file, restart the servlet engine again, and the server
should be running.

## General Servlet Configuration

When installing a server, it's useful to isolate the steps so you're not
trying to tune several parameters at the same time. To assist here, the
DRDS comes with a test server that invents data to return to a client
requesting data. You can use it to test your servlet engine and the
servlet installation, without taxing the JDBC drivers or your DBMS.
We'll get to that in a moment, first let's review some aspects of the
Servlet configuration that are common to all DODS servlets.

The OPeNDAP servlets get their configuration from the servlet's
<font color='green'>web.xml</font>

`file. The default location of the `<font color='green'>`web.xml`</font>` file is (at least in Tomcat 4.1) in `<font color='green'>`$TOMCAT_HOME/webapps/dods/WEB-INF`</font>` (Obviously if the dods directory`
`gets renamed then things change accordingly.)`

Each instance of a servlet running in the opendap servlet area needs an
entry in the <font color='green'>web.xml</font> file. Multiple instances
of a servlet and/or several different servlets can be configured in the
one <font color='green'>web.xml</font> file. For instance you can have a
DTS and 2 instances of the DRDS configured through one
<font color='green'>web.xml</font> file.

Each instance of a servlet needs a unique name which is specified inside
a <font color='green'><servlet></font> element in the
<font color='green'>web.xml</font> file using the
<font color='green'><servlet-name></font> tag. This is a name of
convenience, for example if you where serving data from an ARGOS
satellite you might call that servlet argos.

Additionally each instance of a <font color='green'><servlet></font>
must specify which Java class contains the actual servlet to run. This
is done in the <font color='green'><servlet-class></font> element. For
example the DRDS's class name is
<font color='green'>opendap.servers.sql.drds</font>

Here is a syntax example combining the two previous example values:

    <servlet>
        <servlet-name>argos</servlet-name>
        <servlet-class>opendap.servers.sql.drds</servlet-name>
        .
        .
        .
    </servlet>

This servlet could then be accessed as:

    http://hostname/opendap/servlet/argos

You may also add to the end of the web.xml file a set of
<font color='green'><servlet-mapping></font> elements. These allow you
to abbreviate the URL or the servlet. By placing the servlet mappings:

     <servlet-mapping>
        <servlet-name>argos</servlet-name>
        <url-pattern>/argos</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>argos</servlet-name>
        <url-pattern>/argos/*</url-pattern>
    </servlet-mapping>

At the end of the web.xml file our previous example changes it's URL to:

    http://hostname/opendap/argos

Eliminating the need for the word servlet in the URL. For more on the
<font color='green'><servlet-mapping></font> element see the
Jakarta-Tomcat documentation.

### <font color='green'>\<</font>init-param<font color='green'>\></font> Elements

The OPeNDAP servlets use <font color='green'><init-param></font>
elements inside of each <font color='green'><servlet></font> element to
get specific configuration information.

<font color='green'><init-param></font>'s common to all OPeNDAP servlets
are:

- <font color='green'>DebugOn</font> - This controls output to the
  terminal from which the servlet engine was launched. The value is a
  list of flags that turn on debugging instrumentation in different
  parts of the code. Common values are:
  <font color='green'>showRequest</font>,
  <font color='green'>showResponse</font>,
  <font color='green'>showException</font>, and
  <font color='green'>probeRequest</font>. Other debugging values that
  are specific to each servlet should be documented in each servlets
  javadoc documentation. Example:

<!-- -->


    <init-param>
       <param-name>DebugOn</param-name>
       <param-value>showRequest showResponse</param-value>
    </init-param>

*Default* : If this parameter is not set, or the value field is empty
then debugging instrumentation is not turned on.

- <font color='green'>DDScache</font> - This is should be set to the
  directory containing the DDS files for the data sets used by the
  servlet. Some servlets have been developed that do not use DDS's that
  are cached on the disk, however the default behavior is for the
  servlet to load DDS images from disk. Example:

<!-- -->


    <init-param>
       <param-name>DDScache</param-name>
       <param-value>/usr/OPeNDAP/sdds-testsuite/dds/</param-value>
    </init-param>

*Default* : If this parameter is not set (does not appear in as an
<font color='green'><init-param></font>) then it is set to
<font color='green'>\$TOMCATHOME/webapps/opendap/datasets/servlet-name/dds</font>
(where servlet-name is the same as the name specified in the
<font color='green'><servlet-name></font> element of the servlet
configuration)

- <font color='green'>DAScache</font> - This is should be set to the
  directory containing the DAS files for the data sets used by the
  servlet. Some servlets have been developed that do not use DAS's that
  are cached on the disk, however the default behavior is for the
  servlet to load DAS images from disk. Example:

<!-- -->


    <init-param>
       <param-name>DAScache</param-name>
       <param-value>/usr/OPeNDAP/sdds-testsuite/das/</param-value>
    </init-param>

*Default* : If this parameter is not set (does not appear in as an
<font color='green'><init-param></font>) then it is set to
<font color='green'>\$TOMCATHOME/webapps/opendap/datasets/servlet-name/das</font>
(where servlet-name is the same as the name specified in the
<font color='green'><servlet-name></font> element of the servlet
configuration) .

- <font color='green'>INFOcache</font> - This is should be set to the
  directory containing the files used by the ".info" service for the
  servlet. This directory should contain any data set specific
  "over-ride" files (see below), any data set specific additional
  information files (see below), and any servlet specific information
  files(see below). Example:

<!-- -->


    <init-param>
      <param-name>INFOcache</param-name>
      <param-value>/usr/OPeNDAP/sdds-testsuite/info/</param-value>
    </init-param>

*Default* : If this parameter is not set (does not appear in as an
<font color='green'><init-param></font>) then it is set to
<font color='green'>\$TOMCATHOME/webapps/opendap/datasets/servlet-name/info</font>
(where servlet-name is the same as the name specified in the
<font color='green'><servlet-name></font> element of the servlet
configuration)

## DODS Test Server (DTS)

*classname* : <font color='green'>dods.servers.test.dts</font>

This servlet is primarily used to test the core code (in particular the
DAP). This servlet will take any DDS in it's
<font color='green'>DDScache</font> and populate it with invented data
per client request. This allows the testing of unusual DDS structures.
By default the DTS will look in these locations:

    $TOMCAT_HOME/webapps/dods/datasets/dts/dds
    $TOMCAT_HOME/webapps/dods/datasets/dts/das
    $TOMCAT_HOME/webapps/dods/datasets/dts/info

For files containing DDS, DAS, and ancillary information. These
directories come full of test dataset descriptions and metadata in the
distribution (1.1.7 or higher). If the
<font color='green'>web.xml</font> file entry for the DTS contains
entries for any of the <font color='green'>DDScache</font>,
<font color='green'>DAScache</font>, or the
<font color='green'>INFOcache</font> directories then the DTS will look
there instead of in the defalut location(s).

### DTS Configuration

The DTS comes configured to startup the first time the servlet's Web
Archive File (WAR file) is unpacked by Tomcat. You may wish to change
the length of the returned Sequences via the information below, but it
is not neccessary.

<font color='green'><init-param></font>'s unique to the DTS:

- <font color='green'>SequenceLength</font> - This
  <font color='green'><init-param></font> sets the number of rows each
  Sequence returned by the DTS will have. Common values are typically
  small (5-100) for simple testing. If you are to testing client code
  against the DTS you may wish to use a large value
  (<font color='green'>\></font>50000) here to check the client's
  ability to handle large Sequences. Example:

<!-- -->

     <init-param>
      <param-name>SequenceLength</param-name>
      <param-value>100</param-value>
    </init-param>

*Default* : Is set to 5 if this parameter is not set (does not appear in
as an <font color='green'><init-param></font>).

- <font color='green'>DebugOn</font> - There are no DTS specific values
  for this <font color='green'><init-param></font>.

Example of web.xml content:

    <servlet>
            <servlet-name>
                    dts
            </servlet-name>
            <servlet-class>
                    dods.servers.test.dts
            </servlet-class>
            <init-param>
                    <param-name>DebugOn</param-name>
                    <param-value>showRequest showResponse </param-value>
            </init-param>
                    <param-name>SequenceLength</param-name>
                    <param-value>showRequest showResponse </param-value>
            </init-param>
                    <param-name>SequenceLength</param-name>
                    <param-value>100</param-value>
            </init-param>
    </servlet>

In this example SequenceLength gets set to 100.

## DODS Relational Database Server (DRDS)

*classname* : <font color='green'>dods.servers.sql.drds</font>

This DODS server serves data from a relational database to DODS clients.

The DRDS uses the JDBC interface to connect to the database. This means
that to use this server you will need to locate and install JDBC drivers
for your database system. (This requirement is for the server only, the
DODS clients that use the server need know nothing about JDBC or SQL).
Here are some links that should lead you to the JDBC support pages for
some of the more common RDBM's:

1.  MySQL JConnector Site:

[<cite><http://www.mysql.com/products/connector/j/index.html></cite>](http://www.mysql.com/products/connector/j/index.html)

1.  PostgreSQL JDBC site:
    \[<http://jdbc.postgresql.org/><cite><http://jdbc.postgresql.org/></cite>\]
    Additional PostgreSQL JDBC docs:
    \[<http://developer.postgresql.org/docs/postgres/jdbc.html><cite><http://developer.postgresql.org/docs/postgres/jdbc.html></cite>\]
2.  Oracle's SQLJ, JDBC, \\ JPublisher site:
    \[<http://www.oracle.com/technology/tech/java/sqlj_jdbc/index.html><cite><http://www.oracle.com/technology/tech/java/sqlj_jdbc/index.html></cite>\]
3.  Informix JDBC Drivers site:
    \[<http://www-306.ibm.com/software/data/informix/tools/jdbc/><cite><http://www-306.ibm.com/software/data/informix/tools/jdbc/></cite>\]

Most modern database vendors usually provide JDBC drivers for their
product. The glaring exception has been Microsoft, which isn't
surprising as they have made no bones about wanting to kill Java. As of
the release of MSSQL-Server 2000, Microsoft appears to be offering a
JDBC driver for Win2000/XP systems. I have developed using MSSQL-Server
for some time (still do actually.) and I have been using a purchased set
of drivers from DataDirect Technologies (formerly known as Merant
DataDirect). I am using their "SequeLink" product and it's been working
great. Find it, and the Microsoft stuff, at the following links:

1.  Microsoft SQL-Server 2000 JDBC Drivers:
    \[[http://www.microsoft.com/downloads/details.aspx?FamilyID=86212d54-8488-481d-b46b-af29bb18e1e5\\DisplayLang=en](http://www.microsoft.com/downloads/details.aspx?FamilyID=86212d54-8488-481d-b46b-af29bb18e1e5\&DisplayLang=en)<cite>
    <http://www.microsoft.com/></cite>\]
2.  Data Direct Technologies:
    \[<http://www.datadirect.com/index.ssp><cite><http://www.datadirect.com/index.ssp></cite>\]

In this release the DRDS does not support SQL JOIN operations. Each
database table must appear as a DODS Sequence data type in its own DDS
file. If your data crosses multiple tables then you will need to make a
"view" or a "virtual table" in the database in order to serve the data
with the DRDS. This situation will improve in the next major revision.
(I have an as yet un-implemented plan to allow the DRDS to support SQL
JOIN operations.)

### DRDS Datatype Translation

Since the DRDS is reading data from a relational database through a JDBC
connection it is important to note that their are several layers of type
translation happening in this:

Database <font color='green'>-\></font> JDBC
<font color='green'>-\></font> Java <font color='green'>-\></font> DAP2

The Database types are the native types for the particular database that
is being read from. The translation from
Database<font color='green'>-\></font>JDBC is handled before we get to
the data (most likely by the JDBC Drivers). Our mapping of JDBC type to
DAP2 types (the intermediate Java types happen in the process) can be
seen in \tableref{tab,jdbc-mapping}. The mappings are handled in the
read() method for each of the corresponding DAP2 data types.

### DRDS Configuration

To install the real data in your servlet engine, you will have to
complete the installation of JDBC, make sure your DBMS is correctly
configured, and reconfigure the DRDS to use the JDBC library to find
your data. Before starting this part, make sure that the JDBC drivers
are properly installed.

Next modify your web.xml to include the <init-param> elements listed
below. Look carefully at the example web.xml section provided below.

<font color='green'><init-param></font>'s unique to the DRDS:

- <font color='green'>JDBCdriver</font> - This must be set to the fully
  qualified Java class name of the JDBC drivers that the servlet is to
  use to make the JDBC connection to the DBMS. In my servlet that is
  using the Merant DataDirect JDBC drivers the class name of the JDBC
  driver is com.merant.sequelink.jdbc.SequeLinkDriver Example:

<!-- -->


    <init-param>
       <param-name>JDBCDriver</param-name>
       <param-value>com.merant.sequelink.jdbc.SequeLinkDriver</param-value>
    </init-param>

*Default* : This is a required <font color='green'><init-param></font>,
there is no default value.

- <font color='green'>JDBCconnectionURL</font> - This is the connection
  URL (aka the connection string) that the DRDS is to use to connect to
  to the DBMS. This is usually defined by the developers of the JDBC
  driver. Read the documentation for the JDBC driver that you are using.
  It is not always easy to ascertain for a particular installation. For
  example, in my server that is using the Data Direct drivers the value
  is set to: <font color='green'>
  jdbc:sequelink://sugar.oce.orst.edu:19996</font> If you are stumped
  get in touch with me(ndp@coas.oregonstate.edu) and I'll try to help.
  Example:

<!-- -->


    <init-param>
       <param-name>JDBCconnectionURL</param-name>
       <param-value>jdbc:sequelink://foogoo.oce.orst.edu:18796</param-value>
    </init-param>

*Default* : This is a required <font color='green'><init-param></font>,
there is no default value.

- <font color='green'>JDBCusername</font> - This is the user name for
  the DBMS that the JDBC connection will be made under. This is often
  set to "guest". Example:

<!-- -->


    <init-param>
      <param-name>JDBCusername</param-name>
      <param-value>guest</param-value>
    </init-param>

*Default* : This is a required <font color='green'><init-param></font>,
there is no default value.

- <font color='green'>JDBCpassword</font> - The password associated with
  the above username. This is stored as simple text, so make sure that
  the JDBC user doesn't have any significant privileges! If there is no
  password required, you must still set the this
  <font color='green'><init-param></font>, just leave the value element
  empty. Example:

<!-- -->


    <init-param>
      <param-name>JDBCpassword</param-name>
      <param-value>abracadabra</param-value>
    </init-param>

*Default* : This is a required <font color='green'><init-param></font>,
there is no default value.

- <font color='green'>JDBCMaxResponseLength</font> - This limits the
  number of lines that the DRDS will for a given client request. For
  debugging I use 300. For production I use 300000. Your milage may vary
  depending on the amount of memory resources available. You may wish to
  perform some testing on your server to establish an appropriate value.
  *Warning: Small values may lead to incomplete returns from queries.*
  Example:

<!-- -->


    <init-param>
      <param-name>JDBCMaxResponseLength</param-name>
      <param-value>50000</param-value>
    </init-param>

*Default* : : If this parameter is not set (does not appear in as an
<font color='green'><init-param></font>) then it is set to 100.

- <font color='green'>UseDatasetName</font> - This is a (probably
  temporary) hack. Some databases (in particular MS-SQL Server 7.0)
  require that the database name and the owner of the database be
  specified in every variable and table name. This is awkward for the
  current implementation of the DRDS. The work around is to name the
  data set (in the DDS file) with the database name and owner name of
  the table being served. For example in one data set that I server the
  database name is "EOSDB" and the owner of the database is "DBO". I set
  the <font color='green'><init-param></font> UseDatasetName then I
  define the DDS as follows:

<!-- -->


    Dataset {
        Sequence {
                 Float64 battery;
                 Float64 checksum;
                 Float64 data_age;
             } Drifters;
         } EOSDB.DBO;

Thus the hack is invoked. It doesn't matter if the value of this
init-param is empty (although if it's not you should set it to "true"),
it simply needs to appear in the <font color='green'>web.xml</font>
file.If you don't want to use this hack then DO NOT even included the
init-param "UseDatasetName" in the <font color='green'>web.xml</font>
entry for the DRDS. Example:

         <init-param>
             <param-name>JDBCDriver</param-name>
             <param-value>com.merant.sequelink.jdbc.SequeLinkDriver</param-value>
         </init-param>

*Default* : If this <font color='green'><init-param></font> does not
appear in the configuration then the hack is not invoked..

- <font color='green'>DebugOn</font> - Values specific to the DRDS are:
  <font color='green'>JDBC</font>

Example of web.xml content:

    <servlet>
        <servlet-name>
            drds
        </servlet-name>
        <servlet-class>
            dods.servers.sql.drds
        </servlet-class>
        <init-param>
            <param-name>JDBCdriver</param-name>
            <param-value> com.merant.sequelink.jdbc.SequeLinkDriver</param-value>
        </init-param>
        <init-param>
            <param-name>JDBCconnectionURL</param-name>
            <param-value>jdbc:sequelink://sugar:19996</param-value>
        </init-param>
        <init-param>
            <param-name>JDBCusername</param-name>
            <param-value>guest</param-value>
        </init-param>
        <init-param>
            <param-name>JDBCpassword</param-name>
            <param-value></param-value>
        </init-param>
        <init-param>
            <param-name>JDBCMaxResponseLength</param-name>
            <param-value>300</param-value>
        </init-param>
        <init-param>
            <param-name>UseDatasetName</param-name>
            <param-value></param-value>
        </init-param>
        <init-param>
            <param-name>INFOcache</param-name>
            <param-value>/usr/Java-DODS/sdds-testsuite/info/</param-value>
        </init-param>
        <init-param>
            <param-name>DDScache</param-name>
            <param-value>/usr/Java-DODS/sdds-testsuite/dds/</param-value>
        </init-param>
        <init-param>
            <param-name>DAScache</param-name>
            <param-value>/usr/Java-DODS/sdds-testsuite/das/</param-value>
        </init-param>
        <init-param>
            <param-name>DebugOn</param-name>
            <param-value>showRequest showResponse JDBC</param-value>
        </init-param>
    </servlet>

### Configure the Table Data Types

Each database table (including table "views") you wish to serve must be
described with a DDS and DAS. Optionally, you may also include an "info"
message to provide information to users about the source and quality of
the data provided, as well as restrictions on its use. These structures
are contained in text files that must be tailored to each table to be
served.

Here is an example using a table called "b31" that is defined like this
(using Oracle):

<center>

| Name                             | Null?                               | Type                                    |
|----------------------------------|-------------------------------------|-----------------------------------------|
| <font color='green'>ID</font>    | <font color='green'>NOT NULL</font> | <font color='green'>NUMBER</font>       |
| <font color='green'>CLASS</font> | <font color='green'>NOT NULL</font> | <font color='green'>CHAR(1)</font>      |
| <font color='green'>TEXT</font>  | <font color='green'>NOT NULL</font> | <font color='green'>VARCHAR(270)</font> |

</center>

First, determine the DAP data types that correspond with the JDBC data
types (see the data type mapping in \tableref{tab,jdbc-mapping}). These
mappings are used to create a DDS. In the cache directories defined in
the servlet's <font color='green'>web.xml</font> file, create the files
<font color='green'>dds/b31</font> (see the example DDS below),
<font color='green'>das/b31</font> (see the example DAS below), and
<font color='green'>info/b31</font> (if desired).

<center>

| JDBC Data Type                           | DAP2 Data Type                                              |
|------------------------------------------|-------------------------------------------------------------|
| <font color='green'>TINYINT</font>       | <font color='green'>Byte</font>                             |
| <font color='green'>SMALLINT</font>      | <font color='green'>Int16</font>                            |
| <font color='green'>INTEGER</font>       | <font color='green'>Int32</font>                            |
| <font color='green'>BIGINT</font>        | No sensible mapping. Use <font color='green'>Int32</font>.  |
| <font color='green'>REAL</font>          | <font color='green'>Float32</font>                          |
| <font color='green'>FLOAT</font>         | <font color='green'>Float64</font>                          |
| <font color='green'>DOUBLE</font>        | <font color='green'>Float64</font>                          |
| <font color='green'>DECIMAL</font>       | No sensible mapping. Use <font color='green'>Float64</font> |
| <font color='green'>NUMERIC</font>       | No sensible mapping. Use <font color='green'>Float64</font> |
| <font color='green'>BIT</font>           | <font color='green'>Boolean</font>                          |
| <font color='green'>CHAR</font>          | <font color='green'>String</font>                           |
| <font color='green'>VARCHAR</font>       | <font color='green'>String</font>                           |
| <font color='green'>LONGVARCHAR</font>   | <font color='green'>Array</font>(of bytes)                  |
| <font color='green'>BINARY</font>        | <font color='green'>Array</font>(of bytes)                  |
| <font color='green'>VARBINARY</font>     | <font color='green'>Array</font>(of bytes)                  |
| <font color='green'>LONGVARBINARY</font> | <font color='green'>Array</font>(of bytes)                  |
| <font color='green'>DATE</font>          | <font color='green'>String</font>                           |
| <font color='green'>TIME</font>          | <font color='green'>String</font>                           |
| <font color='green'>TIMESTAMP</font>     | <font color='green'>String</font>                           |

`   Mapping from JDBC Data Types to DAP2 Data Types`

</center>

> The data types described in \tableref{tab,jdbc-mapping} are only
>
> ` the primitive data types (except for Array). DAP2 supports more`
> ` sophisticated data types: Grids, Arrays, Structures, and Sequences,`
> ` among others For information about all these, refer to the`
> ` \DODSuser.`

#### Make a DDS

In a file named for your table, in the directory nominated by the
<font color='green'>dds_cache_dir</font> directive in the
<font color='green'>web.xml</font> file, put a DDS for your table. This
must contains declarations for each of the data types you expect a user
to ask for (or select over). The fields you don't list are effectively
invisible to the user of the DRDS. Use \tableref{tab,jdbc-mapping} to
determine the data types that most closely correspond to the data in
your tables.

The data served from a DRDS server is always enclosed in a DAP2 data
type called a "Sequence." Each instance of a Sequence corresponds to one
row of a relational table. In effect, the DDS declares the data types
and arrangement of the table columns. For more information about what
the DDS is, see \DODSuser. There is also a very cursory introduction to
these structures in \DODSquick.

> NOTE: When writing your DDS, be sure the name of the top-level
>
> ` sequence matches the database table name. It is the sequence name,`
> ` rather than the dataset name, that is used in the SQL statement that`
> ` will be submitted to your DBMS.`

For our example server, we would create a file called "b31," and put it
in the <font color='green'>dds_cache_dir</font>. The file contents would
look like this:

    Dataset {
      Sequence {
        Int16 id;
        String class;
        String text;
      } b31;  # Sequence name
    } b31;    # Dataset name

#### Make a DAS

Making a DAS is precisely comparable to the DDS. Make a file called
"b31," and put it in the <font color='green'>das_cache_dir</font>.
Here's the DAS for our example.

    Attributes {
      b31 {
        id {
          String long_name "Id";
        }
        class {
          String long_name "class";
        }
        text {
          String long_name "text";
        }
      }
    }

#### Make an Info File

This is entirely optional, but it can be helpful to users who want to
know more about your data. Create a file with HTML in it (without the
HTML or BODY tags), and put it in the
<font color='green'>info_cache_dir</font> directory with the same name
as the DDS and DAS files, it will be served to the user when they make
an info request. See \DODSquick for information about the contents of
the info response.

#### Done

You should now be done installing the DRDS. The URL for this server
should be the same as the URL for the test server, using the "drds"
directory instead of "dts," per the
<font color='green'>servlet-mapping</font> direction in the
<font color='green'>web.xml</font> file. (See ([Section
4.3.2](Wiki_Testing/ServerInstallationGuide4 "wikilink")).) This should
bring up a list of datasets you can select from.