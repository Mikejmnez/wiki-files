This page provides access to the presentations and other materials used
at the Australian Partnership for Advanced Computing (APAC) and
Australian Bureau of Meteorology (BOM) workshops in Perth and Melbourne
on 12 and 15 to 18 October 2007

## Australian Partnership for Advanced Computing Meeting

Presentations from the Australian Partnership for Advanced Computing
(APAC) OPeNDAP Workshop (held Friday 12 Oct 2007)

- [Introduction and
  background](http://www.opendap.org/support/APAC_2007/OPeNDAP_APAC_intro_20071012.ppt)
- [DAP
  Servers](http://www.opendap.org/support/APAC_2007/APAC_section_2r3.ppt)
- [DAP Clients and client-side
  issues](http://www.opendap.org/support/APAC_2007/APAC_section_3r3.ppt)

## Australian Bureau of Meteorology Meeting

The [Australian BOM System Administrator's Agenda and
Presentations](Australian_BOM_System_Administrator's_Agenda_and_Presentations "wikilink")

The [Australian BOM Software Developer's Agenda and
Presentations](Australian_BOM_Software_Developer's_Agenda_and_Presentations "wikilink")

## Virtual Machine for tutorials

For each of the workshops, attendees needed access to a computer running
Windows XP or Vista or an Intel Mac running OS/X (unfortunately there
was no support for the PowerPC Mac). The workshop attendees used a
virtual host running SLAX Linux for the hands-on material. The virtual
host was provided on CD-ROM, but it can be downloaded (see below). The
host contains a complete development environment along with a pre-built
and installed server and sample client software. To run the virtual
machine, you need to download a copy of [VMware
Player](http://www.vmware.com/products/player/) (free) or
[Workstation](http://www.vmware.com/products/ws/)(pay ware; 30-day
evaluation period) for Windows XP or Vista or [VMware
Fusion](http://www.vmware.com/products/fusion/)(pay ware) for Intel Mac.

The workshop's version of the virtual machine ISO is 1.6 and can be
found at [OPeNDAP Tutorial
1.6](http://www.opendap.org/pub/vm/opendap_tutorial_1_6.iso). The size
is around 590MB so you'll need a fast connection to get this.

It is also possible to bypass the Virtual Machine by downloading the
software, setting up build and development environment and then
compiling from sources. We used only software that was released at the
time of the workshops.

What's included on the VM:

- KDE desktop; Firefox
- A development environment including make, gcc, g++, g77, bison, flex,
  ANTLR, et cetera.
- Libraries: readline, curl
- Web servers:
  - Apache 2.2.x
  - Tomcat 5.5.12
- Ant 1.7
- JDK 1.5
- OPeNDAP DAP Sources (All of these compiled and installed in the order
  listed):
  - libdap 3.7.7
  - libnc-dap 3.7.0
  - bes 3.5.1
  - dap-server 3.7.4
  - netcdf_handler 3.7.6
  - freeform_handler 3.7.5
- OPenDAP Java binaries:
  - ODC plus a hand tooled install and script for starting it
  - olfs-1.2.3 (java binary; opendap.war file installed in Tomcat's
    webapps directory
- Other sources:
  - netcdf 3.6.2 - you need to build and install this before building
    the netcdf handler above
  - nco 3.9.2 - you need to build and install this after building the
    libnc-dap code above; we might have the attendees do this build
    depending on time

This VM was used for both the APAC and BOM workshops, so it contained
some things that are targeted at the BOM System Admin and Developer
Workshops and were not completely relevant to the APAC workshop. For
example, If all you want to do is look at some clients, you can skip all
the downloads & builds for the server parts. In that case you could get
the ODC and NCO clients. For NCO you'll need to get the various support
code or a binary build - Google "NetCDF Operators". We will also spend
some time demonstrating the web interface and reading data into a
spreadsheet (Excel, Spreadsheet, et c.). Most computers include a
spreadsheet and there's clearly no need to download one specially for
the workshop material!