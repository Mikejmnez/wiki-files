NC-DAP Project Meeting

June 3rd 2008

The purpose of this meeting is to update the milestones based on our
work so far. Are they realistic? Was anything overlooked? This is also a
good time in the course of the project for everyone to meet each other
and to take care of routine business.

## Agenda

1.  NSF Annual Report Due
2.  NSF/NMI build and test system
3.  Quick review of the milestones from the kickoff meeting
4.  Translation:
    1.  short-term: simplistic but correct translation
        1.  what to do about grids.
    2.  long-term: preserving netcdf semantics across the DAP protocol
        1.  this probably requires a discussion about standardized
            annotations.
5.  Support for translating to the classic model.
    1.  Should we maintain the existing nc-dap translation as an option
        for back compatibility?
6.  Removing the \[...\] URL prefix notation
7.  Limited selection for non-sequence data
    1.  Currently, selections only apply to sequence type, but it should
        be possible to apply them to certain kinds of arrays as well.
8.  extending DAP data model (e.g. enumerations?)
9.  library proliferation
    1.  The current integration requires hdf5, dap, curl, xml2, and
        zlib/szlib.
10. **Revisit the Milestones and modify as needed**

## Report

The report: [NC-DAP Project Meeting Report 3 June
2008](http://docs.google.com/Doc?id=ddkdb64s_1gswnf7cz).

## Revised milestones

1.  Deliverables:
    1.  libdap++/libnc-dap + netcdf 3 data model **7/2008** Release
        netcdf version 4.1 beta
    2.  Ocapi (supporting DAP2) + glue code + netcdf 3 data model
        **12/2008** Release netcdf version 4.1
    3.  Ocapi (supporting DAP3.3) + glue + netcdf 4 data model
        **6/2009**
    4.  Testing, beta release **12/31/2009**
2.  Other deliverables: (these are later dates than in the meeting
    notes)
    1.  DAP3.3 \~~Specification\~~ Design **9/2008**
    2.  libdap++ for DAP3.3 **12/2008**
    3.  Ocapi for DAP3.3 **3/2009**
    4.  netCDF 4.x server which uses DAP3.3 **9/2009**