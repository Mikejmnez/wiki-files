Back: [AIS Using NcML](AIS_Using_NcML "wikilink") or [BES Aggregation
using NcML](BES_Aggregation_using_NcML "wikilink")

## Goal

<font color="green" size="-2">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

Virtual data sets configured by a data provider work just like 'real'
data sets, but have desired metadata that the real (raw) data lack.

## Summary

<font color="green" size="-2">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

Once a data provider has set up Hyrax with the NcML handler and
configured one or more virtual data sets, a user can access a those if
they have access to a URL to the *.ncml* file. The user sees the virtual
data set as if it is real. Using the virtual data set is no different
than using any other data set.

## Actors

<font color="green" size="-2">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

1.  Primary actor: a downstream data-processing element (DPE)
2.  BES
3.  NcML handler
4.  a *.ncml* file (one per virtual data set)
5.  a client connecting to the server
6.  other data handlers for the BES
7.  'real' data sets that work with the other data handlers loaded in
    the BES

In this the DPE requests data using a DAP URL that is handled by the
NcML handler which then uses information in the NcML file to access some
other data store (like a file) using one of the other BES handlers and
merges the result with information in the NcML file itself.

## Preconditions

<font color="green" size="-2">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

1.  The BES, other data handlers and the matching data sets have to be
    appropriately configured.
2.  The data provider has written one or more *.ncml* files which
    reference data sets local to the BES
    1.  We can actually handle access to remote resources, too, but
        there will need to be appropriate safe-guards in place to
        prevent client-side capabilities from being misused. In this
        case, the notion that the BES has to have other handlers and
        data is false -- there could only be the NcML handler and
        *.ncml* files that reference remote data resources.
3.  The client/DPE has a URL to one or more *.ncml* files.

## Triggers

<font color="green" size="-2">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

Using the client/DPE is the trigger for this use-case.

## Basic Flow

<font color="green" size="-2">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

1.  The NcML handler is passed the path name to the NcML file and one of
    the GetDDX, GetData, et c., entry points is activated.
2.  It opens the NcML file and sees which other resources to access to
    get the *basic information*
3.  It then examines the remaining information in the NcML file and
    augments the *basic information* given the semantics of the handler
    entry point (Get DDX, et c.)
4.  The result is returned

## Alternate Flow

<font color="green" size="-2">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

1.  If the NcML file is not found, return a HTTP 404 error.
2.  If the NcML file exists but the referenced data within it cannot be
    found, return a 500 or a 404, depending.
    1.  return a 500 error if the resource is local and does not exist
    2.  return a 404, or whatever error the remote server returned, for
        remote resources that cannot be found (rationale: If a NcML file
        has a pathname wrong, that is a server configuration error but
        if a remote server returns an error, that could be transient -
        the server is down - or indicate that a document has moved).
3.  If the NcML is malformed, return a 500 error.

## Post Conditions

<font color="green" size="-2">*Here we give any conditions that will be
true of the state of the system after the use case has been
completed.*</font>

## Activity Diagram

<font color="green" size="-2">*Here a diagram is given to show the flow
of events that surrounds the use case.*</font>

## Notes

<font color="green" size="-2">*There is always some piece of information
that is required that has no other place to go. This is the place for
that information.*</font>

It's easiest to build this handler so that it works only with local
files since that will not need to include any client code, thus
eliminating any security concerns over having a server talk on the
network in response to its input from a remote client. But having that
additional capability will open the system to servers that only provide
virtual data sets, a really cool thing. I propose that we make the NcML
handler use a switch that disables this feature by default. If a site
wants the extra capability, they can add it easily. Remote resources can
be added to the NcML easily enough.

More: After looking at the basic use case for adding metadata for
projects like REAP and IOOS, I think we need this feature, even if we do
not distribute it in the first versions. See [Add the NcML handler to
the BES](Add_the_NcML_handler_to_the_BES "wikilink") in the Notes
section for more information.

## Resources

<font color="green" size="-2">*In order to support the capabilities
described in this Use Case, a set of resources must be available and/or
configured. These resources include data and services, and the systems
that offer them. This section will call out examples of these
resources.*</font>

|                 |                                            |                                     |                                       |                                          |
|-----------------|--------------------------------------------|-------------------------------------|---------------------------------------|------------------------------------------|
| Resource        | Owner                                      | Description                         | Availability                          | Source System                            |
| *(sensor name)* | *Organization that owns/ manages resource* | *Short description of the resource* | *How often the resource is available* | *Name of system which provides resource* |