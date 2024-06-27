**Point Of Contact:** *[Nathan Potter](User:ndp "wikilink")*

## Description

The Administrator uses the HAI to choose a BES and can then choose to
stop the BES nicely , immediately , or start the BES.

## Goal

To allow the Administrator to control each BES in use with a particular
Hyrax instance.

## Summary

Once logged into the HAI the Administrator selects a BES that's
connected to the instance of Hyrax that is being managed. The page (or
part thereof) returned for the selected BES provides a simple interface
(say 3 buttons) that allow the Admin:

1.  Stop the BES nicely (in which the BES would wait for all open
    connections to close naturally).
2.  Stop the BES immediately (in which the BES will stop all of it's
    child processes immediately).
3.  Start the BES if it's stopped..

## Actors

- Administrator
- Hyrax
- HAI
- BES

## Preconditions

The Administrator has authentication privileges for an HAI.

## Triggers

The Administrator needs to stop or start a BES instance.

## Basic Flow

1.  [The Admin logs into the
    HAI](HAI_Use_Case:_Administrator_Logs_into_the_Hyrax_Admin_Interface "wikilink")
2.  The Administrator uses the HAI to navigate one or more pages to
    select a BES associated with the Hyrax instance of the HAI.
3.  Once selected the Admin the HAI provides a page (or fragment
    thereof) which contains the StopNice, StopNow, and Start buttons.
4.  The Admin presses one of the three buttons: StopNice, StopNow, or
    Start.

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

[Category:HAI](Category:HAI "wikilink")