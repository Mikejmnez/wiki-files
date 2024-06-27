## Software Development Rules FAQ

**Are there any rules to follow when performing development tasks?**

Yes, there are several rules that must be followed in order for the
tracking and control system to function effectively and to permit work
to move from person to person in a seamless manner. If you take into
account that you are communicating and presenting your work to other
team members as you submit it, then you are likely to be following the
spirit of these rules.

## Ticket Rules

**Avoid performing work without a ticket**

If you feel you need to briefly work without a ticket (one day max)
while using your own judgment to chase down an issue you run into, you
can do so. This should not be a terribly frequent practice. Please
consider having a ticket added retroactively in such cases, particularly
if you want your work to be reflected in the reports that come from
Trac.

**When you start work on a Ticket, accept it via the Action area at the
bottom of the ticket**

It is ok to accept the ticket and stop work as other priorities compete.
This gives management a good perception of which tasks are currently
being actively pursued.

**When done with the work associated with a ticket, close it promptly**

For those tickets that are for bug fixes or new features, immediately
after checking in the associated code on the trunk is ideal.

**Preference ticket notations over scratch paper and/or memory when
keeping notes on your progress for a task**

Notating tickets frequently allows work to be seemlessly reassigned to
another person in mid-stream. It makes the team more flexible and allows
us to take better advantage of each others strengths.

**Use the Category ticket property in a consistent manner when adding a
new ticket or when changing the category of a ticket.**

From left to right in the Ticket Property area, we have Bug, Feature and
Task categories. Set the property according to the left-most category
that it fits into. If it is a bug, classify it as such. Otherwise, if it
is a feature classify it as such. If it is neither of those categories,
classify it as a Task. This keeps the categorization both consistent and
compatible with the automatic generation of data for the Release
Description Document at release time.

## Source Code Repository Rules

**Do not use the selective locking feature of Subversion's, except for
documents.**

Documents are stored under /doc - the designated place for sharing and
versioning documents via subversion.

**Enter appropriately descriptive text describing changes when checking
code into Subversion**

The string should be descriptive enough to place the changes in the
context of the ticket(s) the changes are associated with. This can be
implicit through the wording, explicity by referring to the ticket(s)
using wiki formatting in the subversion check-in string or both. We do
not place any restrictions on what this text includes. You can include
any text you wish as long as it includes information that associates it
with the ticket.

**Form and work from branches as necessary, but for short periods of
time only (30 days or less).**

Branches are transitory and formed with the intent to merge back to the
trunk at a point in the near future.

## Coding Rules

**Follow Java coding conventions as outlined below.**

Adherence to the conventions outlined below are required. In addition,
developers should attempt to adhere to the coding conventions outlined
by Sun Microsystems.

- Package names are all lower case and are prefixed by "org.opendap."
- Class names are nouns in !UpperCamelCase
- Interfaces are in !UpperCamelCase
- Methods names are verbs in lowerCamelCase
- Instances of classes are in lowerCamelCase
- Constants are all uppercase with words separated by an underscore