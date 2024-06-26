## Making a Branch of Shrew for a Server Release

(Last updated for Hyrax 1.7 as the module release target platform)

This document describes how to make a branch in svn for a new release of
the Hyrax server. Since the *shrew* meta-project uses the
*<svn:externals>* property to reference its child projects, A major part
of this document describes how to change those external links to work
correctly. For this document, \$SVN refers to the svn root, presumably
"<https://scm.opendap.org/svn>".

About version numbers: we use *<major>.<minor>.<sub-minor>* version
numbers for code.

## Overview

Because Hyrax is made up of a number of components, each of which is its
own project in subversion (svn), we have made a svn *meta-project* that
can be used to check out all of those as a single unit. The meta-project
includes a Makefile.am and other tools to build and run Hyrax, at least
in the common configurations. The meta-project is called *shrew* and can
be checked out from *\$svn/trunk/shrew*. Since it is on the *trunk* of
svn, this is where active development takes place. When we are ready to
make a release of Hyrax, we make a branch of this meta-project on
*\$svn/branch/shrew/<version>*. In order to do that, the
*<svn:externals>* links within shrew must be set to point to versions of
the components that are parts of the svn/branch section of the svn
repository. Once that's done, it is a simple matter to check out the
branch copy of shrew and build/edit the code for the release. Changes to
the components will be checked in to their branch versions, keeping the
trunk versions unchanged until a merge is performed.

## Assumptions

The code in a checkout of \$svn/trunk/shrew all builds and passes its
tests or, at least, we're all satisfied that the tests that are failing
are not so important. It's a good idea to test targets like *pkg* and
*rpm* to make sure those are working. The nightly builds should be doing
this...

## Steps

This section covers the steps to make the new branch.

1.  Make sure that all of the changes to be in the release are checked
    in to the trunk. When using the *svn* client in a shell, make sure
    to run svn in each of the source directories in addition to the
    top-level shrew directory:
    - *svn ci -m "hyrax <version> release" .*
    - *svn ci -m "hyrax <version> release" src/libdap*
    - *svn ci -m "hyrax <version> release" src/bes*
    - ...
    - *svn ci -m "hyrax <version> release" src/modules*
    - *svn ci -m "hyrax <version> release" src/modules/ncml_module* (you
      have to run this for each module, not just the *modules*
      directory)
2.  Use svn's copy command to copy the \$svn/trunk/shrew meta-project to
    \$svn/branch/shrew
    - *svn cp \$svn/trunk/shrew
      \$svn/branch/shrew/hyrax_\<<version>\>_release* (use two digit
      numbers for branches in almost all cases since it's likely that
      there will be several sub-minor versions for any given branch)
3.  For each of the components to be included in the release, copy their
    current trunk code to a branch.
    - Make the branch for that component: *svn cp \$svn/trunk/<comp>
      \$svn/branch/\<<component>\>/hyrax_\<<version>\>_release*. *This
      is a change from our previous process*. In the past we made these
      branches have specific version triples but that turned out to be
      confusing because while fixing problems we'd bump up the versions
      and so the version on a branch might be 1.5.2 while the version
      number used to name the branch was 1.4.5. Very confusing. In
      addition, each of the branch names was specific to the module and
      its version number at the time the branch was made, not the
      version of the server with which it was actually associated.
      Naming the branch after the version of the server the component is
      associated with makes sorting out what goes with server version
      *x.y* much easier. *Use the two digit version number for Hyrax for
      this name*.
    - Record the branch path for the component
4.  Checkout the shrew branch but suppress getting all of its child
    projects: *svn co --ignore-externals \$svn/branch/shrew/<version>*
5.  Edit the file *externals.txt* so that this copy of shrew will check
    out the branch versions of the components instead of their trunk
    versions.
    - E.G.: *^/trunk/libdap src/libdap* --\>
      *^/brnach/libdap/hyrax_\<<version>\>_release/libdap* (each line
      in the file means check out the first arg and put it in the second
      arg and where '^' is svn externals shorthand for the root of the
      repository, which we have been calling *\$svn*).
6.  Once you have edited the externals.txt file, **set the svn property
    *<svn:externals>*** to it:
    - ***svn propset <svn:externals> --file externals.txt .*** (note the
      trailing dot; see
      <http://svnbook.red-bean.com/en/1.7/svn.advanced.externals.html>
      for more info about this property). Since we're reading the value
      of <svn:externals> from a file, using propset is OK; if we were
      setting it from the command line, propedit would be a better
      choice, assuming we have a svn version supporting it.
7.  Check in the changes, making sure that both the file and the
    properties are checked in. (you should see two things that have been
    changed, one the file and the second will be '.'; i.e., the current
    working directory).
8.  Either update (*svn up*) or erase the current directory and checkout
    a new copy of the shrew/branch, this time omitting the
    --ignore-externals flag. Either way, the result is a complete
    checkout of the entire meta-project.
9.  Test the build
    - By hand:
      - Set the environment variable: *source spath.sh* (n.b.: you can
        alias this script like *alias spath="source spath.sh"* and then
        just type *spath* at the shell prompt; also there are several
        other variants of shell scripts that set the required env vars).
      - *autoreconf -fiv* (-fiv == --force --install --verbose)
      - *./configure --prefix=\$prefix*
      - *make universe* or *make world* (The universe target builds the
        dependencies; on OS/X these are expected to be used and on linux
        they are not. Some obvious editing of the Makefile.am can be
        used to change that.)
    - Using a build script:
      - Edit the *nbuild_shrew.sh.in* script and run it; or
      - *source build_hyrax* or *source build_hyrax_debug*
10. Tagging the components
    - When code is released, tag the component using *svn cp
      \$svn/branch/\<<component>\>/hyrax_\<<version>\>_release
      \$svn/tags/\<<component>\>/x.y.z* where *x.y.z* is the current
      full (three digit) version number (found in the configure.ac file)
      of the component.
    - svn will ask you to commit that; use *Hyrax \<<version>\>* for the
      log comment
    - First look at the code (e.g., libdap) and check its current
      version by looking at the configure.ac file.
    - Decide if, for the release, the minor version is going to be
      bumped up. If not, decide if the sub-minor version number should
      be bumped.

## Merging Code

### Overview

Major development is always done on a branch **never** on the trunk.

Two basic classes of branches:

1.  Release branch
2.  Development branch

#### Release Branches

- A release branch is made when the software is ready to release.
- The release branch always begins as a snapshot of the current trunk.
- The release branch is **never** merged to the trunk.
- Changes are never made directly to the release branch.
- Bug fixes and repairs to the released code are made on the trunk and
  the trunk is merged to the release branch.
- If the trunk has changed radically because a large body of
  changes/code has been merged back from a *Development Branch* then the
  current release must be abandoned and a new release created as a snap
  shot of the current trunk.

#### Development Branches

- A development branch is created for any collection of new features,
  changes to the API, or any other large changes to the code base.
- A development branch is always created as a snapshot of the (current)
  trunk.
- Changes to the trunk get merged, on a regular basis to all of the
  development branches.
- Code, documentation, and production rule development continue on the
  development branch until the new code is ready to release.
- When ready to release the new code changes the development branch is
  merged *ONE TIME* to the trunk.
- Once the development branch is merged back to trunk it is abandoned.
  It is never used again.
- Subsequent small changes (bug fixes) happen on the trunk. Big changes
  get a NEW branch.

### Example: Merging the trunk to a branch

<figure>
<img src="Screen_Shot_2013-09-12_at_2.00.20_PM.png"
title="Screen_Shot_2013-09-12_at_2.00.20_PM.png" width="200" />
<figcaption>Screen_Shot_2013-09-12_at_2.00.20_PM.png</figcaption>
</figure>

Both release and development branches need to receive changes made on
the trunk.

Assumption: *\$svn* is the root of the SVN repository (e.g.,
*<https://scm.opendap.org/svn>*) and *package* is the name of a package
which has both a trunk and branch version.

This is the same as Eclipse's *Merge a range of revisions* although in
the example I don't show the option to merge a range because I think
that's harder to do and should only apply if a specific change needs to
be merged to a release branch without merging other changes from the
trunk that contain some new complex feature. For simpler projects (ours)
it's easiest to just make a new release branch.

You need to run this command from within a working copy and the changes
are made to the working copy. You need to commit them to add them to the
repository. To merge a range of revisions, use -N num:num.

In a branch (*release* or *development*), merge from the trunk like
this: <font size="2">

``` bash
svn merge $svn/trunk/package
```

</font>

### Example: Merging a development branch to the trunk

<figure>
<img src="Screen_Shot_2013-09-12_at_2.00.11_PM.png"
title="Screen_Shot_2013-09-12_at_2.00.11_PM.png" width="200" />
<figcaption>Screen_Shot_2013-09-12_at_2.00.11_PM.png</figcaption>
</figure>

Only a development branch is ever merged to the trunk, and this must
only be done a single time after which subsequent changes are made to
the merged code on the trunk. If major feature changes are required then
a new development branch should be created.

NB: Same assumptions as above. The working copy must be all checked in
and made up of only one revision. It also cannot have any switched
children. This is the same as Eclipse's *Reintegrate a branch* merge
option.

You must run this command from within the working copy at its 'top' and
the result of the merge will change your working copy, but not the
repository. You will need to commit your changes to make them part of
the repository.

<font size="2">

``` bash
svn merge --reintegrate $svn/branch/package/branch_name
```

</font>