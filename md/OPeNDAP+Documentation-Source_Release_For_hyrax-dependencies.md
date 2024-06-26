This task is to ensure that the *hyrax-dependencies* project is up to
date and tar balls on www.o.o. are current.

## Update ChangeLog, NEW, and release version

### Update the **ChangeLog** file.

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

### Update the Version Numbers

If the review of the ChangeLog indicates that there have been changes
since the last release, increment the version number in the Makefile.

Make sure any change in version number is also reflected in the NEWS
file.

### Update the NEWS file

To update the NEWS file, just read over the new ChangeLog entries and
summarize.

## Commit And Push

1.  Save, commit, and push the changes to master branch.
2.  Once the *hyrax-dependencies* CI build is finished
    1.  Trigger a CI build *libdap4* by pushing a small change to the
        *libdap4* master branch. When that CI build has completed
        successfully,
    2.  Trigger a CI build in the *bes* by pushing a small change to the
        *bes* master branch.
3.  Wait for the successful completion.

    If there's a problem with the CI builds at this point you may wish
    to follow the advice of ***Herman Wouk***: *"When in danger or in
    doubt, run in circles, scream and shout"*

## Publish and Sign

All you need do is build the tar file using `make dist`, sign it, and
push (or pull) these files onto www.opendap.org/pub/source.

1.  Go to the **hyrax-dependencies** project on your local machine and
    run `make dist` which will make a hyrax-dependencies-x.y.tar.gz file
    in the directory above the top level of the **hyrax-dependencies**
    project.
2.  Use **gpg** to sign the tar bundle:

    `gpg --detach-sign --local-user security@opendap.org ../hyrax-dependencies-x.y.tar`
3.  Use **sftp** to push the signature file and the tar bundle to the
    /httpdocs/pub/source directory on www.opendap.org

    *(Assuming your current working directory is the top of the
    **hyrax-dependencies** project)*

    `sftp opendap@www.opendap.org`

    `cd httpdocs/pub/source`

    `put hyrax-dependencies-x.y.tar.sig`

    `put hyrax-dependencies-x.y.tar`

    `quit`
4.  Check your work!
    1.  Download the source tar bundle and signature from
        www.opendap.org.
    2.  Verify the signature:

        `gpg --verify hyrax-dependencies-x.y.tar.sig hyrax-dependencies-x.y.tar`

## Tag The Release

1.  Tag, and push the tag.
    - *git tag -m "version-<number>" -a <numbers>*
    - *git push origin <numbers>*

## Make The Release On GitHub

1.  Goto the [GitHub 'tags' page for
    *hyrax-dependencies*](https://github.com/OPENDAP/hyrax-dependencies/tags).
2.  Click the "Create release from tag" button
3.  Enter a title for the release (looks at previous releases for
    examples)
4.  Copy the most recent text from the NEWS file into the describe field
5.  Click Save/Update this release.