Integration of DAP and netCDF

## Project administration activities

- 11/2/07: Kickoff meeting. Full notes at Google Docs:
  <http://docs.google.com/Doc?id=ddkdb64s_0c5bfmw>

1.  There are a number of issues regarding DAP2 and the forthcoming
    netCDF4 (Common Data Model) work that will need to be resolved. We
    have long thought that this would have happened by now with the
    development of DAP4, but that development has stalled. For this
    project, we will need to start work on DAP4 again. *Action: Make a
    DAP4 page that can be referenced by all of the projects that are
    affected by it so that their efforts can be coordinated.*
2.  Deliverables:
    1.  libdap++/libnc-dap + netcdf 3.7 **3/2008**
    2.  Ocapi (supporting DAP2) + glue code + netcdf 4.x **9/2008**
    3.  Ocapi (supporting DAP3.x) + glue + netcdf 4.x **3/2009**
    4.  Ocapi (Supporting DAP4) + glue + netcdf 4.x **9/2009**
    5.  Testing, beta snapshot released **12/31/2009**
3.  Other deliverables: (these are later dates than in the meeting
    notes)
    1.  DAP4 Specification **3/2009**
    2.  libdap++ for DAP4 **6/2009**
    3.  netCDF 4.x server which uses DAP4 **9/2009**
4.  Licensing: Unidata will release netCDF using the MIT Open Source
    license while OPeNDAP will keep Ocapi, libdap++, and libnc-dap under
    LGPL. Use of libnc-dap will be fazed out at the end of this project.

## related documents

- \[<http://docs.opendap.org/images/4/4a/NetCDF_OPeNDAP_Integration_Project_Ki>....doc
  NetCDF OPeNDAP Integration Project Kickoff Meeting Notes\]
- [Progress report for 3/2008 from
  Unidata](http://docs.opendap.org/images/4/43/SDCIprogress080401.pdf)