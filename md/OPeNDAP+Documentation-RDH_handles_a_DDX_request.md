**Point Of Contact:** [ndp](User:Ndp "wikilink")

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier.*</font>

The RDH receives and responds to a bes:get request for a DDX.

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>
<font size="-2" color="red">*Who is the user in question?
[ndp](User:Ndp "wikilink").*</font>

Clarify the details of the DDX handling operation: Dispatch, Database
interactions, DDS construction etc.

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

The BES receives a request for a DDX that it identifies as being
serviceable by the RDH.

The RDH determines which ODBC Data Source is associated with the
request, conneccts to the Data Sources and uses the introspection
methods of ODBC to collect information about the tables and views in the
target database. From this information the RDH builds a DDX that
represents the Data Source and returns it to the requesting client.

## Actors

<font size="-2" color="green">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

client
A BES client application

## Preconditions

<font size="-2" color="green">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

1.  The BES is installed and configured - including the correct
    [installation and configuration of the
    RDH](Adding_the_RDH_to_the_BES "wikilink")
2.  The RDBMS database with tables and/or views associated with the Data
    Source associated with the requested DDX is available.

## Triggers

<font size="-2" color="green">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

- A client issues a bes:get command for a DDX associated with a Data
  Source in the RDH

## Basic Flow

<font size="-2" color="green">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

1.  The BES receives a request containing bes:get command for a DDX
    associated with a Data Source in the RDH from a BES
    client:<font color="red">(*How does the BES know the request is for
    a DDX in the RDH catalog? Will the RDH have it's own catalog? Or
    will it claim part of the resource URI space? Or something else?
    [ndp](User:Ndp "wikilink")*)</font><tt>

    <?xml version="1.0" encoding="UTF-8"?>

    <bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">

      <bes:setContext name="xdap_accept">2.0</bes:setContext>

      <bes:setContext name="dap_explicit_containers">no</bes:setContext>

      <bes:setContext name="errors">dap2</bes:setContext>

      <bes:setContext name="xml:base"><http://localhost:8080/opendap/data/hdf/1990104h09da-gdm.hdf.ddx></bes:setContext>

      <bes:setContainer name="catalogContainer" space="catalog">/data/hdf/1990104h09da-gdm.hdf</bes:setContainer>

      <bes:define name="d1" space="default">

        <bes:container name="catalogContainer" />

      </bes:define>

      <bes:get type="ddx" definition="d1" />

    </bes:request></tt>

    <font color="red">*Make sure to update this to match what we actuall
    use! [ndp](User:Ndp "wikilink") 16:27, 28 April 2009 (PDT)*</font>
2.  In the BES, the RDH is identified as the service will provide the
    requested DDX.
3.  The RDH determines which ODBC Data Source is associated with the
    requested DDX.
4.  The RDH uses the ODBC introspection API to get the structural
    <font color="red">(*do i mean schema here?
    [ndp](User:Ndp "wikilink")*)</font> descriptions of the tables and
    views held by the ODBC Data Source.
5.  The RDH instantiates/constructs a DDS class instance in memory that
    reflects the [DAP data model representation of the tables and views
    held by the ODBC Data
    Source](Mapping_the_ODBC_data_model_to_the_DAP2_data_model "wikilink")
    using the information collected in the previous step.
6.  The RDH applies any constraint expression to the in memory DDS.
    (marks all of the projected variables).
7.  The RDH uses the DDS instance to serialize it's constrained DDX
    representation back to the BES client.

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