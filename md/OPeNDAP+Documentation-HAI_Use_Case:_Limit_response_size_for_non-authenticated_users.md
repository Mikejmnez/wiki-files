**Point Of Contact:** *[Nathan Potter](User:ndp "wikilink")*

## Description

Limit data response sizes.

## Goal

Reduce server loading by limiting response sizes for non-authenticated
users. This limitation may be extended to authenticated users in certain
roles in the future.

## Summary

When a user makes a data request the server checks to see if they are
authenticated. If they are not then the server determines the size of
the requested data object, and if it is larger than a configurable
threshold then the server will return a response object with an error
and instructions on how to limit the response size.

## Actors

- User
- Hyrax

## Preconditions

A user wants to access data held at a running Hyrax sever.

## Triggers

A user attempts to access data held at a running Hyrax server.

## Basic Flow

1.  The user makes a large data request of a Hyrax server.
2.  The server receives the request.
3.  The server checks to see if the user is authenticated and determines
    that the user is not authenticated.
4.  The server determines the size of the requested data object.
5.  The server compares the size of the requested data object to a
    threshold value, and finds the data request to be larger
6.  The server returns an error object that either contains or
    references instructions on how to reduce the size of the requested
    data object.

## Alternate Flow

### Non-authenticated user makes successful request

1.  The user makes a large data request of a Hyrax server.
2.  The server receives the request.
3.  The server checks to see if the user is authenticated and determines
    that the user is not authenticated.
4.  The server determines the size of the requested data object.
5.  The server compares the size of the requested data object to a
    threshold value, and finds the data request to be smaller
6.  The server returns the regular data object for the request..

### Authenticated user makes request

1.  The user makes a large data request of a Hyrax server.
2.  The server receives the request.
3.  The server checks to see if the user is authenticated and determines
    that the user is authenticated.
4.  The server returns the regular data object for the request..

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