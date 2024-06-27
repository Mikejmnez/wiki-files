**Point Of Contact:** *[Nathan Potter](User:ndp "wikilink")*

## Description

A user authenticates with a Hyrax server and gets a session.

## Goal

Provide user identification information to Hyrax/Tomcat in order to
allow Hyrax Admin's to manage access to datasets.

## Summary

- A user attempts to access a restricted part of the Hyrax server.
- The server presents an authentication challenge.
- The user provides credentials.
- The user is granted access.

## Actors

- User - typically software, either an automation or something driven by
  a human (like a browser)
- Hyrax

## Preconditions

- The user want to access data held in a Hyrax server.
- The Hyrax server has been configured to consider this data restricted.

## Triggers

The user attempts to access the restricted data set on Hyrax.

## Basic Flow

1.  A user attempts to access a restricted part of the Hyrax server.
2.  The server returns an authentication challenge.
3.  The user provides credentials.
4.  The user is granted access.

## Alternate Flow

1.  A user attempts to access a restricted part of the Hyrax server.
2.  The server returns an authentication challenge.
3.  The user provides credentials.
4.  The user is denied access.

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