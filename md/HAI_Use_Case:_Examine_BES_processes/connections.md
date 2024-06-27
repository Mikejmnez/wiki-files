**Point Of Contact:** *[Nathan Potter](User:ndp "wikilink")*

## Description

The Administrator uses the HAI to choose a BES and then view a list of
its current processes/connections.

## Goal

To allow the Administrator to see, for each BES in use with a particular
Hyrax instance, how many processes/connections the BES has open.

## Summary

Once logged into the HAI the Administrator selects a BES that's
connected to the instance of Hyrax that is being managed. The page (or
part thereof) returned for the selected BES shows all of the
processes/connections that the BES has running or is servicing.

## Actors

- Administrator
- Hyrax
- HAI

## Preconditions

The Administrator has an authentication privileges for an HAI.

## Triggers

The Administrator wants to see the list of processes/connections for a
particular BES.

## Basic Flow

1.  [The Admin logs into the
    HAI](HAI_Use_Case:_Administrator_Logs_into_the_Hyrax_Admin_Interface "wikilink")
2.  The Administrator the HAI to navigate one or pages to select a BES
    associated with Hyrax instance of the HAI.
3.  Once selected the Admin can see the processes/connections in use by
    the BES.

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