A one-hour plus meeting over the phone.

## Agenda

Agenda for 2009-01-08 Teleconference

- Grant status, schedule, milestones (James, 5 minutes)
- Current status of Unidata effort (Dennis, 5 minutes)
- Current status of OPeNDAP effort (Rikki, 5 minutes)
- Ocapi issues (all, 30 minutes)
- Summary and action items (all, 5 minutes)

## Report

### Grant status

We're pretty close to being on track for our milestones as described in
[NC-DAP Project Meeting 3 June
2008](NC-DAP_Project_Meeting_3_June_2008 "wikilink"). We've not quite
made the 1.2 deliverable (Ocapi (supporting DAP2) + glue code + netcdf 3
data model 12/2008 Release netcdf version 4.1) but that should be out in
the next month, at least as a snapshot. There's a fair amount of the DAP
3.3 stuff implement in libdap++ although some key parts for this project
need coordinated effort from both opendap and Unidata to get done.

### Unidata effort

Dennis has modified the Ocapi code so that it reads the response into
memory and then either uses pointers or computed offsets into that block
of XDR-endoded data instead of allocating new memory and copying values
into a 'data value tree' with a structure based on the data set's DDS.
The current Ocapi code does the latter and, while it will work in
theory, in practice, the resulting data tree is very complex for certain
data sets (e.g., those that contain arrays of structures).

### Ocapi status

Rikki has release Ocapi 1.4.2 (just prior to the AGU meeting). This
version of the library contains support for various 'usability' features
like HTTP basic and digest authentication, SSL authentication and Proxy
servers.

### Ocapi & Other issues

#### The *Array of String* issue

The current netCDF handler represents an array of char found in a
netCDF3 file as an array of one character strings. This makes writing
clients laborious because when client authors cannot assume that an
array of String is actually an array of characters, since it's possible
that the data set is not a netCDf file and really does contain and array
of strings. So, clients must be written to scan these to determine if
they are in fact arrays of single characters or real arrays of strings.

This should be addressed by fixing the handler and releasing it so that
a client can examine the DAP version and know that at or past a certain
DAP version, an array of single characters will be represented as either
a String or an array of Bytes (we're not sure which, but there's and
action item to sort that out) and that such a variable will also contain
a new attribute 'string_length' that can be used to determine the length
of the string without having to read the string in order to figure out
how big it is.

> Ah. I think ([jimg](User:Jimg "wikilink") 15:20, 8 January 2009 (PST))
> I remember why Reza did this. If you externalize an array of char as a
> String, there's no way to know how many characters there are in that
> string from just looking at the DDS. So code like the netCDF Client
> library cannot provide the information that a netCDF application needs
> to allocate the memory needed to store that value. The attribute will
> solve the problem. We should encourage all handler authors to include
> this attribute with any string variable whenever they can.

#### How libnc-dap operates

We talked about how libnc-dap (aka the netCDF client library) works. It
adopts the idea of opening a data set by using the URL, maybe with a
constraint, to read metadata (DAS & DDS) for the data set from a server.
It then builds an internal representation of that data set based on the
information it gets and uses that internal information to answer
virtually all of the netcdf 3 API's inquiry calls. When the client makes
a call to get data, the library builds a DAP constraint expression based
on the variable ID (which is used to index into the locally stored
information about the remote data set read during the 'open' call) and
on any other information passed to the netCDF API function. It then
combines that constraint expression with the base URL given in the
nc_open() call (and this may involve combining the constraint just built
with one supplied as part of the URL) to retrieve the data. Since the
netCDF API can access only one variable at a time, each data access
through libnc-dap is for just one variable at a time.

#### Dennis' new Ocapi code

It sounds like this new code is a big plus in that it's more efficient
and it works for the Array of Sequences case - thecurrent code does not.
This should be checked into SVN as a tag. Rikki can read it over and we
can sort out what to do next: Adopt it most likely.

#### Groups and Shared Dimensions

It seems from the discussion that Groups (aka namespaces) and Shared
dimensions so hand in hand. Another important point made in the
discussion was that shared dimensions are not the same as shared
coordinate variables.

The DAP Grid type provides a mechanism to specify coordinate variables.
We can make these more general by relaxing some constraints on Grid.
Currently a Grid is exactly one N-dimensional array with N
one-dimensional *maps* or *coordinate variables* (adopting DAP and
NetCDF3 vocabulary). We can:

1.  share the maps/coordinate-variables by allowing more than one array;
2.  relax the requirement that the arrays all be N-dimensional (but
    instead be \<= N-dimensional);
3.  expand the maps so that they are no longer limited to one-dimension

In NetCDF4 (aka the Common Data Model) shared dimensions are bound to a
Group.

We need to work out the details of this within the context of DAP!

## Actions

*In lieu of mnilestones we set out action items which all need to be
completed by/in 3/2009*

- Dennis: Check in new Ocapi code to SVN. Check it in as a tag. For our
  code that means that you use *svn cp .
  <http://scm.opendap.org/svn/tags/Ocapi/><some name>* **Done**

<!-- -->

- Dennis, Rikki, James: Design and implement a new API that can layer
  over the top of the existing Ocapi functions/structures that will be
  based on the same concepts used to build the netCDF3/4 API. \[I'm a
  little concerned in looking over these that we're piling a lot on in a
  short time and if I had to cut any of these, it would be this one,
  although I see its long-term importance.\]

<!-- -->

- James, John, Dennis: Design Group and shared dimension support for DAP
  based on the idea of a Group as a Namespace and shared dimensions as
  an object that can be used with an expanded Grid type to represent not
  only shared coordinate variables but also swaths.

<!-- -->

- James: Implement this design in libdap

<!-- -->

- Rikki and Dennis: Based on what is learned in libdap, revise the
  design (probably with John and James) and implement in Ocapi.

<!-- -->

- James, John, Dennis: Fix libdap handler's char array handling. I'll do
  the coding but either try to mimic John's code or adopt Dennis' idea
  to use Byte arrays. [jimg](User:Jimg "wikilink") 14:37, 23 January
  2009 (PST) **Done** I built code to emulate the way TDS handles
  NC_CHAR variables in netCDF files.

<!-- -->

- James: Post the link to the DAP 3.3 design page(s). **Done**