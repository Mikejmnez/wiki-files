**Point Of Contact:** [ndp](User:Ndp "wikilink")

## Description

<font size="-2" color="green">*Give a short descriptive name for the use
case to serve as a unique identifier.*</font>

The RDH receives and responds to a bes:showCatalog request for the
Datasets (ODBC Data Sources) that it is offering.

## Goal

<font size="-2" color="green">*The goal briefly describes what the user
intends to achieve with this use case.*</font>

## Summary

<font size="-2" color="green">*Give a summary of the use case to capture
the essence of the use case (no longer than a page). It provides a quick
overview and includes the goal and principal actor.*</font>

The BES receives a request for a catalog that it identifies as being
serviceable by the RDH.

The RDH uses it's cached list of ODBC Data Sources to build a
bes:showCatalog response and then returns the bes:response document to
the client.

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

## Triggers

<font size="-2" color="green">*Here we describe in detail the event or
events that brings about the execution of this use case. Triggers can be
external, temporal, or internal. They can be single events or when a set
of conditions are met, List all triggers and relationships.*</font>

- A client issues a bes:showCatalog command for the Datasets serviced by
  the RDH.

## Basic Flow

<font size="-2" color="green">*Often referred to as the primary scenario
or course of events. In the basic flow we describe the flow that would
be followed if the use case where to follow its main plot from start to
end. Error states or alternate states that might be highlighted are not
included here. This gives any browser of the document a quick view of
how the system will work. Here the flow can be documented as a list, a
conversation or as a story.(as much as required)*</font>

1.  The BES receives a request containing a bes:showCatalog command for
    the RDH holdings. <font color="red">(*How do we know it wants the
    RDH catalog? Will the RDH have it's own catalog? Or will it claim
    part of the resource URI space? Or something else?
    [ndp](User:Ndp "wikilink")*)</font><tt>

    <?xml version="1.0" encoding="UTF-8"?>

    <bes:request xmlns:bes="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">

      <bes:setContext name="errors">xml</bes:setContext>

      <bes:showCatalog node="/data/stuff/" />

    </bes:request></tt>

    <font color="red">*Make sure to update this to match what we actuall
    use! [ndp](User:Ndp "wikilink") 16:27, 28 April 2009 (PDT)*</font>
2.  In the BES, the RDH is identified as the service will provide the
    requested catalog response.
3.  The RDH constructs a catalog response containing a Dataset entry for
    each ODBC Data Source in it's cached list of Data Source names.
4.  The RDH serializes the response document object back to the BES
    client: <tt>

    <?xml version="1.0" encoding="ISO-8859-1"?>

    <response ns="http://xml.opendap.org/ns/bes/1.0#" reqID="[http-8080-1:16:bes_request]">

      <showCatalog>

       <dataset catalog="catalog" count="18" lastModified="2008-07-16T23:59:34" name="/data/stuff" node="true" size="782">

        <dataset catalog="catalog" lastModified="2008-05-29T23:31:54" name="dataSourceOne" node="false" size="7944799">

          <serviceRef>dap</serviceRef>

        </dataset>

        <dataset catalog="catalog" lastModified="2008-05-29T23:31:54" name="dataSourceTwo" node="false" size="7944799">

          <serviceRef>dap</serviceRef>

        </dataset>

        <dataset catalog="catalog" lastModified="2008-05-29T23:31:54" name="dataSourceThree" node="false" size="7944799">

          <serviceRef>dap</serviceRef>

        </dataset>

       </dataset>

      </showCatalog>

    </response></tt>

    <font color="red">*Make sure to update this to match what we actuall
    use! [ndp](User:Ndp "wikilink") 16:27, 28 April 2009 (PDT)*</font>

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

|          |               |                 |                                                                              |                           |
|----------|---------------|-----------------|------------------------------------------------------------------------------|---------------------------|
| Resource | Owner         | Description     | Availability                                                                 | Source System             |
| BES      | Data Provider | The data server | Assumed available as it is the framework from within which the RDH operates. | Hardware running the BES. |