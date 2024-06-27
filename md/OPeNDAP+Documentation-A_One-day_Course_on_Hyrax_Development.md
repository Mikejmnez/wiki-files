Many of these slide sets show examples based on Hyrax running in Linux.
We have built a CentOS 6 64-bit virtual machine that runs under the
VMware hypervisor (Fusion, Workstation or Player will all work with this
VM) and it is available for download
[here](http://www.opendap.org/pub/vm/centos6). This VM contains a CentOS
6 machine configured according to our page on setting up a [CentOS box
for RPM production](ConfigureCentos "wikilink"). It also includes Hyrax
(from the 1.9 release branch) already built and tested. Additional items
from/for the course outlined here may also be on the VM, but if not,
just follow the directions to download them.

We have also built an older Virtual Machine that runs openSUSE 12.1 for
this tutorial and it's available
[here](http://www.opendap.org/pub/vm/miic). The VM will run in all of
the modern versions of Vmware's tools (Fusion, Workstation and Player).
This virtual machine contains Hyrax 1.8, also built and testes along
with other downloads from the tutorial. We've used it on OS/X (with
fusion and Player) and on Windows 7 (with Player). *In general, unless
you have a particular need for SUSE or Hyrax 1.8, use the newer CentOS 6
VM and Hyrax 1.9.*

## Introduction to OPeNDAP

An introduction to both OPeNDAP and the Data Access Protocol (DAP) data
model. (lecture - 20m)
[Slides](http://www.opendap.org/pub/vm/centos6/1_DAP_Intro_dwf_jhrg.ppt)

DAP and Grid Coverages. (lecture - 20m)
[Slides](http://www.opendap.org/pub/vm/centos6/1.1_Data_Model_and_Coverages.pptx)

DAP4 with DAP2 to DAP4 transition use cases. (lecture - 20m)
[Slides](http://www.opendap.org/pub/vm/centos6/1.2_DAP4_Overview_7_2014.pptx)

## The Hyrax Data Server Architecture

An overview of the architecture of the Hyrax server. This presentation
is intended for software developers. (lecture - 40m)
[Slides](http://www.opendap.org/pub/vm/centos6/2_HyraxArchitecture_v1.1.ppt)

## Running & Debugging the server

The VM is a CentOS 6 64-bit Virtual Machine that works with Vmware's
Fusion 7, Workstation 10 and Player. Older versions of those may work.
The login username is *opendap* and the password is *opendap*.

There is also an openSUSE 12.1 Linux 32-bit VM that works with Fusion 4,
Workstation 7 and player 3. The home account directory is
*/home/dev/OPeNDAP* but otherwise the two VMs are virtually (ahem)
identical. The username for the SUSE VM is *dev* and the password is
*foofoo*.

Start the virtual machine and Hyrax (hands-on 40m)
[Slides](http://www.opendap.org/pub/vm/centos6/3_Hyrax_Setup.ppt)

Break

Hyrax setup on the VM (hands-on - 20m)
[Slides](http://www.opendap.org/pub/vm/centos6/4_Checking_the_Server.ppt)

This shows how to use getdap and telnet to debug the server. These are
low-level command line tools that cut to the chase when the server seems
to be broken. Also note that both of these slide sets show you how to
use the bescmdln tool. The bescmdln tool is very useful for working with
the BES alone - note that we used it to test the BES *before* we started
tomcat and had Hyrax in its full form up and running.

The bescmdln tool has several options that are useful:

- It can use the SQL-like *set*, *define* and *get* commands
- it can also use the XML command syntax that the front-end also uses
- It can read sets of commands from text files (using the *-i* option)
- ...and those command files can also be used with another tool -
  *besstandalone* - which can be used to test server components with
  actually starting the BES.

For more information on *bescmdln* see:

- [Hyrax - Running bescmdln](Hyrax_-_Running_bescmdln "wikilink")
- [Hyrax - BES Client commands](Hyrax_-_BES_Client_commands "wikilink")
- [BES XML Commands](BES_XML_Commands "wikilink")

All of which you can find on the
[Developers](Hyrax#Developers "wikilink") section of the
[Hyrax](Hyrax "wikilink") documentation

## Extending The BES

Extending The BES: Commands, Response Objects, Handlers, and more
[Slides](http://www.opendap.org/pub/vm/centos6/5_BES_Extensibility.ppt)
(lecture)

- New commands (like our hello world example)
- New response objects
- New response handlers
- New request handlers (data handlers like netcdf, freeform, csv)
- Aggregation engines
- Methods of returning your data (return as netcdf)
- Reporters
- Exception Handlers
- Debugging

## Server Functions

This is an introduction to Server-side Functions. It is both lecture and
hands-on and works best if you have Eclipse, Emacs or another suitable
programming editor running.
[Slides](http://www.opendap.org/pub/vm/centos6/6_Server_Side_Functions.ppt)
(lecture + hands-on 2 hours)

> **Note** In libdap 3.13 we changed how functions are managed, making
> it possible to build a 'function module' in the same way that the BES
> supports modules for format access and response transmission. These
> function modules are loaded by the BES, just as the other modules are,
> even though the code they are based on is part of libdap. The
> *essential* parts of the function system are the same as before,
> except that functions are no longer compiled into a specific part of
> the code base (e.g., libdap or a format handler like FreeForm). Here
> is a short tutorial that describes how to build a libdap 3.13++ style
> function module: [server side function modules for
> Hyrax.](Hyrax:_Server_Side_Functions "wikilink")

- Background on DAP2 and Constraints
- Writing Server Functions: short version
- Programming with libdap
- A Real Server Function
- Write HelloWorld()
- Advanced Topics

### Setting up for developing server functions

- [Hyrax - Build the Shrew
  Project](Hyrax_-_Build_the_Shrew_Project "wikilink")
- [Eclipse - How to Setup Eclipse in a Shrew
  Checkout](Eclipse_-_How_to_Setup_Eclipse_in_a_Shrew_Checkout "wikilink")
- [BES - Debugging Using
  besstandalone](BES_-_Debugging_Using_besstandalone "wikilink")

## Advanced Topics

Cloud computing environments provide many interesting capabilities for
sites serving data, especially large volumes of data or with highly
variable load levels.

- [How to configure an Amazon AMI so that you can build and run the
  development version of Hyrax](ConfigureAmazonLinuxAMI "wikilink").
  Note that configuration of a VM (using pretty much any hypervisor and
  a Linux VM) to run the distribution version of Hyrax is no different
  from an install on physical hardware. But *building* the development
  source code requires both development tools and the development
  versions of the server's dependencies.
- Running Hyrax in a cloud environment. This [slide
  set](http://www.opendap.org/pub/vm/centos6/OPeNDAP_Cloud_12.2013.ppt)
  describes some experimental work on the Amazon S3 and Glacier storage
  systems. These are Amazon cloud services that provide ways to store
  data that are very different from a spinning disk with a Unix file
  system.