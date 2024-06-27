**Point Of Contact:** *[Nathan Potter](User:ndp "wikilink")*

## Description

Authentication mechanism compatibility

## Goal

To ensure that whatever develops in the line of user authentication and
access control for Hyrax is kept compatible with clients.

## Summary

Efforts to implement changes to user authentication and access control
activities for Hyrax can easily end up focusing on browser based
activities because this is the manner of interacting with a DAP server
that most humans experience. However the real power of the DAP server is
that it machine crawlable and accessible and can be utilized by a
collection of existing automation clients. It is important that we do
nothing to break these clients. It may be that core changes that can
break existing client need to be tied to newer versions of the DAP.

## Actors

- Existing DAP client software: OC library, Java-DAP
- Hyrax
- Developers

## Preconditions

The desire to keep the overall data system working.

## Triggers

Changes to authentication mechanisms for Hyrax

## Basic Flow

Evaluate potential authentication mechanism changes on existing DAP
client libraries. Mitigate changes by one or of:

- rollback
- update client
- hide change from older clients through protocol version

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