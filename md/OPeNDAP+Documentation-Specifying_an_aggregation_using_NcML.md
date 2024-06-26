Back [BES Aggregation using NcML](BES_Aggregation_using_NcML "wikilink")

**Point Of Contact:** *James Gallagher*

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier.*</font>

Aggregation_00

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

The data provider wants to group together a collection of DAP URLs so
that they can also function as a single URL. This will enable data sets
made up of many files to appear as a single entity, relieving downstream
data processing elements (DPEs) of the need to understand the
organization of the particular collection. By grouping the files/URLs
using aggregation the organization of the data set's components is
'hidden' using the DAP data model.

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

When a data provider wants to form and aggregation (grouping) from a set
of discreet files/URLs, they will need to write a short NcML document
that describes that aggregation. The NcML file will name all the pieces
in the aggregation and how they are to be assembled to form a single
(logical) entity. The file will be saved on the data server's file
system and the BES will be configured to 'serve' that file using the
NcML Aggregation handler.

A variation on this scenario is that the 'data provider' might be more
of a 'reseller' since the raw data might not be local but instead remote
URLs.

## Actors

<font size="-2" color="green">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

1.  Primary actor: The data provider who writes the NcML file
2.  The raw data that is to be aggregated
3.  The BES with the NcML aggregation handler installed and configured

## Preconditions

<font size="-2" color="green">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

1.  The data set to be aggregated must fit into one of the models
    supported by the handler. NcML support three logical models:
    JoinNew, JoinExisting and JoinUnion (New and existing refer to using
    a new or existing dimension). If the data cannot be aggregated using
    one of the supported models, then it won't work. The handler may not
    support all three models initially.
2.  The BES has to be installed and the NcML Aggregation handler must be
    installed. The BES must be configured so that requests for data
    sources that match a certain pattern (".\*\\ncml" seems like a
    likely candidate) are routed to the NcML handler.
3.  There has to be raw data local to the BES or the NcML handler has to
    be capable of working with data specified using a URL.

## Triggers

<font size="-2" color="green">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

There are several possible triggers for this use-case.

- The data provider needs to satisfy the goals of either the REAP or
  IOOS project
- The data provider wants to improve the ease of use of a particular
  data set in general
- Unless aggregated, a particular data set cannot be used by a special
  client.

## Basic Flow

<font size="-2" color="green">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

1.  The data set is identified and an aggregation model is chosen
2.  A NcML file is written that describes the aggregation
    1.  For aggregations of a small number of files/URLs, each can be
        listed separately in the *\*.ncml* file
    2.  For aggregations of many files/URLs, the NcML \<scan ...\>
        element can be used
3.  The file saved in a location that can be accessed by the BES (i.e.,
    under its DocumentRoot directory)

## Alternate Flow

<font size="-2" color="green">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

1.  The data are not local and server does not already exist:
    1.  Install a remote server
    2.  Develop an aggregation for the remote data
2.  The Data are not in a form that's possible to aggregate:
    1.  Develop a 'nested' aggregation
    2.  Reformat the data
        1.  Eliminate the need for aggregation
        2.  Facilitate aggregation is access to the raw elements is
            still important

## Post Conditions

<font size="-2" color="green">*Here we give any conditions that will be
true of the state of the system after the use case has been
completed.*</font>

Both the raw data and the aggregate are available using the DAP.

## Activity Diagram

<font size="-2" color="green">*Here a diagram is given to show the flow
of events that surrounds the use case.*</font>

## Notes

<font size="-2" color="green">*There is always some piece of information
that is required that has no other place to go. This is the place for
that information.*</font>

This particular use case is very general. We will need to develop
specific use cases for the data sets that the IOOS and REAP projects
need to make sure that the required features are developed first and
more general features are added as time allows.

*I suspect that the REAP project will require mostly JoinNew aggregation
to aggregate collections of SST images over time.*

### REAP data sets

|                                   |                                      |                                    |                                            |
|-----------------------------------|--------------------------------------|------------------------------------|--------------------------------------------|
| <font size="-1">*Data set*</font> | <font size="-1">*Server type*</font> | <font size="-1">*Base URL*</font>  | <font size="-1">*Notes*</font>             |
| GHRSST                            |                                      |                                    |                                            |
| Pathfinder (1 & 4km)              |                                      |                                    |                                            |
| Ocean Color                       |                                      |                                    |                                            |
| GOES                              |                                      |                                    |                                            |
| HyCOM                             | TDS                                  | hycom.coaps.fsu.edu/thredds/dodsC/ | It looks like these are already aggregated |

### IOOS data sets

|                                   |                                      |                                   |                                |
|-----------------------------------|--------------------------------------|-----------------------------------|--------------------------------|
| <font size="-1">*Data set*</font> | <font size="-1">*Server type*</font> | <font size="-1">*Base URL*</font> | <font size="-1">*Notes*</font> |
| GHRSST                            |                                      |                                   |                                |

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