This page describes how to use the Trac system for projects run by
OPeNDAP. Add to this as we learn more about Trac.

## Tickets

- Change the Status of a ticket from 'new' to 'accepted' when you start
  to work on the ticket. It's fine to start work on a ticket, drop it
  and come back to it later. We can use the set of tickets marked
  'accepted' to get a handle on which problems are being actively
  pursued.
- There's currently no way to relate one ticket to another (you could
  with Bugzilla using the 'depends on' field). However, you can edit the
  original description of the ticket at any time and include and you can
  include hyper links to other bug reports using `#<`<number>`>`
  anywhere that wiki text is allowed.

## Milestones

- For small projects, use a single Milestone to define the project.
  Break down the project into a collection of tickets that are in the
  'Task' category and assign them to the milestone. If new tasks are
  added (when are they not...), be sure to mark them as part of this
  milestone. This will make the Roadmap display accurately mirror the
  real status of the project.
- Instead of making the milestone text a list of items (which is what I
  started out doing), make the text a short paragraph describing the
  milestone's overall objective. Then create a set of tickets with
  Category set to Task to describe the things that you think will need
  to be done to accomplish the milestone.
- For larger projects, use several milestones.

## Wiki

- Don't use the Trac wiki; having two active wikis is confusing.