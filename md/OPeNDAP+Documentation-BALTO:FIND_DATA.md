**Point Of Contact:** *James or Nathan*

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier.*</font>

BALTO:FIND_DATA

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

Find data to use with ASPECT

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

Search for a data file, served by BALTO, using Google.

## Actors

<font size="-2" color="green">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

- Google general search web site
- BALTO broker site (a specific instance of Hyrax)
- Data provider - the person who puts the data file on BALTO or who
  configures BALTO so that it can access the data if it's 'served' by
  some other source on the Internet.
- Data user - person who searches for data

## Preconditions

<font size="-2" color="green">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

- The data are either:

1.  served by BALTO, or
2.  BALTO can broker requests to the server that holds/serves these data

- Google has indexed BALTO and that index operation has included the
  data it can access using its brokering capabilities

Note that *using* the remote data is a separate use case.

## Triggers

<font size="-2" color="green">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

The Data user decides they want to use data set X and search for it.

## Basic Flow

<font size="-2" color="green">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

1.  The Data user goes to Google and enters text they hope will find the
    data set
2.  Google returns a page of links
3.  The Data user copies the one that seems like it 'is from BALTO' and
    pastes it into an application (e.g., ASPECT)

## Alternate Flow

<font size="-2" color="green">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

1.  The Data user doesn't find any BALTO link but does find a link that
    looks like it references something BALTO could broker.
    1.  Goto
        [BALTO:ADD_BROKERED_DATA](BALTO:ADD_BROKERED_DATA "wikilink")
2.  The Data user finds nothing useful.
    1.  Stop; fail

## Post Conditions

<font size="-2" color="green">*Here we give any conditions that will be
true of the state of the system after the use case has been
completed.*</font>

1.  Normal flow: Data set URL found
2.  Alternate flow: Data set URL not found
3.  Alternate flow: Data set URL found, but it's not currently
    accessible using BALTO. BALTO might be able to broker it, but it
    will have to be added to broker. See
    [BALTO:ADD_BROKERED_DATA](BALTO:ADD_BROKERED_DATA "wikilink")

## Activity Diagram

<font size="-2" color="green">*Here a diagram is given to show the flow
of events that surrounds the use case.*</font>

## Notes

<font size="-2" color="green">*There is always some piece of information
that is required that has no other place to go. This is the place for
that information.*</font>

1.  Should we include cases where Google Dataset Search is used instead
    of the regular Google search?
2.  Should we include cases where some kind of local Balto site search
    is used?

## Resources

<font size="-2" color="green">*In order to support the capabilities
described in this Use Case, a set of resources must be available and/or
configured. These resources include data and services, and the systems
that offer them. This section will call out examples of these
resources.*</font>

|                                             |                                                                                 |                                                                                                  |                                                                            |                                                                               |
|---------------------------------------------|---------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| Resource                                    | Owner                                                                           | Description                                                                                      | Availability                                                               | Source System                                                                 |
| <font size="-2" color="green">*name*</font> | <font size="-2" color="green">*Organization that owns/ manages resource*</font> | <font size="-2" color="green">*Short description of the resource*</font>                         | <font size="-2" color="green">*How often the resource is available*</font> | <font size="-2" color="green">*Name of system which provides resource*</font> |
| BALTO                                       | OPeNDAP, then...?                                                               | An instance of Hyrax, configured to broker specific datasets served from other network locations | 24/7                                                                       | AWS (who pays in the long term?)                                              |
| Google search                               | Google                                                                          | I think we know what this does                                                                   | 24/7                                                                       | Capitalism                                                                    |