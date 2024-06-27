- [OPeNDAP's GitHub repositories](https://github.com/OPENDAP): OPeNDAP's
  software is available using GitHub in addition to the downloads from
  our website.
  - Before 2015 we hosted our own SVN repository. It's still online and
    available, but for read-only access, at
    [<https://scm.opendap.org/svn>](https://scm.opendap.org/svn).
- [Continuous Integration builds](https://travis-ci.org/OPENDAP):
  Software that is built whenever new changes are pushed to the master
  branch. These builds are done on the Travis-CI system.
- [test.opendap.org](http://test.opendap.org/): Test servers with data
  files.
- We use the Coverity static system to look for common software defects,
  information on Hyrax is spread across three projects:
  - [The BES and the standard handlers we
    distribute](https://scan.coverity.com/projects/opendap-bes?tab=overview)
  - [The OLFS - the front end to the Hyrax data
    server](https://scan.coverity.com/projects/opendap-olfs?tab=overview)
  - [libdap - The implementation of DAP2 and
    DAP4](https://scan.coverity.com/projects/opendap-libdap4?tab=overview)

## OPeNDAP's FAQ

The [OPeNDAP FAQ](http://www.opendap.org/faq-page) has a pretty good
section on developer's questions.

## C++ Coding Information

- [Guidelines for including
  headers](Include_files_for_libdap "wikilink")
- [Using lambdas with the STL](Using_lambdas_with_the_STL "wikilink")
- [Better Unit tests for C++](Better_Unit_tests_for_C++ "wikilink")
- [Better Singleton classes
  C++](Better_Singleton_classes_C++ "wikilink")
- [What is faster? stringstream string +
  String](What_is_faster?_stringstream_string_+_String "wikilink")
- [More about strings - passing strings to
  functions](More_about_strings_-_passing_strings_to_functions "wikilink")

## OPeNDAP Workshops

- [The APAC/BOM
  Workshops](http://www.opendap.org/about/workshops-and-presentations/2007-10-12):
  This workshop spanned several days and covered a number of topics,
  including information for SAs and Developers. Oct 2007.
- [ESIP Federation Server
  Workshop](http://www.opendap.org/about/workshops-and-presentations/2008-07-15):
  This half-day workshop focused on server installation and
  configuration. Summer 2008
- [Server Functions](A_One-day_Course_on_Hyrax_Development "wikilink"):
  This one-day workshop is all about writing and debugging server-side
  functions. It also contains a wealth of information about Hyrax, the
  BES and debugging tricks for the server. Spring 2012. Updated Fall
  2014 for presentation to Ocean Networks Canada.

## libdap4 and BES Reference documentation

- [BES Reference](https://opendap.github.io/bes/html/)
- [libdap Reference](https://opendap.github.io/libdap4/html/)

## BES Development Information

- [Logging Configuration](Hyrax_-_Logging_Configuration "wikilink")

<!-- -->

- [How to debug the BES](BES_-_How_to_Debug_the_BES "wikilink")
- [BES - Debugging Using
  besstandalone](BES_-_Debugging_Using_besstandalone "wikilink")
- [How to create your own BES
  Module](Hyrax_-_Create_BES_Module "wikilink")
- Hyrax Module Integration: How to configure your module so it's easy to
  add to Hyrax instances
  ([pdf](:File:HyraxModuleIntegration-1.2.pdf "wikilink"))
- [Starting and stopping the
  BES](Hyrax_-_Starting_and_stopping_the_BES "wikilink")
- [Running the BES command line
  client](Hyrax_-_Running_bescmdln "wikilink")
- [BES Client commands](Hyrax_-_BES_Client_commands "wikilink"). The
  page [BES XML Commands](BES_XML_Commands "wikilink") repeats this info
  for a bit more information on the return values. Most of the commands
  don't return anything unless they return an error and are expected to
  be used in a group where a *get* command closes out the request and
  obviously does return a response of some kind (maybe an error).
- [BES Administrative
  Commands](Hyrax:_BES_Administrative_Commands "wikilink")
- [Extending your BES Module](Hyrax_-_Extending_BES_Module "wikilink")
- [Example BES Modules](Hyrax_-_Example_BES_Modules "wikilink") - the
  Hello World example and the CSV data handler
- [BES communication protocol using PPT (point to point
  transport)](Hyrax_-_BES_PPT "wikilink")

<!-- -->

- [Software Developers
  Workshop](Australian_BOM_Software_Developer's_Agenda_and_Presentations "wikilink")

## OPeNDAP Development process information

These pages contain information about how we'd like people working with
us to use our various on-line tools.

- [Planning a Program
  Increment](Planning_a_Program_Increment "wikilink") This is a
  checklist for the planning phase that precedes a Program Increment
  (PI) when using SAFe with the NASA ESDIS development group.
- [Hyrax GitHub Source Build](Hyrax_GitHub_Source_Build "wikilink") This
  explains how to clone our software from GitHub and build our code
  using a shell like bash. It also explains how to build the BES and all
  of the Hyrax 'standard' handlers in one operation, as well as how to
  build just the parts you need without cloning the whole set of repos.
  Some experience with 'git submodule' will make this easier, although
  the page explains everything.
- [Bug Prioritization](Bug_Prioritization "wikilink"). How we prioritize
  bugs in our software.

### [Making A Release](How_to_Make_a_Release "wikilink")

- [How to Make a Release](How_to_Make_a_Release "wikilink") A general
  template for making a release. This references some of the pages
  below.

## Software process issues:

- [How to download test logs from a Travis
  build](How_to_download_test_logs_from_a_Travis_build "wikilink") All
  of our builds on Travis that run tests save those logs to an S3
  bucket.
- [How to configure a CentOS machine for production of RPM
  binaries](ConfigureCentos "wikilink") - Updated 12/2014 to include
  information regarding git.
- [How to use CLion with our
  software](How_to_use_CLion_with_our_software "wikilink")
- [How to add timing instrumentation to your BES
  code.](BES_Timing "wikilink")
- [How to write unit tests using CppUnit](UnitTests "wikilink") NB: See
  other information under the heading of C++ development
- [How to use valgrind with unit tests](valgrind "wikilink")
- [Debugging the distcheck
  target](Debugging_the_distcheck_target "wikilink") Yes, this gets its
  own page...
- [How to copyright software written for OPeNDAP](CopyRights "wikilink")
- [Managing public and private keys using
  gpg](Managing_public_and_private_keys_using_gpg "wikilink")
- [How to Setup Secure Email and Sign Software
  Distributions](SecureEmail "wikilink")
- [How to Handle Email-list Support Questions](UserSupport "wikilink")
- [Security Policy and Related
  Procedures](NetworkServerSecurity "wikilink")
- [Software version numbers](http://semver.org/)
- [Development Guidelines](GuideLines "wikilink")
- [Apple M1 Special Needs](Apple_M1_Special_Needs "wikilink")

#### Older info of limited value:

- [C++-11 gcc/++-4.4
  support](http://gcc.gnu.org/gcc-4.4/cxx0x_status.html) We now require
  compilers that support C++-14, so this is outdated (4/19/23).
- [How to use Eclipse with Hyrax Source
  Code](How_to_use_Eclipse_with_Hyrax_Source_Code "wikilink") I like
  Eclipse, but we now use CLion because it's better (4/19/23) . Assuming
  you have cloned our Hyrax code from GitHub, this explains how to setup
  eclipse so you can work fairly easily and switch back and forth
  between the shell, emacs and eclipse.

#### AWS Tips

- [Growing a CentOS Root Partition on an AWS EC2
  Instance](Growing_a_CentOS_Root_Partition_on_an_AWS_EC2_Instance "wikilink")
- [How Shutoff the CentOS
  firewall](How_Shutoff_the_CentOS_firewall "wikilink")

## General development information

These pages contain general information relevant to anyone working with
our software:

- **[Git Hacks and Tricks](Git_Hacks_and_Tricks "wikilink")**:
  Information about using git and/or GitHub that seems useful and maybe
  not all that obvious.
- [Git Secrets](Git_Secrets "wikilink"): securing repositories from AWS
  secret key leaks.
- [Valgrind Suppression File
  Howto](https://wiki.wxwidgets.org/Valgrind_Suppression_File_Howto) How
  to build a suppressions file for valgrind.
- [Using a debugger for C++ with Eclipse on
  OS/X](Using_a_debugger_for_C++_with_Eclipse_on_OS/X "wikilink") Short
  version: use lldbmi2 \*\*Add info\*\*
- [Using ASAN](Using_ASAN "wikilink") Short version, look [at the
  Google/GitHub
  pages](https://github.com/google/sanitizers/wiki/AddressSanitizerAndDebugger)
  for useful environment variables \*\*add text\*\* On Centos, use yum
  install llvm to get the 'symbolizer' and try *ASAN_OPTIONS=symbolize=1
  ASAN_SYMBOLIZER_PATH=\$(shell which llvm-symbolizer)*
- [How to use *Instruments* on OS/X to
  profile](How_to_use_''Instruments''_on_OS/X_to_profile "wikilink")
  Updated 7/2018
- [Valgrind - How to generate suppression files for
  valgrind](https://wiki.wxwidgets.org/Valgrind_Suppression_File_Howto)
  This will quiet valgrind, keeping it from telling you OS/X or Linux
  (or the BES) is leaking memory.
- [Migrating source code from SVN to
  git](Migrating_source_code_from_SVN_to_git "wikilink"): How to move a
  large project from SVN to git and keep the history, commits, branches
  and tags.
- [Eclipse - Detailed information about running Eclipse on OSX from the
  Mozzilla
  project](https://developer.mozilla.org/en-US/docs/Eclipse_CDT).
  Updated in 2017, this is really good but be aware that it's specific
  to Mozilla so some of the tips don't apply. Hyrax (i.e., libdap4 and
  BES) also use their own build system (autotools + make) so most of the
  configuration information here is very apropos. See also [How to use
  Eclipse with Hyrax Source
  Code](How_to_use_Eclipse_with_Hyrax_Source_Code "wikilink") below.
- [RPM
  Guide](https://jfearn.fedorapeople.org/en-US/RPM/4/html/RPM_Guide/index.html)
  The best one I'm found so far...
- [Autotools Myth busters](https://autotools.io/index.html) The best
  info on autotools I've found yet (covers *autoconf*, *automake*,
  *libtool* and *pkg-config*).
- The [autoconf](https://www.gnu.org/software/autoconf/autoconf.html)
  manual
- The [automake](https://www.gnu.org/software/automake/) manual
- The [libtool](https://www.gnu.org/software/libtool/) manual
- A good [gdb to lldb cheat sheet](https://lldb.llvm.org/lldb-gdb.html)
  for those of us who know *gdb* but not *lldb*.

# Old information

**Note**: Old build information

#### The Release Process

1.  Make sure the `hyrax-dependencies` project is up to date and tar
    balls on www.o.o. If there have been changes/updates:
    1.  Update version number for the `hyrax-dependencies` in the
        `Makefile`
    2.  Save, commit, (merge?), and push the changes to the `master`
        branch.
    3.  Once the `hyrax-dependencies` CI build is finished, trigger CI
        builds for both `libdap4` and `bes` by pushing change(s) to the
        master branch of each.
2.  [Making a source release of
    libdap](Source_Release_for_libdap "wikilink")
3.  [Making a source release of the BES](ReleaseGuide "wikilink").
4.  [Make the OLFS release WAR file](OLFSReleaseGuide "wikilink").
    Follow these steps to create the three .jar files needed for the
    OLFS release. Includes information on how to build the OLFS and how
    to run the tests.
5.  [Make the official Hyrax Docker image for the
    release](HyraxDockerReleaseGuide "wikilink") When the RPMs and the
    WAR file(s) are built and pushed to their respective download
    locations, make the Docker image of the release.

#### Supplemental release guides

<font color="red">Old - use the packages built using the Continuous
Delivery process</font>

1.  [Make the RPM Distributions](RPM "wikilink"). Follow these steps to
    create an RPM distribution of the software. **Note:** *Now we use
    packages built using CI/CD, so this checklist is no longer needed.*

**Note**: *The following is all about using Subversion and is out of
date as of November 2014 when we switched to git. There are still good
ideas here...*

- [How to merge code](MergingBranches "wikilink")
- [Using the SVN trunk, branches and tags to manage
  releases](TrunkDevelBranchRel "wikilink").
- [Making a Branch of Shrew for a Server
  Release](ShrewBranchGuide "wikilink"). Releases should be made from
  the trunk and moved to a branch once they are 'ready' so that
  development can continue on the trunk and so that we can easily go
  back to the software that mad up a release, fix bugs, and (re)release
  those fixes. In general, it's better to fix things like build issues,
  etc., discovered in the released software *on the trunk* and merge
  those down to the release branch to maintain consistency, re-release,
  etc. This also means that virtually all new feature development should
  take place on special *feature* branches, not the trunk.
- [Hyrax Package for OS-X](Hyrax_Package_for_OS-X "wikilink"). This
  describes how to make a new OS/X 'metapackage' for Hyrax.
- [Making Windows XP distributions](XP "wikilink"). Follow these
  directions to make Windows XP binaries.
- [Making a Matlab Ocean Toolbox Release](ReleaseToolbox "wikilink").
  Follow these steps when a new Matlab GUI version is ready to be
  released.
- [Eclipse - How to Setup Eclipse in a Shrew
  Checkout](Eclipse_-_How_to_Setup_Eclipse_in_a_Shrew_Checkout "wikilink")
  This includes some build instructions
- [How to configure a Linux machine to build Hyrax from
  SVN](LinuxBuildHostConfig "wikilink")
- [How to configure a SUSE machine for production of RPM
  binaries](ConfigureSUSE "wikilink")
- [How to configure an Amazon Linux AMI for EC2 Instance To Build
  Hyrax](ConfigureAmazonLinuxAMI "wikilink")
- [Notes from setting up Hyrax on our new web
  host](TestOpendapOrg "wikilink")
- [Subversion 1.7
  documentation](http://svnbook.red-bean.com/en/1.7/index.html) -- The
  official Subversion documentation;
  [PDF](http://svnbook.red-bean.com/en/1.1/svn-book.pdf) and
  [HTML](http://svnbook.red-bean.com/en/1.1/index.html).
- [OPeNDAP's Use of Trac](OPeNDAP's_Use_of_Trac "wikilink") -- How to
  use Trac's various features in the software development process.