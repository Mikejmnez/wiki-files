Back: [AIS Using NcML](AIS_Using_NcML "wikilink")

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier. Consider goal-driven use case
name.*</font>

Add_Attribtues_00

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

The user adds attributes that enable 'better' or more 'complete' use of
the data by a downstream data consumer (client or server-side processing
module). Examples:

1.  adding information so that the WCS front-end can build responses
    that include geo-referencing information that was not originally
    part of the data set
2.  adding information so that EML documentation can be built for a data
    set
3.  adding information so that a client that recognizes COARDS-compliant
    data sets will have the information it needs to offer those special
    capabilities for a data set that it otherwise could not.

## Summary

<font color="green" size="-2">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

A person decides to add one or more attributes to a data set. They first
examine the data set, maybe using DAP to look at the dataset's variables
and attributes or maybe using some other tool(s), and determine what
information/attributes to add. These attributes may be global attributes
or they may be bound to a specific variable in the data set. They write
a NcML file that describes the extra information needed and a correctly
configured BES uses that *\*.ncml* file as if it were a data set. The
URL to the NcML file is effectively a new data set that contains all of
the original data set's information (variables, attributes and data
values) plus the information in the NcML file. The added information can
be used by any downstream consumers of the data including clients and
server-side processing elements.

## Actors

<font color="green" size="-2">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

1.  Primary actor: The person who writes the NcML file
2.  The BES NcML handler
3.  The BES
4.  The data set to be modified
5.  The downstream processor that will use the additional information

The person who writes the NcML file will wind up using the other actors
in various ways. The most important of the other actors are the data set
itself, how much information it already contains and the requirements of
the intended downstream data processing element. In practice the AIS
will be used to add information that can be used by several downstream
data processing elements, so the NcML writer will want to have their
combined requirements in mind or else will wind up repeating similar
information for each downstream processor.

## Preconditions

<font color="green" size="-2">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

1.  The NcML handler has been installed and configured in the BES
2.  There is at least one data set served by the BES *or* the NcML
    handler can work with remote data.
3.  At least one downstream data processor has been identified and its
    requirements for metadata are well known.

## Triggers

<font color="green" size="-2">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

1.  The downstream data processor needs information that is not present
    in the data set.

## Basic Flow

<font color="green" size="-2">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

1.  A person who has write access to the data store served by the NcML
    handler decides to add information to a data set so that the data
    set can be used by some downstream processing element (DPE).
2.  They look at the DPE documentation to see what information it needs
    and how that information should be represented.
3.  They look at the data source and figure out what of the information
    needed is already there in the necessary form and what needs to be
    added
4.  They add the extra information to a NcML file that references the
    original data set.
5.  They store the new NcML file in a directory that the BES can use to
    'serve it using the NcML handler'. This probably means any directory
    under the BES' DocumentRoot directory (since the BES uses regular
    expressions to match data sets to handlers, there's a fair amount of
    latitude to where the NcML file is stored).
6.  They test the result.

## Alternate Flow

<font color="green" size="-2">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

It's pretty likely that most times a person adding information to a data
set using the NcML handler will have to iterate several times to get the
right information in the right places. There will thus be an implement,
test, implement, ... cycle that converges on a working solution. In
addition, it is possible that some of the DPEs will be in the server
itself and if its easier, those DPEs might be modified so that the
information that they need is easy(ier) to add or is the same as that
required by some other DPE.

## Post Conditions

<font color="green" size="-2">*Here we give any conditions that will be
true of the state of the system after the use case has been
completed.*</font>

Once the NcML handler has been used to augment a data set there are two
ways to access the data values, one using the original data set URL(s)
and one using a *NcML URL*. The latter provides access to all of the
data values of the former plus new information. In the first version,
that new information will take the form of attributes but it might also
be additional variables (data) in a later version since NcML 2.2
supports that in addition to adding attributes.

## Activity Diagram

<font color="green" size="-2">*Here a diagram is given to show the flow
of events that surrounds the use case.*</font>

## Notes

<font color="green" size="-2">*There is always some piece of information
that is required that has no other place to go. This is the place for
that information.*</font>

The basic idea here is that a person sees a need to ad information to a
data set and can use the NcML handler to do so. However, there are two
short-term objects for this code and they both involve adding
information to a data set so that it can be used by other parts of
hyrax - not some distant client over which we have little control. So it
may well be that when we first start to use the NcML handler we also
wind up tweaking these other parts of the BES to make the ensemble work
more smoothly.

## Resources

<font color="green" size="-2">*In order to support the capabilities
described in this Use Case, a set of resources must be available and/or
configured. These resources include data and services, and the systems
that offer them. This section will call out examples of these
resources.*</font>

|                    |                                                                  |                                                                                                                                    |                                                                                                                                                   |                                                                                                      |
|--------------------|------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Resource           | Owner                                                            | Description                                                                                                                        | Availability                                                                                                                                      | Source System                                                                                        |
| *(sensor name)*    | *Organization that owns/ manages resource*                       | *Short description of the resource*                                                                                                | *How often the resource is available*                                                                                                             | *Name of system which provides resource*                                                             |
| Metadata writer    | Initially OPeNDAP although it might be anyone who installs Hyrax | This person will need to be savvy WRT the data, the DPE and NcML                                                                   | If within OPeNDAP, at least for the duration of the project; outside OPeNDAP, unlimited                                                           | N/A                                                                                                  |
| Data server        | Initially opendap, later anyone who runs Hyrax, et c.            | Hyrax/BES/NcML handler                                                                                                             | All the time if within opendap; if running elsewhere, it will be hard to get write access to the server's file system                             | N/A                                                                                                  |
| A data set         | OPeNDAP or a remote site                                         | A local, or remote, data set                                                                                                       | A local data set is available all the time, a remote data set will depend on the serving site and if remote access is enabled in the NcML handler | N/A                                                                                                  |
| NcML Documentation | Unidata                                                          | This describes how NcML is supposed to be interpreted                                                                              | All the time                                                                                                                                      | unidata.ucar.edu                                                                                     |
| DPE                | OPeNDAP, initially at least                                      | This is the downstream data processing element that has metadata needs not satisfied by the content of the 'raw' data files/store. | Varies; we're planning on two DPEs both of which are in development now (April 2009)                                                              | Hyrax will be the initial provider of both planned DPEs; later additional things will play that role |