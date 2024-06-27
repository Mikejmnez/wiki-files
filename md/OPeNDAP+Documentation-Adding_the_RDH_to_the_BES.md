**Point Of Contact:** [ndp](User:Ndp "wikilink")

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier.*</font>

Relational Database Handler (RDH) installation and configuration.

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

A person who is configuring a BES daemon adds the RDH and viola, the BES
can now retrieve data from an RDBMS.

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

The RDH handler reads a configuration (parts of the bes.conf file?) from
which it learns:

- The names of the ODBC Data Sources for which it is going to serve
  data.

The RDH caches that information in memory and is then ready to respond
to requests.

This use case covers just how the RDH handler is added to the BES and
the process of ingesting and caching it's configuration.

## Actors

<font size="-2" color="green">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

A data provider or system administrator.

## Preconditions

<font size="-2" color="green">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

1.  The BES is installed and configured, except for the RDH
2.  There is an accessible RDBMS with tables and/or views containing
    data to be served.
3.  ODBC Data Source Definition(s) for the databases to be accessed have
    been created and tested on the BES host. Data Source Definitions
    contain the following types of information:
    - The name of the Data Source Definition (which is used by processes
      on the host using ODBC)
    - ODBC driver string/id/thingy
    - The host name or IP address of the RDBMS system
    - The port number of the RDBMS system.
    - The user name to use for connecting to the RDBMS
    - The password associated with user name
    - The name of database in the RDBMS

## Triggers

<font size="-2" color="green">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

The trigger is the need to add this feature to the Hyrax data server. In
the case of the IOOS projects, the RDH Handler will provide DAP access
to in situ data stored in a RDBMS. Satisfying the requirements for that
project will be the trigger. In general, the trigger will be the need to
use the features of the RDH with one or more data sets.

## Basic Flow

<font size="-2" color="green">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>
<font color="red">Instead of editing bes.conf, create a rdh.conf in the
etc/bes/modules directory. Every conf file in that directory is loaded
by the BES. - pcw</font>

1.  User (installer/configurer) installs (builds and installs or
    installs a binary) of the RDH code '''
2.  The user edits the BES.conf file so that it contains the ODBC Data
    Source Names that the RDH requires build it's inventory catalog.

    : Here are the relevant lines from the bes.conf file before adding
    the handler to the server:


    `BES.modules=dap,cmd,ascii,usage,www,nc,h4`

    We would add the RDH to the BES.modules line:


    `BES.modules=dap,cmd,ascii,usage,www,nc,h4,rdh`

    and add a new line specific to the RDH to configure it's Data
    Sources:


    `BES.rdh.datasources=dataSourceOne,dataSourceTwo,dataSourceThree`
    <font color="red">where are these defined? - pcw</font>
3.  The user (re)starts the BES
4.  At start-up the RDH loads it's configuration components from the
    bes.conf file.
5.  The RDH caches the list of Data Source Names into a memory object
    (like a HashMap that relates resource ID to Data Source).

## Alternate Flow

<font size="-2" color="green">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

## Post Conditions

<font size="-2" color="green">*Here we give any conditions that will be
true of the state of the system after the use case has been
completed.*</font>

At the end of the Basic Flow the RDH will have in memory the list of
Data Source names.

- Each Data Source name represents a DAP Dataset. Thus, the RDH will be
  able to respond to bes:showCatalog and bes:showInfo requests by
  sending back the Data Source names as the Dataset names.
- The RDH will be ready to accept the various DAP related bes:get
  requests for the Datasets.

## Activity Diagram

<font size="-2" color="green">*Here a diagram is given to show the flow
of events that surrounds the use case.*</font>

## Notes

<font size="-2" color="green">*There is always some piece of information
that is required that has no other place to go. This is the place for
that information.*</font>

## Resources

<font size="-2" color="green">*In order to support the capabilities
described in this Use Case, a set of resources must be available and/or
configured. These resources include data and services, and the systems
that offer them. This section will call out examples of these
resources.*</font>

|                    |                            |                                                                                            |                                                                                                                                                                                      |                                                                                |
|--------------------|----------------------------|--------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| Resource           | Owner                      | Description                                                                                | Availability                                                                                                                                                                         | Source System                                                                  |
| BES                | Data Provider              | The data server                                                                            | Once installed, it can be hard to get the admin to make changes                                                                                                                      | N/a                                                                            |
| ODBC Driver        | Data Provider              | The ODBC driver compatible with the target RDBMS.                                          | Open source availability for most open source RDBMSs. Purchase availability fr others.                                                                                               | Hardware running the BES.                                                      |
| ODBC configuration | Data Provider              | A system level ODBC configuration                                                          | Operating system dependent. Complexity of creation a function of target OS.                                                                                                          | Hardware running the BES.                                                      |
| ODBC Data Sources  | Data Provider              | A Data Source definition for each RDBMS database to be served as a DAP Dataset             | Operating system dependent. Complexity of creation a function of target OS.                                                                                                          | Hardware running the BES.                                                      |
| Target RDBMS(s)    | Database Operator/Provider | The relational database management system(s) that will provide the data server by the RDH. | Depends on level of operators permission in the RDBMS and it's host system. At minimum SQL SELECT access must be available on all databases, tables, and views which will be served. | Hardware running the RDMS. Not required to be the same system running the BES. |