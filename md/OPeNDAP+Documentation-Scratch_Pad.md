The DAP Relational Database Server II (DRDS-II) is a (as yet to funded)
project that will provide a DAP service layer for relational databases
systems. Previously this was (partially) accomplished by a Java servlet
known as the DODS Relational Database Server (DRDS).

## Overview

### Automated catalog generation vs. external configuration

Should the DRDS-II interrogate the database to learn the table and key
structures so that it can automatically build a *catalog* of DAP data
objects that represent the database holdings? Or, should the DRDS-II
rely (as it's predecessor did) on having the operator develop a DDS/DDX
(or some other configuration item) for each data holding?

### Data model representation: Tables/Queries as Sequences

In the world of the RDBMS everything is structurally a *row set*: A
collection (tuple) of columns (variables) whose length my not be known
until the content is delivered in total. Tables are *row sets*. A
database *view* is a *row set*. SQL queries return *row sets*. SQL JOIN
operations take *row sets' as input and produce*row sets''.

*Row sets* map naturally to the DAP Sequence data type, thus a
reasonable representation of an SQL query is a Sequence: A repeating
tuple of unknown length.

When a database contains multiple tables or views, each one could be
represented as a DAP Sequence. Thus a DDS might contain multiple
Sequences each representing a single table or view in the RDBMS:

`Dataset {`
`    Sequence {`
`        String FirstName;`
`        String LastName;`
`        String PhoneNumber;`
`    } Authors;`

`    Sequence {`
`        String Title;`
`        String Publisher;`
`        Int16  Pages;`
`        String   CopyrightDate;`
`        String   ISBN;`
`    } Books;`

`} Inventory;`

Databases are typically much more complex. In our example above it would
more likely that the Books table contains one or more foreign keys that
references the authors of the book. So how to capture this? If we simply
add the key field to the Sequence how do users of the system know how to
construct queries that can return the data they want? Would this
representation allow them to make requests that could return data with
the key relationships expressed in the DAP structure (I don't think so)
How do we prevent them from making requests the create problems for the
RDBMS by creating, via the DAP constraint expression, unreasonable JOIN
requests. Additionally, this very flat representation doesn't clearly
establish the relationships between the tables.

On possible solution is to represent the databse tables as a series of
nested sequences:

`Dataset {`

`    Sequence {`
`        String Title;`
`        String Publisher;`
`        Int16  Pages;`
`        String   CopyrightDate;`
`        String   ISBN;`
`        Sequence {`
`            String FirstName;`
`            String LastName;`
`            String PhoneNumber;`
`        } Authors;`
`    } Books;`

`} Inventory;`

Does this actually represent the case of foreign keys?

### Tasks

A server that adds a DAP interface to a RDBMS needs:

1.  A component that can translate a DAP query into an SQL query string.
    This might happen at the string level by simply converting one to
    the other. The DRDS accomplished this by processing the DAP query
    string (marking variables in a DDS instance as projected and parsing
    the selection part of the constraint into Clause objects) and then
    working with the in memory objects (DDS and Clause objects) to build
    the SQL query.
2.  A mechanism through which the RDBMS holdings (a collection of one or
    more tables and views) can be represented as a collection of DAP
    data sets. For this document we will refer to this as *catalog*
    generation. This is inexorably connected to the *type mapping*
    activities explained next.
3.  A mapping between the data types found in the RDBMS to the
    appropriate DAP data types. For many simple/atomic type this is
    simple: An SQL FLOAT maps naturally to a DAP Float64. For other
    types, such as an database table with foreign keys the mapping is
    less straightforward. This activity s further complicated by the
    fact that nearly every RDBMS implementation contains a number of
    useful, yet proprietary data types. Thus a proper data model might
    have to be generated for each RDBMS encountered.

### Previous Design/Implementation

The first DRDS (DODS Relational Database Server) was implemented as a
Java Servlet. It relied on the Java-DODS implementation of the DAP to
provide the DAP type classes with constraint expression evaluation and
data value serialization. Some documentation can be found here:

- [General Java DAP Implementation
  Documents](http://www.opendap.org/design/java-api/design.html)
- [DRDS source tree](http://scm.opendap.org/svn/trunk/DRDS/)

Important features/activities:

- The server used filesystem directories containing DDS and DAS
  documents to represent the holdings in the RDBMS. These files were
  used to generate the DDS and DAS responses.
- The opendap.servers.sql.sqlCEEval.getSQLQueries() method that takes a
  constrained DDS object and interrogates it to form an SQL query.
- Each table or view in the database could only be represented as a data
  set containing a singe DAP Sequence.
- The individual DAP type classes implement the read() and serialize()
  methods that extracts values from the SQL query result and write the
  values to the client. The DAP Sequence implementation manages which
  "row" is being read from the SQL response object.

Despite its limitations and lack of ongoing support the DRDS was useful
for a number of data centers and continues to be used today. However,
the DRDS had many drawbacks:

- It required the operator to engage in a significant configuration
  steps:
  - Locating a JDBC driver for their database implementation.
  - Configuring the DRDS to correctly use the JDBC driver.
  - Hand writing the DDS and DAS documents for each table or view in the
    database for which DAP 2 services were to be provided. This in
    effect placed the operator of the server in charge of mapping the
    different types of data held in the database tables into the
    different types in the DAP data model. The DRDS implementation only
    allowed certain mappings (as many of the possible combinations were
    non-sensical), thus if the operator made a poor mapping (For example
    creating a floating point variable in the DDS and then trying to
    read a SQL CHAR type into it) the server would not function
    correctly.
- It could not automatically build a catalog of the RDBMS holdings.
  (This was done by the operator as described above)
- The representation of the RDBMS holds was limited to a single table
  per DAP data set. Thus each DDS in the catalog contained a single
  Sequence representing a single table or view from within the RDBMS.
  Since most RDBMS holds are a collection of tables linked together
  through keys, this single Sequence representation was a very "flat"
  view of the RDBMS which many users found particularly restrictive.
  Making different views of the database alleviated the effects of this
  limitation to a certain extent, but typically caused some (many?) data
  values to be sent in a repetitive manner.

## Data model representation

Mapping the a the SQL data model to the DAP data model is problematic
due to the diversity of the implementations of an SQL database.

### Atomic (Simple) Types

### Tables as Sequences

#### Single Tables

#### Multiple Tables

#### Multiple Tables as Nested Sequences

### Other Complex Types

## Relational Database Data Types

Accessing data in an RDBMS is often a many layered affair. Many RDBMS
implementations provide a native API library that can be used with a
common programming language such as C, C++, C#, Visual Basic, or even
Java. These API's interact directly with the RDBMS and provide the
maximum level of flexibility, control and access. If one was using a
native API to work with a RDBMS to get data then there would be a
mapping from the native RDBMS data types to the language data types:


*Native RDBMS Data Types* **---\>** *C++ Data Types*

However, most software that works with RDMSs does so through a database
connectivity API. The two most common are the Open Database Connectivity
(ODBC) API and the Java Database Connectivity (JDBC) API. Using one of
these database connectivity APIs adds a layer of data type mapping to
the process:


*Native RDBMS Data Types* **---\>** *Database Connectivity API Data
Types* **---\>** *C++ Data Types*

To complete the picture, generating DAP data objects requires the
finally mapping from the programming language data types in to the DAP
data model:


*Native RDBMS Data Types* **---\>** *Database Connectivity API Data
Types* **---\>** *C++ Data Types* **---\>** *DAP Data Types*

For simple/atomic types such as integers an booleans this doesn't
require much thought. For more complex types it may require both thought
ad careful development to preserve the original semantics of the RDBMS
data type across the various mappings and into the appropriate DAP data
type. Even then the DAP data model may not have a data type that
directly captures the semantics of the original type. For example
PostgreSQL has a number of geometric types (such as *point*, *box*, and
*circle*) whose content can be held in DAP types, but the semantic
meaning (*these two floating point values represent a point on a
cartesian plain*) is not explicit in the DAP object holding the values.
That information can only be included in the metadata associated with
the instance of the DAP variable.

### Database Connectivity APIs



----

------------------------------------------------------------------------

#### [ODBC Data Types](http://en.wikipedia.org/wiki/Open_Database_Connectivity)



----

------------------------------------------------------------------------

#### [JDBC Data Types](http://java.sun.com/javase/technologies/database/)

The DRDS used a JDBC connection to retrieve data from a relational
database. It is important to note that their are several layers of type
translation happening in this:


**Database -\> JDBC -\> Java -\> DODS**

The Database types are the native types for the particular database that
is being read from. The translation from Database-\>JDBC was handled
before the data arrived at the DRDS (most likely by the JDBC Drivers).
The mapping of JDBC type to DODS types (the intermediate Java types
happen in the process) looked like this:

| JDBC Type     | DAP Type                                                                                                                   |
|---------------|----------------------------------------------------------------------------------------------------------------------------|
| TINYINT       | DByte                                                                                                                      |
| SMALLINT      | DInt16                                                                                                                     |
| INTEGER       | DInt32                                                                                                                     |
| BIGINT        | DInt32 \*\*NO SENSIBLE MAPPING (Need DInt64)                                                                               |
| REAL          | DFloat32                                                                                                                   |
| FLOAT         | DFloat64                                                                                                                   |
| DOUBLE        | DFloat64                                                                                                                   |
| DECIMAL       | DFloat64 \*\*NO SENSIBLE MAPPING (Need Some Kind Monsterous Floating point value)                                          |
| NUMERIC       | DFloat64 \*\*NO SENSIBLE MAPPING (ibid)                                                                                    |
| BIT           | DBoolean                                                                                                                   |
| CHAR          | DString                                                                                                                    |
| VARCHAR       | DString                                                                                                                    |
| LONGVARCHAR   | Implemented to be read into a DString, although it is a "BLOB" type and might be better represented as a DArray(of bytes). |
| BINARY        | DArray(of bytes)                                                                                                           |
| VARBINARY     | DArray(of bytes)                                                                                                           |
| LONGVARBINARY | DArray(of bytes)                                                                                                           |
| DATE          | DString                                                                                                                    |
| TIME          | DString                                                                                                                    |
| TIMESTAMP     | DString                                                                                                                    |

**Mapping from JDBC Types to DODS Types**

### Native SQL Data Types by Implementation



----

------------------------------------------------------------------------

#### [PostgreSQL Data Types](http://www.postgresql.org/docs/8.3/interactive/datatype.html)


**Todo:**


**<font color=red> !! This section needs to be updated and otherwise
corrected. !!</font>**

##### Boolean and Binary Types

| Data Type                 | Description                                           | Standardization | Logical DAP data type association. |
|---------------------------|-------------------------------------------------------|-----------------|------------------------------------|
| boolean, bool             | A single true or false value.                         | SQL99           | Boolean                            |
| bit(n)                    | An n -length bit string (exactly n binary bits).      | SQL92           | *None*                             |
| bit varying(n), varbit(n) | A variable n -length bit string (up to n binary bits) | SQL92           | *None*                             |

##### Character Types

| Data Type                        | Description                                               | Storage           | Standardization     | Logical DAP data type association. |
|----------------------------------|-----------------------------------------------------------|-------------------|---------------------|------------------------------------|
| character (n ), char(n )         | A fixed n -length character string.                       | (4+n) bytes       | SQL89               | String                             |
| character varying(n), varchar(n) | A variable length character string of up to n characters. | Up to (4+n) bytes | SQL92               | String                             |
| text                             | A variable length character string, of unlimited length.  | Variable          | PostgreSQL-specific | String                             |

##### Numeric Types

| Data Type                       | Description                                                    | Storage  | Standardization                  | Logical DAP data type association. |
|---------------------------------|----------------------------------------------------------------|----------|----------------------------------|------------------------------------|
| smallint, int2                  | A signed 2-byte integer                                        | 2 bytes  | SQL89                            | Int16                              |
| integer, int, int4              | A signed, fixed-precision 4-byte number.                       | 4 bytes  | SQL92                            | Int32                              |
| bigint, int8                    | A signed 8-byte integer, up to 18 digits in length.            | 8 bytes  | PostgreSQL-specific              | *None (Need Int64)*                |
| real, float4                    | A 4-byte floating point number.                                | 4 bytes  | SQL89                            | Float32                            |
| double precision, float8, float | An 8-byte floating point number                                | 8 bytes  | SQL89                            | Float64                            |
| numeric(p,s), decimal(p,s)      | An exact numeric type with arbitrary precision p, and scale s. | Variable | SQL99                            | *None*                             |
| money                           | A fixed precision, U.S.-style currency.                        | 4 bytes  | PostgreSQL-specific, deprecated. | *None*                             |
| serial                          | An auto-incrementing 4-byte integer.                           | 4 bytes  | PostgreSQL-specific.             | *None*                             |

##### Date and Time Types

| Data Type                      | Description                                       | Storage  | Standardization | Logical DAP data type association. |
|--------------------------------|---------------------------------------------------|----------|-----------------|------------------------------------|
| date                           | A calendar date (day, month, year).               | 4 bytes  | SQL92           | *String*                           |
| time                           | The time of day.                                  | 4 bytes  | SQL92           | *String*                           |
| time with time zone            | The time of day, including time zone information. | 4 bytes  | SQL92           | *String*                           |
| timestamp (includes time zone) | Both date and time.                               | 8 bytes  | SQL92           | *String*                           |
| interval                       | An arbitrary specified length of time             | 12 bytes | SQL92           | *String*                           |

##### Geometric Types

| Data Type | Description                                                  | Storage       | Standardization     | Logical DAP data type association.   |
|-----------|--------------------------------------------------------------|---------------|---------------------|--------------------------------------|
| box       | A rectangular box in a 2D plane.                             | 32 bytes      | PostgreSQL-specific | *None* (\[2\]\[2\] Array of Float32) |
| line      | An infinite line in a 2D plane.                              | ?? bytes      | PostgreSQL-specific | *None* (\[2\]\[2\] Array of Float32) |
| lineseg   | A finite line segment in a 2D plane.                         | 32 bytes      | PostgreSQL-specific | *None* (\[2\]\[2\] Array of Float32) |
| circle    | A circle with center and radius.                             | 24 bytes      | PostgreSQL-specific | *None* (\[3\]Array of Float32)       |
| path      | Open and closed geometric paths in a two-dimensional plane . | 4+32\*n bytes | PostgreSQL-specific | *None* (Array of Float32)            |
| point     | geometric point in a 2D plane                                | 16 bytes      | PostgreSQL-specific | *None* (\[2\] Array of Float32)      |
| polygon   | A closed geometric path in a 2D plane                        | 4+32\*n bytes | PostgreSQL-specific | *None* (Array of Float32)            |

##### Network Types

| Data Type | Description                                                | Standardization     | Logical DAP data type association. |
|-----------|------------------------------------------------------------|---------------------|------------------------------------|
| cdir      | An IP network specification                                | PostgreSQL-specific | *None*                             |
| inet      | A network IP address, with optional subnet bits.           | PostgreSQL-specific | *None*                             |
| macaddr   | A MAC address (e.g., an Ethernet card's hardware address). | PostgreSQL-specific | *None*                             |

##### System Types

| Data Type | Description                 | Standardization     | Logical DAP data type association. |
|-----------|-----------------------------|---------------------|------------------------------------|
| oid       | An object (row) identifier. | PostgreSQL-specific | *None*                             |
| xid       | A transaction identifier    | PostgreSQL-specific | *None*                             |



----

------------------------------------------------------------------------

#### [Transact-SQL (Microsoft)](http://msdn.microsoft.com/en-us/library/ms187752.aspx)


**Todo:**


**<font color=red> !! This section needs to be updated and otherwise
corrected. !!</font>**

| Type              | Description                                                                                                                                                                                                                  | Storage Bytes | DAP equiv. |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|------------|
| bigint            | Integer (whole number) data from -2^63 (-9,223,372,036,854,775,808) through 2^63-1 (9,223,372,036,854,775,807).                                                                                                              | 8 bytes       | none       |
| int               | Integer (whole number) data from -2^31 (-2,147,483,648) through 2^31 - 1 (2,147,483,647).                                                                                                                                    | 4 bytes       |            |
| numeric           | Functionally equivalent to decimal.                                                                                                                                                                                          |               |            |
| decimal           | Fixed precision and scale numeric data from -10^38 +1 through 10^38 –1.                                                                                                                                                      |               |            |
| bit               | Integer data with either a 1 or 0 value                                                                                                                                                                                      |               |            |
| smallint          | Integer data from -2^15 (-32,768) through 2^15 - 1 (32,767).                                                                                                                                                                 | 2 bytes       |            |
| tinyint           | Integer data from 0 through 255                                                                                                                                                                                              | 1 bytes       |            |
| smallmoney        | Monetary data values from -214,748.3648 through +214,748.3647, with accuracy to a ten-thousandth of a monetary unit.                                                                                                         |               |            |
| money             | Monetary data values from -2^63 (-922,337,203,685,477.5808) through 2^63 - 1 (+922,337,203,685,477.5807), with accuracy to a ten-thousandth of a monetary unit.                                                              |               |            |
| float             | Floating precision number data with the following valid values: -1.79E + 308 through -2.23E - 308, 0 and 2.23E + 308 through 1.79E + 308.                                                                                    | 4 or 8 bytes  |            |
| real              | Floating precision number data with the following valid values: -3.40E + 38 through -1.18E - 38, 0 and 1.18E - 38 through 3.40E + 38.                                                                                        | 4 bytes       |            |
| date              |                                                                                                                                                                                                                              |               |            |
| datetimeoffset    |                                                                                                                                                                                                                              |               |            |
| datetime2         |                                                                                                                                                                                                                              |               |            |
| smalldatetime     | Date and time data from January 1, 1900, through June 6, 2079, with an accuracy of one minute.                                                                                                                               |               |            |
| datetime          | Date and time data from January 1, 1753, through December 31, 9999, with an accuracy of three-hundredths of a second, or 3.33 milliseconds.                                                                                  |               |            |
| time              |                                                                                                                                                                                                                              |               |            |
| char              | Fixed-length non-Unicode character data with a maximum length of 8,000 characters.                                                                                                                                           |               |            |
| varchar           | Variable-length non-Unicode data with a maximum of 8,000 characters.                                                                                                                                                         |               |            |
| next              | Variable-length non-Unicode data with a maximum length of 2^31 - 1 (2,147,483,647) characters                                                                                                                                |               |            |
| nchar             | Fixed-length Unicode data with a maximum length of 4,000 characters                                                                                                                                                          |               |            |
| nvarchar          | Variable-length Unicode data with a maximum length of 4,000 characters. sysname is a system-supplied user-defined data type that is functionally equivalent to nvarchar(128) and is used to reference database object names. |               |            |
| ntext             | Variable-length Unicode data with a maximum length of 2^30 - 1 (1,073,741,823) characters.                                                                                                                                   |               |            |
| binary            | Fixed-length binary data with a maximum length of 8,000 bytes.                                                                                                                                                               |               |            |
| varbinary         | Variable-length binary data with a maximum length of 8,000 bytes.                                                                                                                                                            |               |            |
| image             | Variable-length binary data with a maximum length of 2^31 - 1 (2,147,483,647) bytes.                                                                                                                                         |               |            |
| cursor            | A reference to a cursor.                                                                                                                                                                                                     |               |            |
| timestamp         | A database-wide unique number that gets updated every time a row gets updated.                                                                                                                                               |               |            |
| hierarchyid       |                                                                                                                                                                                                                              |               |            |
| uniquieidentifier | A globally unique identifier (GUID).                                                                                                                                                                                         |               |            |
| sql_variant       |                                                                                                                                                                                                                              |               |            |
| xml               |                                                                                                                                                                                                                              |               |            |
| table             |                                                                                                                                                                                                                              |               |            |

## Template

#### Types

| Data Type | Description | Standardization | Logical DAP data type association. |
|-----------|-------------|-----------------|------------------------------------|
|           |             |                 |                                    |
|           |             |                 |                                    |
|           |             |                 |                                    |

## Desired Features

## Implementation Target