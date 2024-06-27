**Point Of Contact:** *[Nathan Potter](User:ndp "wikilink")*

## Description

Allows the Administrator to control the BES logging framework.

## Goal

Improving Admin problem solving by allowing the Admin to easily start
and stop debug logging for the BES and its component, and to capture to
a file or stream the debugging output of the BES.

## Summary

The Admin logs into the HAI and uses the HAI interface to control and
direct the output of the BES logging framework.

## Actors

- Administrator
- HAI
- Hyrax (OLFS & BES(s))

## Preconditions

The Administrator has an authentication privileges for an HAI.

## Triggers

The Admin wants to view and control the BES logging output.

## Basic Flow

1.  [The Admin logs into the
    HAI](HAI_Use_Case:_Administrator_Logs_into_the_Hyrax_Admin_Interface "wikilink")
2.  The Admin uses the HAI interface and chooses to control the BES
    logging activity.
3.  In the returned page (or portion thereof) the HAI provides an
    interface through which the Admin can:
    1.  Control the level of logging output for different package/class
        components of the BES
    2.  Save the logging output to a local file.
    3.  Stream the logging output to the HAI interface.
    4.  View existing log files for the BES.

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