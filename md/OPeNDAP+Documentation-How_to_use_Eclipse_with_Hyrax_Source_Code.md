This presents one way to set up Eclipse to build, edit, debug, etc., the
Hyrax source code. It's based on our previous how to documents, but is
updated for (and maybe specific to) OSX 10.10, Eclipse 4.4 (Luna) and
CDT 8.5. There are many things about Eclipse that can be pretty obscure,
not the least of which is that Eclipse & CDT are not really 'just
Eclipse' but may have specific configuration nuances. This is almost
certainly not the *only* way to set this tool up to build the Hyrax
code, just one way.

The set up assumes that you have already cloned the *hyrax* repo from
GitHub (https://github.com/opendap/hyrax.git) and have run *source
spath.sh* and *./hyrax_clone.sh* so that the \$PATH and \$prefix
environment variables are set and the *libdap*, *bes* and *olfs* repos
have all been cloned. For most development, it's a good idea to clone
*hyrax-dependencies* too.

## The *git* perspective

What I've done to make projects that use the existing C++ code repos
cloned from GitHub was to first use the *git* perspective to add the
repo to Eclipse.

Switch to the *git* perspective using either the icon/toll-bar item on
the right side of the main window or by going to the Window -\> Open
Perspective menu item.

<figure>
<img src="Perspective_Menu.png" title="Perspective_Menu.png" />
<figcaption>Perspective_Menu.png</figcaption>
</figure>

Once the Eclipse window is showing the *git* perspective, you'll see
something like the following:

<figure>
<img src="Git_Repo_Perspective.png"
title="File:Git Repo Perspective.png" />
<figcaption><a href="File:Git">File:Git</a> Repo
Perspective.png</figcaption>
</figure>

Use the *Add existing git repository* icon to add an existing *git*
repo.

<figure>
<img src="Add_Existing_Repo.png" title="File:Add Existing Repo.png" />
<figcaption><a href="File:Add">File:Add</a> Existing
Repo.png</figcaption>
</figure>

The result looks like:

<figure>
<img src="Once_Added.png" title="File:Once Added.png" />
<figcaption><a href="File:Once">File:Once</a> Added.png</figcaption>
</figure>

## Make the projects

Follow theses steps to make a new project using the repo you just added
to the *git* perspective.

- Import Projects
- New Project
- Use the New Project Wizard
- Existing Code Makefile
- Setup the Project
- Switch to C++ Perspective
- The New project

<figure>
<img src="0_Import_Projects_2.png"
title="File:0 Import Projects 2.png" />
<figcaption><a href="File:0">File:0</a> Import Projects
2.png</figcaption>
</figure>

<figure>
<img src="2_Use_New_Project_Wizard.png"
title="File:2 Use New Project Wizard.png" />
<figcaption><a href="File:2">File:2</a> Use New Project
Wizard.png</figcaption>
</figure>

<figure>
<img src="3_Existing_Code_Makefile.png"
title="File:3 Existing Code Makefile.png" />
<figcaption><a href="File:3">File:3</a> Existing Code
Makefile.png</figcaption>
</figure>

<figure>
<img src="4_Setup_The_Project.png"
title="File:4 Setup The Project.png" />
<figcaption><a href="File:4">File:4</a> Setup The
Project.png</figcaption>
</figure>

<figure>
<img src="5_Switch_To_Cpp_Perspective.png"
title="File:5 Switch To Cpp Perspective.png" />
<figcaption><a href="File:5">File:5</a> Switch To Cpp
Perspective.png</figcaption>
</figure>

<figure>
<img src="6_The_New_Project.png" title="File:6 The New Project.png" />
<figcaption><a href="File:6">File:6</a> The New Project.png</figcaption>
</figure>

## General Eclipse configurations

These are not specific to our project, but they are very useful and
somewhat hard to sort out.

- How to set up PATH so the CDT build (which uses an external program)
  reads from the PATH you want instead of the default PATH
  - <figure>
    <img src="0_Set_PATH_in_Eclipse.png"
    title="File:0 Set PATH in Eclipse.png" />
    <figcaption><a href="File:0">File:0</a> Set PATH in
    Eclipse.png</figcaption>
    </figure>

  - <figure>
    <img src="1_Add_the_prefix_var_to_project.png"
    title="File:1 Add the prefix var to project.png" />
    <figcaption><a href="File:1">File:1</a> Add the prefix var to
    project.png</figcaption>
    </figure>

  - <figure>
    <img src="2_Set_PATH_in_the_project.png"
    title="File:2 Set PATH in the project.png" />
    <figcaption><a href="File:2">File:2</a> Set PATH in the
    project.png</figcaption>
    </figure>

  - Do you need to set PATH in both Eclipse and the Project? I don't
    know, but that's what I wound up doing to get it to work.
- How to get the editor in CDT to save before building code
  - <figure>
    <img src="0_Set_autosave_on_build.png"
    title="File:0 Set autosave on build.png" />
    <figcaption><a href="File:0">File:0</a> Set autosave on
    build.png</figcaption>
    </figure>
- How to configure builds using an existing Makefile
  - <figure>
    <img src="0_make_target.png" title="File:0 make target.png" />
    <figcaption><a href="File:0">File:0</a> make target.png</figcaption>
    </figure>

  - <figure>
    <img src="1_configure_new_make_target.png"
    title="File:1 configure new make target.png" />
    <figcaption><a href="File:1">File:1</a> configure new make
    target.png</figcaption>
    </figure>
- Editor tweaks:
  - Emacs mode (it's not a real emacs, but it's bearable)
  - How to configure Eclipse's editor to find includes
    - <figure>
      <img src="0_Goto_includes_fir_project.png"
      title="File:0 Goto includes fir project.png" />
      <figcaption><a href="File:0">File:0</a> Goto includes fir
      project.png</figcaption>
      </figure>

    - <figure>
      <img src="1_Add_directory_workspace_path.png"
      title="File:1 Add directory workspace path.png" />
      <figcaption><a href="File:1">File:1</a> Add directory workspace
      path.png</figcaption>
      </figure>

    - <figure>
      <img src="2_Result_of_adding_workspace_path.png"
      title="File:2 Result of adding workspace path.png" />
      <figcaption><a href="File:2">File:2</a> Result of adding workspace
      path.png</figcaption>
      </figure>

    - [Filesystem path.png](File:4)\]

    - <figure>
      <img src="3_Add_filesystem_path.png"
      title="File:3 Add filesystem path.png" />
      <figcaption><a href="File:3">File:3</a> Add filesystem
      path.png</figcaption>
      </figure>

    - <figure>
      <img src="5_Probably_reindex.png" title="File:5 Probably reindex.png" />
      <figcaption><a href="File:5">File:5</a> Probably
      reindex.png</figcaption>
      </figure>
  - Setting up folding to hide \#if 0 ... \#endif blocks (help with
    refactoring, among other things).
    - <figure>
      <img src="0_Editor_folding.png" title="File:0 Editor folding.png" />
      <figcaption><a href="File:0">File:0</a> Editor folding.png</figcaption>
      </figure>

    - <figure>
      <img src="1_editor_folding.png" title="File:1 editor folding.png" />
      <figcaption><a href="File:1">File:1</a> editor folding.png</figcaption>
      </figure>

    - <figure>
      <img src="2_editor_folding.png" title="File:2 editor folding.png" />
      <figcaption><a href="File:2">File:2</a> editor folding.png</figcaption>
      </figure>

## Debugging with eclipse on OSX

Short version: Use lldbmi2.

I changed debugger to LLDB using the library lldbmi2:

1.  git clone <https://github.com/freedib/lldbmi2.git> lldbmi2
2.  cd lldbmi2
3.  mkdir build
4.  cd build
5.  cmake ../
6.  make
7.  sudo make install

Once lldbmi2 is installed, you can debug your application by creating a
new C/C++ Application in Debug Configurations... and change the GDB
debugger (in Debugger tab) from gdb to lldbmi2. Options to lldbmi2 may
be set there. Something like /usr/local/bin/lldbmi2 --log.

<figure>
<img src="Lldbmi2_in_eclipse_oxygen.3.png"
title="File:Lldbmi2 in eclipse oxygen.3.png" />
<figcaption><a href="File:Lldbmi2">File:Lldbmi2</a> in eclipse
oxygen.3.png</figcaption>
</figure>

## Old information on using GDB - this will not work with OSX 10.13

- GDB on OS/X 10.9 and 10.10 is broken WRT shared object libraries
  (i.e., BES modules).
  - Build GDB 7.8.x (other versions might work, but the default 6.x that
    comes with OS/X won't). To do this I had to *sudo cp
    /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/usr/include/machine/setjmp.h
    /usr/include/machine/* because while *setjmp.h* is present, there's
    nothing in \<machine/setjmp.h\> on 10.10 (and maybe 10.9, too). This
    may also explain the *homebrew* install fail on gdb, but I don't
    know. I also used *export CC=gcc* before running *./configure* for
    gdb, but I doubt that made any difference in hindsight. Regardless,
    get a modern GDB built and installed.

  - Follow this advice on setup with eclipse:
    <http://ntraft.com/installing-gdb-on-os-x-mavericks/>. NB: since
    PATH is already configured to have /usr/local/bin before the other
    stuff, the part about setting C/C++ -\> Debug -\> GDB to
    /usr/local/bin/gdb may be moot, but I did it all the same. The
    example shows the preferences window from a version of eclipse
    earlier than 4.4, which I am using.

  - GDB and libtool: But GDB won't debug programs built with shared libs
    (dylibs on OS/X), responding with this message: *"hell": not in
    executable format: File format not recognized* for the program
    *hell* that's linked using libtool. Nice. The work-around is to use
    *libtool --mode=execute gdb hell* and maybe to set GDB to be
    *libtool --mode=execute gdb* although I have not tried the later.

  - Here is advice on signing GDB (if you build it from source) so that
    OS/X 10.9++ will run it and let it attach to a running process:
    <https://sourceware.org/gdb/wiki/BuildingOnDarwin>

  - Now set up Eclipse:
    - <figure>
      <img src="0_GDB_Issues_with_shared_libs.png"
      title="File:0 GDB Issues with shared libs.png" />
      <figcaption><a href="File:0">File:0</a> GDB Issues with shared
      libs.png</figcaption>
      </figure>

    - <figure>
      <img src="1_GDB_Issues_cont.png" title="File:1 GDB Issues cont.png" />
      <figcaption><a href="File:1">File:1</a> GDB Issues cont.png</figcaption>
      </figure>

    - <figure>
      <img src="2_Setup_the_debug_configuration.png"
      title="File:2 Setup the debug configuration.png" />
      <figcaption><a href="File:2">File:2</a> Setup the debug
      configuration.png</figcaption>
      </figure>

    - <figure>
      <img src="3_Debug_config_dialog.png"
      title="File:3 Debug config dialog.png" />
      <figcaption><a href="File:3">File:3</a> Debug config
      dialog.png</figcaption>
      </figure>

    - <figure>
      <img src="4_Use_legacy_launcing_with_Eclipse_4.4.png"
      title="File:4 Use legacy launcing with Eclipse 4.4.png" />
      <figcaption><a href="File:4">File:4</a> Use legacy launcing with Eclipse
      4.4.png</figcaption>
      </figure>

    - <figure>
      <img src="5_Debugger_tab_should_look_like.png"
      title="File:5 Debugger tab should look like.png" />
      <figcaption><a href="File:5">File:5</a> Debugger tab should look
      like.png</figcaption>
      </figure>

    - <figure>
      <img src="6_warning_gone.png" title="File:6 warning gone.png" />
      <figcaption><a href="File:6">File:6</a> warning gone.png</figcaption>
      </figure>

  - <figure>
    <img src="0_GDB_Suppres_auto_build_on_launch.png"
    title="File:0 GDB Suppres auto build on launch.png" />
    <figcaption><a href="File:0">File:0</a> GDB Suppres auto build on
    launch.png</figcaption>
    </figure>

  -