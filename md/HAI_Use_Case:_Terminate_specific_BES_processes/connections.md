**Point Of Contact:** *[Nathan Potter](User:ndp "wikilink")*

## Description

The Administrator (Admin) uses the HAI to terminate specific BES
processes.

## Goal

By allowing the Admin to terminate BES processes we enable a human to
easily clean up hung or damaged BES connections/processes.

## Summary

Once the Administrator selects a BES that's connected to the instance of
Hyrax that is being managed they can choose a BES processes to
terminate.

## Actors

- Administrator
- HAI
- Hyrax
- BES

## Preconditions

- The Administrator has an authentication privileges for an HAI.

## Triggers

For reasons not specified here, an Admin needs to stop a BES
process/connection.

## Basic Flow

1.  [The Admin logs into the
    HAI](HAI_Use_Case:_Administrator_Logs_into_the_Hyrax_Admin_Interface "wikilink")
2.  [The Admin chooses a BES and views it's
    processes/connections.](HAI_Use_Case:_Examine_BES_processes/connections "wikilink")
3.  The Admin chooses a BES process/connection to terminate.
4.  The Admin clicks a button to terminate the selected
    process/connection.
5.  The page (or fragment thereof) is refreshed to show the updated list
    of BES processes/connections.

## Alternate Flow

<font size="-2" color="green">*Here we give any alternate flows that
might occur. May include flows that involve error conditions. Or flows
that fall outside of the basic flow.*</font>

## Post Conditions

The BES process indicated by the Admin has been terminated.

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