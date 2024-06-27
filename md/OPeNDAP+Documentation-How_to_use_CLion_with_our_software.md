Setting up CLion to work with our software is really the same as using
CLion with a 'Makefile' project. By default, CLion uses CMake to to
figure out which files are part of a program or library. The problem for
our software, and any software that does not use CMake, is that most of
the benefit(s) of CLion (or any IDE) depend on knowing all the files
that are used to build a program or library.

This page shows how to set up CLion with software that uses make and/or
autotools.

NB: I used CLion version 2022.2.1. I think anything from 2022 works as I
describe here.

<figure>
<img src="clion_version.png" title="clion_version.png" width="640" />
<figcaption>clion_version.png</figcaption>
</figure>

## Other sources of this kind of information

From Jet Brains: [Makefile support](https://www.jetbrains.com/help/clion/makefiles-support.html)

## The whole server or just parts

First, you need to decide if you want to work with all of the C++ code
as one 'project' or use a separate project for each of
'hyrax-dependencies,' 'libdap4,' and 'bes.' In practice, you can choose
one way and then switch to the other without paying too great a penalty.

For the rest of this HowTo, I'll assume you have done the following:

1.  Checkout the **hyrax** git repo from GitHub
    (https://github.com/opendap/hyrax) and ...
2.  Have used the script(s) to clone the three C++ projects
    *hyrax-dependencies*, *libdap4*, *bes* in that *hyrax* directory.
3.  Or, you've manually cloned the *hyrax-dependencies*, *libdap4*,
    *bes* repos somewhere useful to you (which can be that hyrax
    directory). The 'magic' provided by the *hyrax* project is minimal.
    It does, however, contain a script called *spath.sh* that sets up
    some SHELL environment variables you may find useful and that will
    keep you from endless hours of headscrathing while you try to sort
    out why changes made to libdap don't seem to have any effect (the
    answer is that you have a hidden version of libdap installed in
    /usr/local/{bin,lib,include} and it's that library that is being
    used, not the copy you have just changed). **Always** using
    *spath.sh* saves you from that particular nightmare.

## Open a directory that contains code

I think it's best to have the three C++ projects/repos
(*hyrax-dependencies*, *libdap4*, and *bes*) each opened independently,
so let's do that, and open *libdap4* for now. However, if you have
already opened the *hyrax* repo, take a quick peak in the *libdap4*
directory it contains and see if there's an *.idea* directory in there.
If there isn't, you should be able to open the *libdap4* directory and
follow all of the instructions that follow. CLion only reads project
information from the *.idea* directory in the at the top level of the
project it opens. If there is an *.idea* directory in there already,
remove it (there's nothing sacred in there...).

The real power of something like CLion is that it can read and analyze
the source code, providing a way to navigate from file to file finding
the stuff you want without you doing too much searching. We have 1,000s
of files and 100,000s of lines of code. In the (dark) past, we had to
use CMake to do this. Now, in the renaissance, we can configure our
projects using the Makefiles that we actually use to build the code.
This has several advantages, including 1. Not having to maintain two
lists of sources and, 2. builds where the errors and warnings are
clickable links into the actual code (no more matching errors to line
numbers in the editor).

Before going to the next section, if you are using a fresh checkout of
the sources, use *autoreconf* and *configure* to get a valid *Makefile*.
Doing this will make things easier. Since we have a *CMakeList.txt* file
in the repo, we need to have a *Makefile* in there, too. So, run

    autoreconf --fiv
    ./configure --prefix=$prefix --enable-developer

Where you can fiddle with the arguments to *configure* (you don't need
*--enable-developer* and you can use whatever path you want for the
*prefix* value).

What ***is*** important is that the directory contain a Makefile.

## Getting CLion to *index* our code

Assuming your source code (I'm using *libdap4*) does not contain a
*.idea* directory, you can just 'Open' the directory ***or*** go into
the repo directory and open the *Makefile*. Either way seems to work.
The key thing is that you convince CLion that this is a *Makefile*
project:

<figure>
<img src="clion_open.png" title="clion_open.png" width="640" />
<figcaption>clion_open.png</figcaption>
</figure>

In the dialog that pops up, navigate to the directory that contains your
sources:

<figure>
<img src="clion_open_2.png" title="clion_open_2.png" width="640" />
<figcaption>clion_open_2.png</figcaption>
</figure>

Since the directory has both a CMakeLists.txt and a Makefile, CLion asks
which kind of project you want to make.

<figure>
<img src="clion_open_3.png" title="clion_open_3.png" width="640" />
<figcaption>clion_open_3.png</figcaption>
</figure>

Note that you can also choose the Makefile explicitly and IIRC, CLion
will skip the question but instead ask if you want to open just the file
or import a new project based on the *Makefile*.

The two key things to getting this to work are to have the *Makefile* in
the directory and to using *Open*. Using the options to make a New
Project lead me round and round and the checkout from Get From VCS won't
get something with a Makefile and then you'll be stuck with a CMake
project. You might be able to coerce CLion to switching by re-opening
the directory once it has a Makefile, but I have not tried that.

Aside: Once you have done this once, you can click on the name in the
*Choose Project* dialog and it'll open as a *Makefile* project.

It's all pretty cool except, you'll see that there is a bit of a mess at
the bottom of the CLion window that looks like indexing has failed. It
has.

<figure>
<img src="clion_index_fail.jpg" title="clion_index_fail.jpg"
width="640" />
<figcaption>clion_index_fail.jpg</figcaption>
</figure>

However, now we are ready to...

### Configure this project for a *Makefile* build

This is the key to getting the code to index and that is the key to all
the sophisticated navigation capabilities of this IDE.

You can check that this really has been slurped up as a *Makefile*
project by looking under the *Tools* menu.

<figure>
<img src="clion_tools_menu.png" title="clion_tools_menu.png"
width="320" />
<figcaption>clion_tools_menu.png</figcaption>
</figure>

To configure the automatic indexing, go to the *CLion* menu and choose
*Preferences...*, In the dialog, open up the *Build, Execution,
Deployment* section and select *Makefile*:

<figure>
<img src="CLion_libdap4_BED_configuration.png"
title="CLion_libdap4_BED_configuration.png" width="960" />
<figcaption>CLion_libdap4_BED_configuration.png</figcaption>
</figure>

Now, here's where we are going to fix things so indexing works.

Make the dialog entries look like this:

<figure>
<img src="clion_bed_done_right.png" title="clion_bed_done_right.png"
width="960" />
<figcaption>clion_bed_done_right.png</figcaption>
</figure>

To wit:

1.  Build Directory: "**.**" (i.e., the current working directory)
2.  Build options: "**-j20**" (unless you don't have 20 cores, in which
    case use a lower number ;-)
3.  Pre-configuration commands: "**make all -j20**" (This seems
    redundant because we're going to run make as part of
    'pre-configuration' and then we're going to do it all over again in
    the next section. However, we need to run make once as part of
    pre-configuration because *make* generates some of our source code)
4.  Arguments: "**-j20**" (unless...)
5.  Build target: "**check**" (For libdap4 I use *check* because that
    works to build all the source and the tests and we want *everything*
    to build so it's all indexed. For the *BES* use *all check* instead)
6.  Clean target: "**clean**"

Now, re-run the indexing. In the "Build" section of the bottom pane,
click on the 'recycle' symbol inthe upper left corner:

<figure>
<img src="Screen_Shot_2022-08-30_at_13.46.10.png"
title="Screen_Shot_2022-08-30_at_13.46.10.png" width="320" />
<figcaption>Screen_Shot_2022-08-30_at_13.46.10.png</figcaption>
</figure>

If you open code like *Byte.cc*, it should look like this after a few
seconds:

<figure>
<img src="clion_code_is_indexed.png" title="clion_code_is_indexed.png"
width="960" />
<figcaption>clion_code_is_indexed.png</figcaption>
</figure>

NB: I had to run `make all -j20` in the terminal before I could get this
to work correctly. - ndp 11/3/22

## Building code in CLion

The big win here is that when code is built in the IDE, the errors and
warnings are links that will take you directly to the source of the
error.

Goto the Edit Configurations item under the build item in the 'toolbar'
or *Run* menu:

<img src="clion_edit_config.png" title="clion_edit_config.png"
width="320" alt="clion_edit_config.png" />
<img src="clion_run_menu.png" title="clion_run_menu.png" width="400"
alt="clion_run_menu.png" />

And this will bring up a dialog for those items. Initially the dialog
will look like:

<figure>
<img src="clion_initial_edit_conf_dialog.png"
title="clion_initial_edit_conf_dialog.png" width="960" />
<figcaption>clion_initial_edit_conf_dialog.png</figcaption>
</figure>

Edit that so it looks like:

<figure>
<img src="clion_targets.png" title="clion_targets.png" width="960" />
<figcaption>clion_targets.png</figcaption>
</figure>

Where the different targets can be edited. There's no real reason to do
them all, just get *all*, *clean* and *check* working.

Note that the *Target:* drop down list can either be a *custom build
configuration* (something you can set up by clicking the gear to the
right) or it can be one of the Makefile targets. It's easier to use one
of the Makefile targets. I'm not 100% sure what this does... However, if
leave it blank, the build won't start.

**NB:** *On OSX, in order to identify `make` as the **Executable** I had
to use the file system navigator that launches from the ellipsis button
(...) on the far right side of the field and navigate to `/usr/bin/make`
There is a trick: Once the finder dialog box is open use
"`Command-Shift .`" to make it show the hidden files. Why? Because the
finder hides `/usr` be default.* - ndp 11/3/22

Most of the settings are straightforward, but the Environment variables
are not. You need to set the environment variables so that *PATH*
matches the value that the *spath.sh* script will set. Generally, this
is the default value of *PATH* prefixed with
*\$prefix/deps/bin:\$prefix/bin* where you should expand *\$prefix*.
There may be a way to set values of environment variables that are used
by default for a project, but I have not found it. Note that the first
path is used to find things like *ncdump* while the second is used for
things like *besstandalone*. Both paths are needed to run the tests. The
assumption is that the Makefiles are built using *configure* in a shell
with the *PATH* set.

You can cut and paste the value of *PATH* or use the dialog brought up
using the little box icon on the right of the *Environment variables*
edit field. That dialog looks like:

<figure>
<img src="clion_env_dialog.png" title="clion_env_dialog.png"
width="400" />
<figcaption>clion_env_dialog.png</figcaption>
</figure>

**NB:** *I had to brute force the value of PATH. You cannot just set it
to (the expanded \$prefix)
**PATH=\$prefix/deps/bin:\$prefix/bin:\$PATH** That does not work.
Rather, you must set it to the actual value of your shell path with out
the reference. Otherwise the build will not find system basics like
**sed**.* - ndp 11/2/22

One trick, however, is to use *-k* when you run make. This will build
all the code instead of stopping at the first error (drop the *-k* if
*want* to stop at the first error). This way you can fix a bunch of
small issues without the time to run lots of builds.

Lastly, look at the list box at the bottom. It will have a single entry
*Build*. Remove that by clicking on it and then clicking the minus sign
at the top of the list box (right next to the plus sign). This will keep
a default build from running before the build you just configured.

### Run a build

To run a build, choose the item and hit the play button to the right:

<figure>
<img src="clion_run.png" title="clion_run.png" width="600" />
<figcaption>clion_run.png</figcaption>
</figure>

To goto the line where there is an error or warning, click the link:

<figure>
<img src="clion_code_error_2.jpg" title="clion_code_error_2.jpg"
width="960" />
<figcaption>clion_code_error_2.jpg</figcaption>
</figure>

## Running the debugger

Running the debugger is easy once you get the hang of it. There is one
really obscure detail, however. To debug a program we have built, you
will need to make sure you name the correct file. Some background...

Our build for both libdap and the BES makes *shared object* (SO)
libraries. The libdap library, the libraries that make up the be and the
modules that the BES loads at run time are all 'SOs.' What this means in
practice is that even though we link object code with the library files,
code from libraries is not transferred to the executable. Instead, a
reference to the place where the code can be found is added to the
executable. If 100 programs use the same library function, with a SO,
there's only one copy of that function, the one in the SO library. The
object code is shared by all 100 programs.

OK, that's cool, but what does that have to do with debugging? When we
build programs and run them using libraries *before the libraries are
installed,* the normal way the operating system finds the libraries does
not work - that scheme depends on the libraries being installed (SO
library installation populates a simple database). Instead, the run-time
loader uses paths listed on an environment variable to search for the SO
library. The particular package we use to manage the build of SOs
replaces our program with a shell script of the same name that sets that
environment variable and then calls the 'real' executable, which is
cleverly put in a hidden directory called *.libs* (dot 'libs').

So, when *you* want to run the program in a debugger (which must have
access to the process that runs the object code), you need to look for
that *.libs* directory, find you program in there and choose *that*
program and not the trojan shell script that sits in the directory
containing the *.libs* dir. Phew. Almost there...

There's just one problem. IDEs like CLion do everything with GUIs and
the dialog boxes for file selection hide the hidden directories by
default. To get the file dialog to show files and directories with names
that start with a dot (**.**), on a mac hit the Shift, Command and
Period keys at the same time (Shift-Command Period). The dialog will
suddenly show the hidden stuff. On Linux I think it's Shift-Control
Period.

### Configuring a program for debugging

There are at least three places where you can find the place to set up a
*debug configuration.* First, to the left of the play button n the
toolbar, the *Edit Configurations...' item in the menu of 'things to
play' will open this dialog, next the*Edit Configurations...*item in
the*Run/Debug*menu will get you there and the*Debug...*menu, also in
the*Run/Debug*, will show the dialog. Here's what it looks like (but
don't worry about the tile since it might be*Run/Debug ...*or*Debug -
...'')

<figure>
<img src="Clion_debug_config.png" title="Clion_debug_config.png"
width="640" />
<figcaption>Clion_debug_config.png</figcaption>
</figure>

Click on *Custom Build Application* on the left to highlight it, then
click on the plus sign and scroll down to *Custom Build Application*,
like this:

<figure>
<img src="Clion_cbt.png" title="Clion_cbt.png" width="320" />
<figcaption>Clion_cbt.png</figcaption>
</figure>

That gets you to the starting point. To run a program like
*besstandalone* with some arguments, the configured dialog will wind up
looking like:

First, lets set the program we want to run. As was described above, we
need to look in a hidden directory. Lets find the *real* besstandalone
executable. First, hit the little *open* button next to the
*Executable:* drop down item (the square with the three dots (...) in
it). That opens a dialog and use that to navigate to the
*bes/standalone* directory:

<figure>
<img src="clion_plain_dialog.png" title="clion_plain_dialog.png"
width="960" />
<figcaption>clion_plain_dialog.png</figcaption>
</figure>

Then hit Shift-Command Period and you'll see the hidden stuff. Go in the
*.libs* directory and choose *besstandalone.*

<figure>
<img src="clion_scp.png" title="clion_scp.png" width="960" />
<figcaption>clion_scp.png</figcaption>
</figure>

With that done, you'll see the executable has been filled in.

Next, fill in the program arguments. These vary, but usually if you are
running besstandalone in a debugger, you are doing so because a test
failed and the easiest way to find the arguments for a failed test is to
run that test in verbose mode and look at how *it* ran besstandalone.
Here's an example from bes/modules/functions/tests:

    functions/tests % ./testsuite 10 -v
    ## -------------------------------------------------------------- ##
    ## bes 3.20.13 test suite: bes/modules/functions/tests testsuite. ##
    ## -------------------------------------------------------------- ##
    10. testsuite.at:17: testing bescmd/tabular_0.dap.bescmd ...
    COMMAND: besstandalone  -c bes.conf -i bescmd/tabular_0.dap.bescmd
    ./testsuite.at:17: besstandalone -c $abs_builddir/$bes_conf -i $input
    stdout:
    ...

So test number 10 using the command line arguments *-c bes.conf -i
bescmd/tabular_0.dap.bescmd*. Lets put those in the dialog.

Now set the working directory to *bes/modules/functions/tests*. This
will enable besstandalone to find the correct *bes.conf* file and then
correct *bescmd* file. Use the little button on the right or type it all
in. You need the full path, starting with '/'. Make sure you set this,
else the program will exit with an error.

If you're running some other program in the debugger and it needs env
vars, set them up. This program does not, so leave that alone.

The *Redirect input from* option provides a way to read input from a
file as if you had piped that input into the program. We're not using
that for this example.

Lastly, the *Before launch* box provides a way to build/rebuild your
program before you run the debugger. This can be useful, but it also
means running make on all the BES code. Not a big deal if you're not
changing the code, but tedious if you don't need it. We don't for this
example, so I'll click on the *Build* item to highlight it and then
click the minus sign to remove it.

Oh, set the name at the top to something useful. The result looks like:

<figure>
<img src="clion_complete_dialog.png" title="clion_complete_dialog.png"
width="960" />
<figcaption>clion_complete_dialog.png</figcaption>
</figure>

### Start the programm in a debugger

Now, look up at the toolbar. You'll see the configured 'debug task' in
the drop down menu. Click the *debug* button to the right:

<figure>
<img src="clion_start_it.png" title="clion_start_it.png" width="600" />
<figcaption>clion_start_it.png</figcaption>
</figure>

and here's the result (I trimmed the window size to make it easier to
read):

<figure>
<img src="clion_debugging.png" title="clion_debugging.png"
width="1000" />
<figcaption>clion_debugging.png</figcaption>
</figure>

Note that although I'm running CLion in a project that contains only the
BES, I stopped in a method inside libdap.