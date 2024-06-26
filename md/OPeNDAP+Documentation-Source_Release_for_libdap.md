This page covers the step needed to release the libdap software for
Hyrax. There is are separate pages for the BES and OLFS code and an
overview page that describes how the website is updated and lists are
notified.

We now depend on the CI/CD process to build binary packages and to test
the source builds. When the source code is tagged and marked as a
release in GitHub, our linked Zenodo account archives that software and
mints a DOI for it.

## The Release Process


**Tip**: If, while working on the release, you find you need to make
changes to the code and you know the CI build will fail, do so on a
*release branch* that you can merge and discard later. Do not make a
release branch unless you need to since it complicates making tags.

### Verify the code base

1.  We release using the *master* branch. The code on *master* must pass
    the CI build.
2.  Make sure that the source code you're using for the following steps
    is up-to-date. (*git pull*)

### Update Release Files

Update the text documentation files and version numbers in the
configuration files:

**Note**
It's helpful to have, in the **NEWS** file, the Web site and the release
notes, a list of the Jira tickets that have been closed since the last
release. The best way to do this is to goto *Jira's Issues* page and
look at the *Tickets closed recently* item. From there, click on
*Advanced* and edit the time range so it matches the time range since
the past release to now, then *Export* that info as an excel spreadsheet
(the icon with a hat and a down arrow). YMMV regarding how easy this is
and Jira's UI changes often.

#### Update the **ChangeLog** file.

Use the script `gitlog-to-changelog` (which can be found with Google) to
update the **ChangeLog** file by running it using the
`--since="`<date>`"` option with a date one day later in time than the
newest entry in the current ChangeLog.


**gitlog-to-changelog --since="1970-01-01"** (*Specify a date one day
later than the one at the top of ChangeLog*)

Save the result to a temp file and combine the two files:
: **cat tmp ChangeLog \> ChangeLog.tmp; mv ChangeLog.tmp ChangeLog** If
you're making the first ChangeLog entries, then you'll need to create
the ChangeLog file first.
**Tip**: *When you're making the commit log entries, use line breaks so
ChangeLog will be readable. That is, use lines \< 80 characters long.*

#### Update the NEWS file

To update the NEWS file, just read over the new ChangeLog entries and
summarize.

#### Update the Version Numbers

There are really 2 version numbers for each of these project items. The
*human* version (like version-3.17.5) and the *library* API/ABI version
which is represented as `CURRENT:REVISION:AGE`. There are special rules
for when each of the numbers in the library API/ABI version get
incremented that are triggered by the kinds of changes that where made
to the code base. The human version number is more arbitrary. So for
example, we might make a major API/ABI change and have to change to a
new Libtool version like `25:0:0` but the human version might only
change from bes-3.17.3 to bes-3.18.0

##### Version for Humans

1.  Determine the human version number. This appears to be a somewhat
    subjective process.
2.  Edit each of the *Affected Files* and update the human version
    number.

:;Affected Files:



***configure.ac*** - Look for:


`AC_INIT(libdap, ###.###.###, opendap-tech@opendap.org)`

debian/changelog (see [Debian
ChangeLog](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html#changelog))


**Take Note!** *The `debian/changelog` is the "single source of truth"
for the libdap4 version in the debian packaging. If this does not agree
with the version being packaged the package build will fail.*

**README.md**

**INSTALL**

##### API/ABI Version

The library API/ABI version is represented as CURRENT:REVISION:AGE.

The rules for shared image version numbers:

:# No interfaces changed, only implementations (good): Increment
REVISION.

:# Interfaces added, none removed (good): Increment CURRENT, set
REVISION to 0, increment AGE.

:# Interfaces removed or changed (BAD, breaks upward compatibility):
Increment CURRENT, set REVISION to 0 , and set AGE to 0.

See the *Appendix: How to see the scope of API/ABI changes in C++
sources* below for gruesome details. Often basic knowledge of the edits
is good enough.

Affected Files:
***configure.ac*** - Look for


DAPLIB_CURRENT=###

DAPLIB_REVISION=###

DAPLIB_AGE=###

### Commit

- Commit and push the code. Wait for the CI/CD builds to complete. You
  must be working on the *master* branch to get the CD package builds to
  work.

### Update the Build Offset

*Setting the build offset correctly will set the build number for the
new release to "0".*

In the file `travis/travis_libdap_build_offset.sh` set the value of
`LIBDAP_TRAVIS_BUILD_OFFSET` to the number of the last TravisCI build
plus one. The previous commit and push will have triggered a TravisCI
build. Find the build number for the previous commit in [the TravisCI
page for libdap4](https://app.travis-ci.com/github/OPENDAP/libdap4) and
use that build number plus 1.

This is not the build number for the package. It is the build number
used by Travis, which is the the total number of times Travis has build
the code. This number is the build number on the left-hand TOC

Once you have updated the `travis/travis_libdap_build_offset.sh` commit
and push this change. Do NOT use a `[skip ci]` string in the commit
message as it is important that this commit run through the entire CI
process.

### Tag The Release

In the past we manually made the tags for builds. Since we started a
'build number release' for NASA, we automated that.

If this is part of Hyrax, also tag this point in the master branch with
the Hyrax release number:

1.  **git tag -m "hyrax-<number>" -a hyrax-<numbers>** I think we can
    leave this tag as *hyrax-<version>* since it's for our own
    bookkeeping.
2.  **git push origin hyrax-<numbers>**

    NB: Instead of tagging the HDF4/5 modules, use the saved commit
    hashes that git tracks for submodules. This cuts down on the
    bookkeeping for releases and removes one source of error.

### Create the release on Github

Goto the 'tags' page ('code' then 'tags' at the top of the directory
window) and click the 'Tags' tab. There, click the ellipses (...) on the
right of the 'version-\*' tag and:

1.  Enter a *title* for the release
2.  Copy the most recent text from the NEWS file into the *describe*
    field
3.  Click *Update this release* or *Save draft*

This will trigger a 'archive and DOI' process on the Zenodo system.

### Publish and Sign

When the release is made on GitHub the source tar bundle is made
automatically. However, this bundle is **not** the one we wish to
publish because it requires people to have *autoconf* installed. Rather
we want to use the result of "`make dist`" which will have the
`configure` script pre-generated.

All you need do is build the tar file using `make dist`, sign it, and
push (or pull) these files onto www.opendap.org/pub/source.

1.  Go to the **libdap4** project on your local machine and run
    `make dist` which will make a libdap-x.y.z.tar.gz file at the top
    level of the **libdap4** project.
2.  Use **gpg** to sign the tar bundle:

    `gpg --detach-sign --local-user security@opendap.org libdap-x.y.z.tar.gz`
3.  Use **sftp** to push the signature file and the tar bundle to the
    /httpdocs/pub/source directory on www.opendap.org

    *(Assuming your current working directory is the top of the **bes**
    project)*

    `sftp opendap@www.opendap.org`

    `cd httpdocs/pub/source`

    `put libdap-x.y.z.tgz.sig`

    `put libdap-x.y.z.tgz`

    `quit`
4.  Check your work!
    1.  Download the source tar bundle and signature from
        www.opendap.org.
    2.  Verify the signature:

        `gpg --verify libdap-x.y.z.tgz.sig libdap-x.y.z.tgz`

### Get the DOI from [Zenodo](https://zenodo.org)

1.  Goto [Zenodo](https://zenodo.org) and look at the 'upload' page.
    Since the libdap, BES and OLFS repositories are linked to Zenodo,
    the newly-tagged code is uploaded to Zenodo automatically and a DOI
    is minted for us.
2.  Click on the new version, then click on the DOI tag in the pane on
    the right of the page for the given release.
3.  Copy the DOI as markdown from the window that pops up and paste that
    into the info for the version back in Github land.
4.  Also paste that into the README file. Commit using *\[skip ci\]* so
    we don't do a huge build (or do the build, it really doesn't matter
    that much).

Images for the above steps to help with the web UI: coming soon

### Update the online reference documentation

1.  *make gh-docs*

## Appendix: How to see the scope of API/ABI changes in C++ sources

Determine the new software version (assuming you don't already know the
extent of the changes that have been made)


For C++, build a file of the methods and their arguments using:


**nm .libs/libdap.a \| c++filt \| grep ' T .\*::' \| sed 's@.\* T
$.*$@\1@' \> libdap_funcs**

and compare that using `diff` on the previous release's library.

Assess the changes you find based on the following rules for the values
of `CURRENT`,`REVISION`, and `AGE`

- No interfaces changed, only implementations (good): ==\> Increment
  REVISION.
- Interfaces added, none removed (good): ==\> Increment CURRENT,
  increment AGE, set REVISION to 0.
- Interfaces removed or changed (BAD, breaks upward compatibility): ==\>
  Increment CURRENT, set AGE and REVISION to 0.

The current value of `CURRENT`,`REVISION`, and `AGE` can be found in
`configure.ac`:

``` bash
LIB_DIS_CURRENT=14
LIB_DIS_AGE=6
LIB_DIS_REVISION=1
```

Once you have determined the new values of the `CURRENT:REVISION:AGE`
strings then:

Edit the configure.ac and update the version values to the new ones.