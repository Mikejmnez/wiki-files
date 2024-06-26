This pages covers the steps required to release the BES software for
Hyrax.

We now depend on the CI/CD process to build binary packages and to test
the source builds.


**Tip**: If, while working on the release, you find you need to make
changes to the code and you know the CI build will fail, do so on a
*release branch* that you can merge and discard later. Do not make a
release branch if you don't **need** it, since it complicates making
tags.

## Verify the code base

1.  We release using the *master* branch. The code on *master* must have
    passed the CI builds. **This includes the hyrax-docker builds since
    that CI build runs the full server regression tests!**
2.  Make sure that the source code you're using for the following steps
    is up-to-date. (*git pull*)

## Update the Version Numbers

### Version for Humans

1.  Determine the human version number. This appears to be a somewhat
    subjective process.
2.  Edit each of the *Affected Files* and update the human version
    number.

:; Affected Files

:\* ***configure.ac*** - Look for:



`AC_INIT(bes, ###.###.###, opendap-tech@opendap.org)`

:\* ***debian/changelog*** (see [Debian
ChangeLog](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html#changelog))



**Take Note!** *The `debian/changelog` is the "single source of truth"
for the libdap4 version in the debian packaging. If this does not agree
with the version being packaged the package build will fail.*

:\* ***ChangeLog***

:\* ***NEWS***

:\* ***README.md***

:\* ***INSTALL***

### Update the internal library (API/ABI) version numbers.

The BES is ***not*** a shared library, it is a set of c++ applications
that are typically built as statically linked binaries. Because of this
the usual CURRENT:REVISION:AGE tuples used to express the binary
compatibility state of a c++ shared object library have little meaning
for the BES code. So, what we choose to do is simply bump the REVISION
numbers by one value for each release.

- In the **configure.ac** file locate each of:
  - LIB_DIS_REVISION
  - LIB_PPT_REVISION
  - LIB_XML_CMD_REVISION
- Increase the value of each by one (1).
- Save the file.
- Update the text documentation files and version numbers in the
  configuration files:

Example of the relevant section from configure.ac:

    LIB_DIS_CURRENT=18
    LIB_DIS_AGE=3
    LIB_DIS_REVISION=3
    AC_SUBST(LIB_DIS_CURRENT)
    AC_SUBST(LIB_DIS_AGE)
    AC_SUBST(LIB_DIS_REVISION)
    LIBDISPATCH_VERSION="$LIB_DIS_CURRENT:$LIB_DIS_REVISION:$LIB_DIS_AGE"
    AC_SUBST(LIBDISPATCH_VERSION)

    LIB_PPT_CURRENT=5
    LIB_PPT_AGE=1
    LIB_PPT_REVISION=2
    AC_SUBST(LIB_PPT_CURRENT)
    AC_SUBST(LIB_PPT_AGE)
    AC_SUBST(LIB_PPT_REVISION)
    LIBPPT_VERSION="$LIB_PPT_CURRENT:$LIB_PPT_REVISION:$LIB_PPT_AGE"
    AC_SUBST(LIBPPT_VERSION)

    LIB_XML_CMD_CURRENT=5
    LIB_XML_CMD_AGE=4
    LIB_XML_CMD_REVISION=2
    AC_SUBST(LIB_XML_CMD_CURRENT)
    AC_SUBST(LIB_XML_CMD_AGE)
    AC_SUBST(LIB_XML_CMD_REVISION)
    LIBXMLCOMMAND_VERSION="$LIB_XML_CMD_CURRENT:$LIB_XML_CMD_REVISION:$LIB_XML_CMD_AGE"
    AC_SUBST(LIBXMLCOMMAND_VERSION)

## Update the **ChangeLog** file.

Use the script `gitlog-to-changelog` (which can be found with Google) to
update the **ChangeLog** file by running it using the
`--since="`<date>`"` option with a date one day later in time than the
newest entry in the current ChangeLog.


`gitlog-to-changelog --since="1970-01-01"`


(*Specify a date one day later than the one at the top of the existing
ChangeLog file.*)

Save the result to a temp file and combine the two files:
: `cat tmp ChangeLog > ChangeLog.tmp; mv ChangeLog.tmp ChangeLog` If
you're making the first ChangeLog entries, then you'll need to create
the ChangeLog file first.
**Tip**: *When you're making the commit log entries, use line breaks so
ChangeLog will be readable. That is, use lines \< 80 characters long.*

## Update the **NEWS** file

To update the NEWS file, just read over the new ChangeLog entries and
summarize.

The new entries to the NEWS file will be used later when making the
GitHub release and when writing the server's release page on
www.opendap.org.

We might replace this:

- It's also helpful to have, in the **NEWS** file and the Web site and
  the release notes, a list of the Jira tickets that have been closed
  since the last release. The best way to do this is to goto *Jira's
  Issues* page and look at the *Tickets closed recently* item. From
  there, click on *Advanced* and edit the time range so it matches the
  time range since the past release to now, then *Export* that info as
  an excel spreadsheet (the icon with a hat and a down arrow). YMMV
  regarding how easy this is and Jira's UI changes often.)

With instructions about making an associated release in JIRA using
version tagging.

## Update the Version Numbers for Humans

Affected Files:
configure.ac

debian/changelog (see
[1](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html#changelog)
Debian ChangeLog)

NEWS

README.md

INSTALL

1.  Determine the human version number. This appears to be a somewhat
    subjective process.
2.  Edit each of the *Affected Files* and update the human version
    number.
3.  In the **README.md** file be sure to update the description of how
    to locate the DOI for the release with the new version number.

## Update the libdap version

Determine the libdap version associated with this release by checking
the contents of the file `libdap4-snapshot` The `libdap4-snapshot` file
should contain a single line like this example:

    libdap4-3.20.9-0 2021-12-28T19:23:45+0000

The libdap version for the above example is: `libdap-3.20.9` (The
version is NOT `libdap4-3.20.9`)

### Update the libdap version in the .travis.yml file

Affected Files
.travis.yml

In the .travis.yml file update the value of *LIBDAP_RPM_VERSION* in the
*env: global:* section so that it contains the complete numerical value
of the libdap version you located in the previous step. Using the
previous example the value would be:

        - LIBDAP_RPM_VERSION=3.20.9-0

### Update the libdap version in the RPM spec files

Affected Files
*bes.spec\*.in*

Update the `bes.spec*.in` files by changing the `Requires` and
`BuildRequires` entries for libdap. Based on our example the result
would be:

    Requires:       libdap >= 3.20.9
    BuildRequires:  libdap-devel >= 3.20.9

(*These lines may not be adjacent to each other in the spec files*)

### Update the libdap version in the README.md file

Affected Files
README.md

1.  [Get the DOI markdown from Zenodo](https://zenodo.org) by using the
    search bar and searching for the libdap version string that you
    determined at the beginning of this section.
2.  Update the **README.md** file with libdap version and the associated
    DOI link (using the markdown you got from Zenodo).

Note
You will also need this DOI markdown when making the GitHub release page
for the BES.

See the section on this page titled "*Get the BES DOI from Zenodo*" for
more details about getting the DOI markdown.

## Update the RPM dependencies

Affected Files:
*bes.spec\*.in*

In the RPM *.spec* file, update the dependencies as needed.

- The libdap version dependency was covered in a previous step.
- Be attentive to changes that have been made to the hyrax-dependencies
  since the last release.

## Update the module version numbers for humans

In bes/modules/common, check that the file all-modules.txt is complete
and update as needed. Then run:

- Remove the sentinel files that prevent the version updater from being
  run multiple times in succession without specific intervention:



**`rm -v ../*/version_updated`**

- Now run the version updater:



**`./version_update_modules.sh -v`**

This will update the patch number (x.y.patch) for each of the named
modules.

If a particular module has significant fixes, hand edit the number, in
the Makefile.am.

See below for special info about the HDF4/5 modules (which also applies
to any modules not in the BES GitHub repo).

## ~~For the BES HDF4/5 modules (BES only)~~

1.  ~~*Make sure that you are working on the master branch of each
    module!!*~~
2.  ~~Goto those directories and update the ChangeLog, NEWS, README, and
    INSTALL files (even though INSTALL is not used by many).~~
3.  ~~Update the module version numbers in their respective Makefile.am
    files.~~
4.  ~~Commit and Push these changes.~~

## Update the Build Offset

*Setting the build offset correctly will set the build number for the
new release to "0".*

In the file `travis/travis_bes_build_offset.sh` set the value of
`BES_TRAVIS_BUILD_OFFSET` to the number of the last TravisCI build plus
one. The previous commit and push will have triggered a TravisCI build.
Find the build number for the previous commit in [the TravisCI page for
the BES](https://app.travis-ci.com/github/OPENDAP/bes) and use that
build number plus 1.

## Commit Changes

*Be sure that you have completed all of the changes to the various
ChangeLog, NEWS, INSTALL, configure.ac,
`travis/travis_bes_build_offset.sh`, and other files before proceeding!*

1.  Commit and push the BES code. Wait for the CI/CD builds to complete.
    You must be working on the *master* branch to get the CD package
    builds to work.

## Tag the BES code

The build process automatically tags builds of the master branch. The
Hyrax-version tag is a placeholder for us so we can sort out what code
goes with various Hyrax source releases.

1.  If this is part of a Hyrax Release, then tag this point in the
    master branch with the Hyrax release number
    - `git tag -m "hyrax-`<number>`" -a hyrax-`<numbers>
    - `git push origin hyrax-`<numbers>


    **NB:** *Instead of tagging the HDF4/5 modules, use the saved commit
    hashes that git tracks for submodules. This cuts down on the
    bookkeeping for releases and removes one source of error.*

## Create the BES release on Github

1.  [Goto the BES project page in
    GitHub](https://github.com/OPENDAP/bes)
2.  Choose the **releases** tab.
3.  On the [Releases page](https://github.com/OPENDAP/bes/releases)
    click the 'Tags' tab.
4.  On the [Tags page](https://github.com/OPENDAP/bes/tags), locate the
    tag (created above) associated with this new release.
5.  Click the ellipses (...) located on the far right side of the
    *version-x.y.z* tag 'frame' for this release and and choose *Create
    release*.
    - Enter a *title* for the release
    - Copy the most recent text from the NEWS file into the *describe*
      field
    - Click **Publish release** or **Save draft**.
      - If you have previously edited the release page you can click
        **Update this release**

## Publish and Sign

When the release is made on GitHub the source tar bundle is made
automatically. However, this bundle is **not** the one we wish to
publish because it requires people to have *autoconf* installed. Rather
we want to use the result of "`make dist`" which will have the
`configure` script pre-generated.

All you need do is build the tar file using `make list`, sign it, and
push (or pull) these files onto www.opendap.org/pub/source.

1.  Go to the **bes** project on your local machine and run
    *`make dist`* which will make a bes-x.y.z,tar.gz file at the top
    level of the **bes** project.
2.  Use **gpg** to sign the tar bundle:

    `gpg --detach-sign --local-user security@opendap.org bes-x.y.z.tgz`
3.  Use **sftp** to push the signature file and the tar bundle to the
    /httpdocs/pub/source directory on www.opendap.org

    *(Assuming your current working directory is the top of the **bes**
    project)*

    `sftp opendap@www.opendap.org`

    `cd httpdocs/pub/source`

    `put bes-x.y.z.tgz.sig`

    `put bes-x.y.z.tgz`

    `quit`
4.  Check your work!
    1.  Download the source tar bundle and signature from
        www.opendap.org.
    2.  Verify the signature:

        `gpg --verify bes-x.y.z.tgz.sig bes-x.y.z.tgz`

## Get the BES DOI from Zenodo

Get the Zenodo DOI for the newly created BES release and add it to the
associated GitHub BES release page.

1.  [Goto Zenodo](https://zenodo.org)
2.  Look at the 'upload' page. If there is nothing there (perhaps
    because you are not *jhrg* or whoever set up the connection between
    the BES project and Zenodo) you can use the search bar to search for
    **bes**.

    Since the libdap, BES and OLFS repositories are linked to Zenodo,
    the newly-tagged code is uploaded to Zenodo automatically and a DOI
    is minted for us.
3.  Click on the new version, then click on the DOI tag in the pane on
    the right of the page for the given release.
4.  Copy the DOI as markdown from the window that pops up.
5.  Edit the GitHub release page for the BES release you just created
    and paste the DOI markdown into the top of the description.

**Tip:** *If you are trying to locate the **libdap** releases in Zenodo
you have to search for the string:* `libdap4`

### Images

<figure>
<img src="Screenshot_2018-12-06_11.06.44.png"
title="Screenshot_2018-12-06_11.06.44.png" />
<figcaption>Screenshot_2018-12-06_11.06.44.png</figcaption>
</figure>

## Update the online reference documentation

1.  *make gh-docs*