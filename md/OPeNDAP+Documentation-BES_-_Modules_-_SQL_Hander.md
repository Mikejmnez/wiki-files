## Kinds of files the handler will serve

This handler will serve data stored in a relational database if that
database is configured to be accessed using ODBC. The handler has been
tested using both the unixODBC and iODBC driver managers on Linux and
OS/X, respectively. While our testing has been limited to the MySQL and
Postgres database servers, the handler is not specific to either of
those severs; it should work with any database that can be accessed
using an ODBC driver.

The handler can be configured to combine information from several tables
and provide access to it as a single dataset, including performing the
full range of SQL operations. At the same time, the SQL database server
is never exposed to the web using this handler, so the database contents
are safe.

### Mappings between the ODBC data types and DAP2 data types

The SQL Handler maps the datatypes defined by SQL into types defined by
DAP. In most cases the mapping is obvious. Here we document each of the
supported SQL types and their corresponding DAP type. Note that any
types not listed here causes a runtime fatal error. That is, if you
include in the *\[select\]* part of the dataset file the name of a
column with an unsupported data type, the handler will return an error
saying *SQL Handler: The datatype read from the Data Source is not
supported. The problem type code is: <type code>*.

Here's a table that maps ODBC to/from SQL:
<http://publib.boulder.ibm.com/infocenter/dzichelp/v2r2/index.jsp?topic=%2Fcom.ibm.db2.doc.odbc%2Fbjneappl1009305.htm>

<table>
<caption>The Mapping between ODBC and DAP datatypes</caption>
<thead>
<tr class="header">
<th><p>ODBC Type</p></th>
<th><p>DAP Type</p></th>
<th><p>Comments</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>SQL_C_CHAR</p></td>
<td><p>Str</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>SQL_C_SLONG, SQL_C_LONG</p></td>
<td><p>Int32</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>SQL_C_SHORT</p></td>
<td><p>Int16</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>SQL_C_FLOAT</p></td>
<td><p>Float32</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>SQL_C_DOUBLE</p></td>
<td><p>Float64</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>SQL_C_NUMERIC</p></td>
<td><p>Int32</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>SQL_C_DEFAULT</p></td>
<td><p>Str</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>SQL_C_DATE, SQL_C_TIME, SQL_C_TIMESTAMP,<br />
SQL_C_TYPE_DATE, SQL_C_TYPE_TIME, SQL_C_TYPE_TIMESTAMP</p></td>
<td><p>Str</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>SQL_C_BINARY, SQL_C_BIT</p></td>
<td><p>Int16</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>SQL_C_SBIGINT, SQL_C_UBIGINT</p></td>
<td><p>Int32</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>SQL_C_TINYINT, SQL_C_SSHORT, SQL_C_STINYINT</p></td>
<td><p>Int16</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>SQL_C_ULONG, SQL_C_USHORT</p></td>
<td><p>Int32</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>SQL_C_UTINYINT</p></td>
<td><p>Int32</p></td>
<td></td>
</tr>
<tr class="even">
<td><p>SQL_C_CHAR</p></td>
<td><p>Str</p></td>
<td></td>
</tr>
<tr class="odd">
<td><p>SQL_C_CHAR</p></td>
<td><p>Str</p></td>
<td></td>
</tr>
</tbody>
</table>

The Mapping between ODBC and DAP datatypes

| SQL Type                               | ODBC Type |
|----------------------------------------|-----------|
| SQL_CHAR, SQL_VARCHAR, SQL_LONGVARCHAR |           |
| SQL_WCHAR, SQL_WVARCHAR, SQL_WCHAR     |           |
| SQL_DECIMAL, SQL_NUMERIC               |           |

The Mapping between SQL and ODBC datatypes

### Known problems

There are no known problems.

## Configuration parameters

### Configuring the ODBC Driver

To configure the handler the handler itself must be told which tables,
or parts of tables, should be accessed and the ODBC driver must be
configured. In general, ODBC drivers are pretty easy to configure and,
while each driver has its idiosyncrasies, most of the setup is the same
for any driver/database combination. Both *unixODBC* and *iODBC* use two
configuration fills: */etc/odbcinst.ini* and */etc/odbc.ini*. The driver
should have documentation on these files and their setup. There is one
parameter you will need to know to make use of the sql handler. In the
*odbc.ini* file, the parameter *database* is used to reference the
actual database that is matched to particular *Data Source Name* (DSN).
You will need to know the DSN since programs that use ODBC to access a
database use the DSN and not the name of the database. In addition,
there is a *user* and *password* parameter set defined for a particular
DSN; the sql handler will likely need that too (NB: This might not
actually be needed 9/9/12).

What the configuration files look like on OSX:

This file likely will no change. odbcinst.ini:

``` ini
[ODBC Drivers]
MySQL ODBC 5.1 Driver = Installed
psqlODBC              = Installed

[ODBC Connection Pooling]
PerfMon    = 0
Retry Wait =

[psqlODBC]
Description = PostgreSQL ODBC driver
Driver      = /Library/PostgreSQL/psqlODBC/lib/psqlodbcw.so

[MySQL ODBC 5.1 Driver]
Driver = /usr/local/lib/libmyodbc5.so
```

This file holds information about the database name and the Data Source
Name (DSN). Here it's creatively named 'test'. odbc.ini:

``` ini
[ODBC Data Sources]
data_source_name = test

[ODBC]
Trace         = 0
TraceAutoStop = 0
TraceFile     =
TraceLibrary  =

[test]
Description = MySQL test database
Trace       = Yes
TraceFile   = sql.log
Driver      = MySQL ODBC 5.1 Driver
Server      = localhost
User        = jimg
Password    =
Port        = 3306
DATABASE    = test
Socket      = /tmp/mysql.sock
```

### Configuring the handler

#### SQL.CheckPoint

Checkpoints in the SQL handler are phases of the database access process
where error conditions can be tested for and reported. If these are
activated using the *SQL.CheckPoint* parameter and an error is found,
then a message will be printed in the bes.log and an exception will be
thrown. There are five checkpoints supported by the handler:

CONNECT: 1 (Fatal error)
CLOSE: 2
QUERY: 3
GET_NEXT: 4 (Recoverable error)
NEXT_ROW: 5

The default for the handler is to test for and report all errors:

`SQL.CheckPoint=1,2,3,4,5`

### Configuring Datasets

One aspect of the SQL handler that sets it appart from other handlers is
that the datasets it serves are not files or collections of files.
Instead they are values read from one or more tables in a database. The
handler uses one file for each dataset it serves; we call them *dataset
files*. Within a dataset file there are several sections that define
which Data Set Name (DSN) to use (recall that the DSN is set in the
*odbc.ini* file which maps the DSN to a particular database, user and
password), which tables, how to combine them and which columns to
*select* and if any other constraints should be applied when retrieving
the values from the database server. As a data provider, you should plan
on having a dataset file for each dataset you want people to access,
even if those all come from the same table.

A dataset file has five sections:

section: This is where the DSN and other information are given
select: Here the arguments to passed to select are given. This may be *\** or the names of columns, just as with an SQL *SELECT* statement
from: The names of the tables. This is just like the *FROM* part of an SQL *SELECT* statement.
where: You're probably seeing a pattern by now: SELECT ... FROM ... WHERE
other: Driver-specific parameters

Each of the sections is denoted by starting a line in the dataset file
with its name in square brackets such as:

`[section]`

or

`[select]`

#### Information in the *section* part of the dataset file

There are six parameters that may be set in the *select* part of the
dataset file:

api: Currently this must be *odbc*
server: The DSN.
user, pass, dbname, port: Unused. These are detected by the code, however, and can be used by a new submodule that connects to a database using a scheme other than ODBC. For example, if you were to specialize the connection mechanism so that it used a database's native API, these keywords could be used to set the database name, user, etc., in place of the ODBC DSN. In that case the value of *api* would need to be the base name of the new connection specialization.

Note that a dataset file may have several \[section\] parts, each which
lists a different DSN. This provides a failover capability so that if
the same information (or similar enough to be accessible using the same
SQL statement) exists both locally and remotely, both sources can be
given. For example, suppose that your institution maintains a database
with many thousands of observations and you want to serve a subset of
those. You have a copy of those data on your own computer too, but you
would rather have people access the data from the institution's high
performance hardware. You can list both DSNs, knowing that the first
listed will get preference.

#### The *select* part

This part lists the columns to include as you would write them in an SQL
SELECT statement. Each column name has to be unique. You can use aliases
(defined in the preamble of the dataset file) to define different names
for two columns from different database tables that are the same. For
example, you could define aliases like these:

`table1.theColumn as col1`
`table2.theColumn as col2`

and then use *col1,col2* in the select part of the dataset file

#### The *from* and *where* parts

Each of these parts are simply substituted and passed to the database
just as you would expect. Note that you do not include the actual words
*FROM* or *WHERE*, just the contents of those parts of the SQL
statement.

#### The *other* part

Entries in this parts should be of the form *key = value*, one per line.
They are taken as a group and passed to the ODBC driver. Use this
section to provide any parameters that are specific to a particular
driver.

#### Using variables

The dataset files also support 'variables' that can be used to define a
name once and then use it repeatedly by simply using the variable name
instead. Then if you decide to read from a different table, only the
variable definition needs to be changed. Variables are defined as the
beginning o the dataset file, before the *section* part. The syntax for
variable is simple: *define \$variable\$ = value*, one per line (the
*\$* characters are literal, as is the word *define*). To reference a
variable, use *\$variable\$* wherever you would otherwise use a literal.

#### Some example dataset files

    [section]
    #  Required.
    api=odbc

    # This is the name of the configured DSN
    server=MySQL_DSN

    [select]
    # The attribute list to query
    # NOTE: The order used here will be kept in the results
    id, wind_chill, description

    [from]
    # The table to use can be a complex FROM clause
    wind_08_2010

    [where]
    # this is optional constraint which will be applied to ALL
    # the requests and can be used to limit the shared data.
    id<100