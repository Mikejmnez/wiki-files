**Point Of Contact:** *[Nathan Potter](User:ndp "wikilink")*

## Description

The Administrator logs into the Hyrax Admin Interface.

## Goal

Provide a secure login and session for Administrators to operate the
Hyrax Admin Interface.

## Summary

The Administrator uses a web browser to access the URL for the Hyrax
Admin Interface. When accessed the server returns a secure
authentication challenge. Once the Administrator has provided correct
authentication credentials all subsequent interactions with the HAI are
secure (encrypted) until the session times out or the Administrator logs
off.

## Actors

- Administrator

## Preconditions

The Administrator has access too, and authentication credentials for, a
running Hyrax server.

## Triggers

One or more of:

- Hyrax needs re configuration.
- Hyrax needs to be restarted.
- The Administrator needs/wants to view the Hyrax logs.

## Basic Flow

1.  The Administrator points their browser at the URL for their HAI.
2.  The HAI returns a secure page containing an authentication
    challenge.
3.  The Administrator enters their authentication credentials.
4.  The HAI Administrators credentials are recognized.
5.  From this point on all pages sent from the HAI to the Administrator
    are secured through encryption (https??)
6.  The HAI returns it's home page.

## Alternate Flow

1.  The Administrator points their browser at the URL for their HAI.
2.  The HAI returns a secure page containing an authentication
    challenge.
3.  The Administrator enters their authentication credentials.
4.  The HAI Administrators credentials are NOT recognized.
5.  The HAI returns it's an authentication failure page.

## Post Conditions

1.  The Administrator has an active session with the HAI which provides
    secure pages for controlling a Hyrax instance.

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