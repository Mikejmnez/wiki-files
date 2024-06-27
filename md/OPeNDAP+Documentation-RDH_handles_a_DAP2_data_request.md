**Point Of Contact:** *A Human*

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier.*</font>

The RDH receives and responds to a bes:get request for DAP2 data.

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

The BES receives a request for a DAP2 data that it identifies as being
serviceable by the RDH.<font color="red">How does it determine
this?</font> The RDH determines which ODBC Data Source is associated
with the request, conneccts to the Data Sources and uses the
introspection methods of ODBC to collect information about the tables
and views in the target database. From this information the RDH builds a
DDS instance that represents the Data Source. The RDH converts the DAP2
constraint expression (or lack thereof) from the request into an SQL
query string(s). It then serializes the DDS, retrieving data from the
ODBC Data Source using the SQL query string(s) to get the data to
populate the DAP2 data content of the response.

## Actors

<font size="-2" color="green">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

### Primary Actors

client
A BES client application

### Secondary Actors

RDBMS
A relational database system holding the data required to fulfill the
request

## Preconditions

<font size="-2" color="green">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

1.  The BES is installed and configured - including the correct
    [installation and configuration of the
    RDH](Adding_the_RDH_to_the_BES "wikilink")
2.  The RDBMS database with tables and/or views associated with the Data
    Source associated with the requested DAP2 data is available.

## Triggers

<font size="-2" color="green">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

- A client issues a bes:get command for DAP2 data associated with a Data
  Source exposed by the RDH

## Basic Flow

<font size="-2" color="green">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

1.  The BES receives from a BES client a request containing bes:get
    command for a DAP2 data response for data help by a Data Source in
    the RDH:<tt>

    <?xml version="1.0" encoding="UTF-8"?>

    <bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-2:17:bes_request]">

       <bes:setContext name="xdap_accept">2.0</bes:setContext>

      
    <bes:setContext name="dap_explicit_containers">no</bes:setContext>

       <bes:setContext name="errors">dap2</bes:setContext>

      
    <bes:setContainer name="catalogContainer" space="catalog">/data/stuff/dataSourceOne</bes:setContainer>

       <bes:define name="d1" space="default">

         <bes:container name="catalogContainer" />

       </bes:define>

       <bes:get type="dods" definition="d1" />

    </bes:request></tt>

    <font color="red">*Make sure to update this to match what we actuall
    use! [ndp](User:Ndp "wikilink") 16:27, 28 April 2009 (PDT)*</font>
2.  In the BES, the RDH is identified as the service will provide the
    requested DAP2 data.
3.  The RDH determines which ODBC Data Source is associated with the
    requested DAP2 data.
4.  The RDH uses the ODBC introspection API to get the structural
    <font color="red">(*do i mean schema here?
    [ndp](User:Ndp "wikilink")*)</font> descriptions of the tables and
    views held by the ODBC Data Source.
5.  The RDH instantiates/constructs a DDS class instance in memory that
    reflects the [DAP data model representation of the tables and
    views](RDH:_Mapping_the_ODBC_data_model_to_the_DAP2_data_model "wikilink")
    held by the ODBC Data Source using the information collected in the
    previous step.
6.  The RDH applies the constraint expression to the DDS instance.
    (marks all of the projected variables).
7.  The RDH converts the constraint expression (or the lack of one if
    none is present) into one or more SQL query strings.
8.  The RDH uses the ODBC API to send the SQL query string(s) to the
    Data Source.
9.  The RDH uses the ODBC API retrieve the *row set(s)* returned.
10. The RDH uses the ODBC API and the DDS instance to serialize the DAP2
    data response to the BES client, while applying any as yet
    unresolved query conditions (conditions that were not converted into
    part of the SQL query string(s).)

## Alternate Flow

<font size="-2" color="green">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

## Post Conditions

<font size="-2" color="green">*Here we give any conditions that will be
true of the state of the system after the use case has been
completed.*</font>

At the end of the Basic Flow the RDH will have returned to a ready
state, awaiting the next transaction.

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
| BES                | Data Provider              | The data server                                                                            | Assumed available as it is the framework from within which the RDH operates.                                                                                                         | Hardware running the BES.                                                      |
| ODBC Driver        | Data Provider              | The ODBC driver compatible with the target RDBMS.                                          | Open source availability for most open source RDBMSs. Purchase availability fr others.                                                                                               | Hardware running the BES.                                                      |
| ODBC configuration | Data Provider              | A system level ODBC configuration                                                          | Operating system dependent. Complexity of creation a function of target OS.                                                                                                          | Hardware running the BES.                                                      |
| ODBC Data Sources  | Data Provider              | A Data Source definition for each RDBMS database to be served as a DAP Dataset             | Operating system dependent. Complexity of creation a function of target OS.                                                                                                          | Hardware running the BES.                                                      |
| Target RDBMS(s)    | Database Operator/Provider | The relational database management system(s) that will provide the data server by the RDH. | Depends on level of operators permission in the RDBMS and it's host system. At minimum SQL SELECT access must be available on all databases, tables, and views which will be served. | Hardware running the RDMS. Not required to be the same system running the BES. |