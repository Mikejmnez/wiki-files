**Point Of Contact:** James

Back: [REAP Cataloging and
Searching](REAP_Cataloging_and_Searching "wikilink")

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier.*</font>

AddInformation00

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

The data provider would like a data set to be included in various online
catalogs. By adding information those catalogs require, that becomes
possible.

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

The data provider (DP) will look over the data set to get a general idea
of the domain and range of the data set. They would then use a guide or
some sort which will suggest which XML fragments they must write to
provide support for a specific metadata documet type (e.g., what must
they add so the data set will have enough information so the cataloging
system can build an EML document for Kepler).

Once the DP knows what information to provide, they go about the task of
writing it down in XML fragments and binding those to the data set's DAP
attribute set. They will do this using the NcML-based Ancillary
Information System (AIS). NcML is an XML notation which can be used to
add new attributes, or replace exiting ones, using files that are
external to the actual data set. NcML also provides a syntax for
defining new variables for an existing data set, although we won't use
that for this design. See [AIS Using NcML](AIS_Using_NcML "wikilink")
for information on the NcML-based AIS system planned for the WCS
interface to the Hyrax Data server.

Since many data sets consist of a collection of files where the content
is constant in space but varies over time (or exhibits similar
variations with other parameters), data providers might be faced with
the task of providing the same information tens, hundreds or thousands
of times over; hardly ideal. However, the NcML-based AIS system will
provide two ways to address this issue:

1.  The writer may use NcML to mark some information so that it is
    applied to a collection of files, typically all those in a certain
    directory tree; and/or
2.  The writer may choose to aggregate the files to create a new logical
    data set where each file is an array slice or tile. Attributes are
    then defined for the new data set once and apply to all subsets
    derived from that new logical entity.

Once the XML fragments have been added to the data set, the data
provider will need to see if they can be used to build a decent metadata
record. Some system/software will exist to automate this process and
will provide a way for the DP to determine if a working record can be
built using the fragments provided.

## Actors

<font size="-2" color="green">*List actors, people or things outside the
system that either acts on the system (primary actors) or is acted on by
the system (secondary actors). Primary actors are ones that invoke the
use case and benefit from the result. Identify sensors, models, portals
and relevant data resources. Identify the primary actor and briefly
describe role.*</font>

- The Data Provider (DP)
- The Data set
- The NcML-based Ancillary Information System (AIS)
- The information regarding which XML fragments are needed to satisfy
  different standards/formats (The Guide)

## Preconditions

<font size="-2" color="green">*Here we state any assumptions about the
state of the system that must be met for the trigger (below) to initiate
the use case. Any assumptions about other systems can also be stated
here, for example, weather conditions. List all preconditions.*</font>

- The data provider has installed/configured/tested Hyrax and is serving
  the data set.

<!-- -->

- The NcML AIS is part of that server (It will be a standard
  module/handler in the future, but for the time when these use cases
  are being used to drive the design, it will be brand new (and buggy
  ;-)). As a fallback, new features added to libdap 3.9.0 can be used
  with the existing and supported ancillary DAS to add new attributes to
  a data set. The ancillary DAS system/feature is no where near as
  powerful as the AIS, but for it can be used in a proof-of-concept
  fashion.

<!-- -->

- There exists the necessary documentation regarding what XML fragments
  to add for at least EML.

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

See the Summary section.

## Alternate Flow

<font size="-2" color="green">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

Aside from various errors in creating the XML fragments...

It might be that the data provider discovers, right after adding the
XML, that it's not sufficient to build the required information in the
required format and that they have done all of the things described in
the the Guide. In this case they have to root around and sort out what
needs to be added and then add that to both the Guide. If we develop a
separate software system to evaluate the completeness of the generated
EML (because there are valid EML documents that won't work with the
Kepler catalg search system) then that would also likely need to be
updated.

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

It would be best if the system/software that builds the record(s) is
also smart enough to use information already present in the variables
(data) in the data set in addition to the data set's attributes.
However, the initial version might do this not because it is actually
fairly complicated to program since the result is often a mini
rule-based system implemented in C++.

## Resources

<font size="-2" color="green">*In order to support the capabilities
described in this Use Case, a set of resources must be available and/or
configured. These resources include data and services, and the systems
that offer them. This section will call out examples of these
resources.*</font>

- Hyrax with the NcML AIS
- One or more data sets
- A DP who knows enough about the system to add the correct metadata
- The Guide; documentation that explains what information must be
  present to build records fo a specific format

### Other Resources

|                 |                                            |                                     |                                       |                                          |
|-----------------|--------------------------------------------|-------------------------------------|---------------------------------------|------------------------------------------|
| Resource        | Owner                                      | Description                         | Availability                          | Source System                            |
| *(sensor name)* | *Organization that owns/ manages resource* | *Short description of the resource* | *How often the resource is available* | *Name of system which provides resource* |