This is an updated set of notes regarding a software release. It is
tailored to a release of Hyrax, but the same process can be used for any
of our code.

1.  Look for any tickets that have been bound to the label 'fix release
    x.y.z' and make sure those are completed. Be sure to use both:
    - The HyraxKB Kanban board in OPeNDAP's JIRA.
    - The Hyrax board in our NASA JIRA.
2.  When we are ready to release, follow the process outlined in the
    sections below.
3.  Once done, close the board

## When we decide to release software

Use the developers at opendap.org list to send out notices to all the
developers about pending releases.

### Planned release

If this is a planned release, send out a notice at least two weeks in
advance to anyone who likely has made changes to the software. More lead
time is, of course, better. This will allow developers time to get their
code and documentation changes into github and onto the master branch in
time for the release.

Run Coverity!

### Emergency releases

If this is an 'emergency' release, then send out a notice to developers
as soon as the decision to release is made, since this will give a
chance for other developers to lets us know if there are new changes in
the master branch that are ready for release. if we don't get an email
from those developers of a particular component, then we should assume
that the code might not be 100% ready for release and we should use the
version tagged with/for the last release if possible.

Maybe run Coverity - depends on the scope of the changes.

## Release Prerequisites

Only start this process when hyrax-dependencies, libdap, bes and OLFS
*master* branches all building and passing their tests.

## Steps For A Major Release (ex: 1.16.x -\> 1.17.0)

1.  <span style="text-decoration: line-through;">Security Review for
    libdap, BES and OLFS</span> Now Handled By TravisCI
2.  Write a new X.Y web page. See the [web site admin
    notes](https://docs.opendap.org/images/f/fe/Admin_instructions.pdf)
    for info about this. It has important information about the menu
    links. Base this on the previous release page's structure/content.
3.  Follow the remaining steps for a *minor* release (below).

## Steps For A Minor Release (ex: 1.16.5 -\> 1.16.6)

Notes

- *Committing and pushing the various changes made for the source
  releases will trigger a binary build and those binaries will have the
  version numbers made in the source distributions update.*

<!-- -->

- *Perform these steps in the order stated as downstream activities rely
  on items created in earlier steps, such as the creation of the DOIs
  for libdap and BES are required when updating the OLFS README.md,*

### Perform the [Jira Release Process](Jira_Release_Process "wikilink")

### hyrax-dependencies: [Source Release For hyrax-dependencies](Source_Release_For_hyrax-dependencies "wikilink")

### libdap: [Source Release for libdap](Source_Release_for_libdap "wikilink")

### BES: [Source Release for BES](Source_Release_for_BES "wikilink")

### OLFS: [Source and Binary release for OLFS](OLFSReleaseGuide "wikilink")

### [Signing And Publishing C++ Binary Distributions](Signing_And_Publishing_C++_Binary_Distributions "wikilink")


**Note:** *This was already done for the java OLFS in the previous
steps.*

### [Build And Publish Docker Images For The Release](HyraxDockerReleaseGuide "wikilink")

### Install the new server on test.opendap.org

## [Update The Hyrax Release Web Page](Update_The_Hyrax_Release_Web_Page "wikilink")

## Send out a notice.

Here's a sample email message. I sent this to: "OPeNDAP Inc."
\<opendap-tech@opendap.org\>, opendap-announce@opendap.org,
opendap@opendap.org

Subject: We are pleased to announce the release of Hyrax 1.16.8

    Greetings!

    OPeNDAP is pleased to announce the release of Hyrax 1.16.8. This version of the DAP2 and DAP4 data server is available as RedHat 8 and CentOS 7 RPM packages, Docker containers and source code via GitHub. See https://www.opendap.org/software/hyrax/1.16#SoftwareDownloads for the details.

    Hyrax is Open Source.

    Below are the changes/updates for this version of the server.

    DMR++ Improvements - Direct subsetting access to data in S3

        • Various improvements for supporting FillValue in dmr++ lifecycle.
        • Improved support for arrays of type String.
        • Fixed trusted url bug in DMZ parser.
        • Added support for "empty" valued scalars with associated _FillValue metadata.
        • get_dmrpp Improvements
        • Added support for S3 hosted granules to get_dmrpp
    bes

        • Support for RHEL8
        • Improved the error messages returned to the client
        • Refactored get_dmrpp, application. Some test features are still broken but core functionality is working now.
        • Improved support for more GES DISC level 3 and level 4 products
        • Added coverage the support for the AIRS level 3 and GLDAS level products.
        • Patched bug in HDF5Array.cc introduced by the std::vector refactor
        • Added time.h header to ppt/SocketUtilities.cc
        • Modified fileout_netcdf handler to allow netcdf-3 responses to be up to 4GB in size.
            This behavior can be reverted by setting FONc.NC3ClassicFormat=true in the BES configuration
            (aka /etc/bes/site.conf file)
    olfs

        • Improved the DAP4 Data Request Form (now the default form for the server)
        • Patched bug where unexpected Authentication headers would trigger a redirect loop.
        • Fixed the broken service that was failing to deliver flat (not-data) files to clients.
        • Made the "Get As NetCDF-3" an "Get As DAP2 Binary" buttons on the DAP4 Data Request Form context sensitive. If the dataset in question contains variables whose data types are found in DAP4 and not in DAP2/NetCDF-3 then the buttons are disabled. A more complete solution is envisioned where the projected variables are assessed and if only DAP2/NetCDF-3 types are selected then the buttons would would be enabled. This fix is only a step in that more dynamic direction.
        • Tested on Tomcat-9.0.x

    Thank you for your continued support,

    The Hyrax development team: Nathan Potter, Dan Holloway, Sam Lloyd, Kodi Neumiller, Kent Yang and James Gallagher

    Acknowledgement: OPeNDAP thanks NASA and Raytheon for continued support of the Hyrax Data Server.
    —
    James Gallagher
    jgallagher@opendap.org

## Time to complete by release

Update and of the release checklists and the 'Time to complete...'
information below.

- Hyrax 1.12.2: The total time was planned as *3w 1d 5h* and turned out
  as *1w 2d 2h 10m*.
- Hyrax 1.15.1: *3d* calendar time
- Hyrax 1.16.2: *3d* effort