Back: [AIS Using NcML](AIS_Using_NcML "wikilink") or [BES Aggregation
using NcML](BES_Aggregation_using_NcML "wikilink")

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier. Consider goal-driven use case
name.*</font>

NcML_Handler_Configuration_00

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

A person who is configuring a BES daemon adds the NcML handler and
*viola*, the AIS and whatever else we do with NcML works.

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

The NcML handler creates a synthetic data set using some extra
information, written using NcML, and an existing data set. The new
information is added to, or replaces, the existing information in the
data set.

NcML has the syntax to add to, supplant or remove attributes from a data
set. It can also add new variables to a data set. In the design that
goes with this use case we are proposing to implement only the attribute
part of the NcML syntax initially and then add the capability to add
variables later.

This use case covers just how the NcML handler is added to the BES and
how it treats the *.ncml* files to make the synthetic data sets.

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

1.  The BES is installed and configured, except for the NcML handler
2.  There are data sets
3.  There is at least one NcML file under/within the BES' DocumentRoot

## Triggers

<font size="-2" color="green">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

The trigger is the need to add this feature to the Hyrax data server. In
the case of the REAP or IOOS projects, the NcML Handler will implement
the AIS that will be used to build documents these projects need.
Satisfying the requirements for those projects will be the triggers. In
general, the trigger will be the need to use the features of the AIS
with one or more data sets.

If the NcML handler supports remote data sets, then a second type of
trigger might be the desire to provide additional information (using the
AIS) for a remote data set.

## Basic Flow

<font size="-2" color="green">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

1.  User (installer/configurer) installs (builds and installs or
    installs a binary) of the NcML handler code
2.  The user edits the BES.conf file so that any *.ncml* files are
    routed to the NcML handler
    1.  To do this, the NcML handler is bound to files matching a
        certain regular expression int eh bes.conf file just as is the
        case with other handlers.
    2.  The NcML file(s) are stored in a directory under the BES's
        DocumentRoot.
3.  The user (re)starts the BES

Here are two relevant lines from the *bes.conf* file before adding the
handler to the server:

    BES.modules=dap,cmd,ascii,usage,www,nc,h4
    BES.Catalog.catalog.TypeMatch=nc:.*\.(nc|NC)(\.gz|\.bz2|\.Z)?$;h4.*\.(hdf|HDF|eos)(\.gz|\.bz2|\.Z)?$;

We would add a ncml handler to the BES.modules line and a regular
expression for it to the Typematch line like this:

    BES.modules=dap,cmd,ascii,usage,www,nc,h4,ncml
    BES.Catalog.catalog.TypeMatch=nc:.*\.(nc|NC)(\.gz|\.bz2|\.Z)?$;h4.*\.(hdf|HDF|eos)(\.gz|\.bz2|\.Z)?$;ncml:.*\.ncml

Thus any request with file that ends in *.ncml* (i.e., that matches teh
regex *.\*\\ncml*) as its target will be handled by the NcML handler.

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

The basic system design so far assumes that the NcML files will
reference data sets using <file:/> URLs and that may introduce a problem
with resources early on because that will mean that the data must be
local to whomever is writing the NcML files. This will mean that either
we have to serve all the data to experiment with the system or we have
to rely on others to write that metadata for us. Either option seems
doomed to fail. If we provide the NcML handler with the capability to
work with remote data, even if it's horribly inefficient, we can use the
full data sets and experiment with different metadata microdocuments,
then have installed at the server sites ones we're fairly confident are
good. The extra time spent adding this feature will almost certainly be
a sound investment.

The downside is that we have to be able to remove this from anything we
distribute unless we put the entire client-side of libdap through a
security audit.

## Resources

<font size="-2" color="green">*In order to support the capabilities
described in this Use Case, a set of resources must be available and/or
configured. These resources include data and services, and the systems
that offer them. This section will call out examples of these
resources.*</font>

### Other Resources

|                                    |                                                 |                                                                                                                                       |                                                                                                                                                                    |                                                      |
|------------------------------------|-------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------|
| Resource                           | Owner                                           | Description                                                                                                                           | Availability                                                                                                                                                       | Source System                                        |
| *Name*                             | *Organization that owns/ manages resource*      | *Short description of the resource*                                                                                                   | *How often the resource is available*                                                                                                                              | *Name of system which provides resource*             |
| BES & the NcML handler             | Data Provider                                   | The data server                                                                                                                       | Once installed, it can be hard to get the admin to make changes                                                                                                    | N/a                                                  |
| Data set                           | The data provider                               | This is the thing that we're adding information to                                                                                    | Depends on how easy it is for the metadata writer to get access to the server's/data's file system                                                                 | Hardware running the server and/or staging the data. |
| Metadata writer                    | Hopefully not owned, since this is a person ;-) | This is the person who figures out which XML micro documents are needed for a particular data set                                     | Hard to tell at this stage.                                                                                                                                        | The institution that hosts the data                  |
| In-House (opendap) metadata writer | opendap                                         | Like the on-site metadata writers, this person figures out what metadata are needed and adds that to files served by the NcML handler | In this case, the data would be local *or* the NcML handler would be configured to work with remote resources (using <http://> URLs in addition to <file:/> URLs). | opendap/IOOS/REAP                                    |