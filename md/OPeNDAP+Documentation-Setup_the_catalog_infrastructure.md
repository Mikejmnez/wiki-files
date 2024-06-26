**Point Of Contact:** *A Human*

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier.*</font>

SystemConfiguration00

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

To build both a system that will work for the REAP project and to make
one that we can describe as, at minimum, capable of being used in a
general context, the design must support many different metadata stores.
The goal of this use case is to describe the set up and initialization
of a new Metacat that will catalog some collection of DAP data sets.

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

This use case describes how a new catalog site is set up. The process
involves getting, installing and configuring the MetaCat server and DAP
server crawler. But those steps only put the infrastructure in place,
other required activities will include configuring the crawler to scan
and catalog a group of servers, writing a document to describe how to
add metadata for a particular client or project and probably writing
XSLT to build those documents from the datasets' DDX objects.

A key thing about this use case is that it requires software
development, at least at the level of XSLT.

## Actors

<font size="-2" color="green">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

- A person who wants to setup a catalog for a particular client or
  project
- One or more data servers, likely maintained independently from each
  other and the catalog
- A client or project with a specific set of search requirements

## Preconditions

<font size="-2" color="green">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

## Triggers

<font size="-2" color="green">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

## Basic Flow

<font size="-2" color="green">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

1.  Install and configure the cataloging system infrastructure
    1.  Install the TPAC crawler
    2.  Configure the TPAC crawler
    3.  Install MetaCat
    4.  Configure MetaCat
2.  Build the records the client will search
    1.  Write up a Guide describing the kinds of metadata records that
        are required (ISO 19115 fragments, et c.)
    2.  Write a tool that can check a DDX to determine if it provides
        the required fragments
    3.  Write a tool that will build the records
3.  Link the crawler, the record builds and the catalog

## Alternate Flow

<font size="-2" color="green">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

## Post Conditions

<font size="-2" color="green">*Here we give any conditions that will be
true of the state of the system after the use case has been
completed.*</font>

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

|                                             |                                                                                 |                                                                          |                                                                            |                                                                               |
|---------------------------------------------|---------------------------------------------------------------------------------|--------------------------------------------------------------------------|----------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| Resource                                    | Owner                                                                           | Description                                                              | Availability                                                               | Source System                                                                 |
| <font size="-2" color="green">*name*</font> | <font size="-2" color="green">*Organization that owns/ manages resource*</font> | <font size="-2" color="green">*Short description of the resource*</font> | <font size="-2" color="green">*How often the resource is available*</font> | <font size="-2" color="green">*Name of system which provides resource*</font> |