**Point Of Contact:** *[Nathan Potter](User:ndp "wikilink")*

## Description

View/set user-roles and their mapping to URLs

## Goal

To provide Admin control of the relationships between user-roles and the
URL's offered by the Hyrax server.

## Summary

The Admin logs into the HAI and navigates to the page (or portion
thereof) that contains the user-roles interface. There the Admin can:

- create roles
- delete roles
- edit the URL pattern(s) that are mapped to this role.
- add a user to a role
- remove a user from a role.

## Actors

- Admin
- HAI
- Users

## Preconditions

- A Hyrax service running with users and authentication enabled.
- The Administrator has authentication privileges for the HAI.

## Triggers

The Admin needs to change the user-roles for an instance of Hyrax. This
could arise from the need to add a new user, remove a user, change a
user's access, etc.

## Basic Flow

1.  [The Admin logs into the
    HAI](HAI_Use_Case:_Administrator_Logs_into_the_Hyrax_Admin_Interface "wikilink")
2.  The Administrator uses the HAI to navigate one or more pages to the
    user-roles interface.
3.  Hyrax returns a page (or portion thereof) with the user-roles
    interface.
    In the user-roles interface the Admin can:
    - Create roles
    - Delete roles
    - edit the URL pattern(s) that are mapped to this role.
    - Add a user to a role
    - Remove a user from a role.

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