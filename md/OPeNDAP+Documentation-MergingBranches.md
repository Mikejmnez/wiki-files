[\<\< back to HowTo Guides](HowTo_guides "wikilink")

## How to Merge Code with SVN

First, here are some references that might be useful. Get a copy of the
SVN 1.5 documentation. Google it. Also, this blog entry has some very
good information on one problem that you can run into with the
--reintegrate option:
<http://blogs.open.collab.net/svn/2008/07/subversion-merg.html>

## General pattern for branch development

When working on a new feature, do the development on a branch unless the
new feature is very simple addition that is unlikely to affect other
code (e.g., reading a new parameter from the .dodsrc file).

To work on a branch periodically merge the trunk back to the branch so
that the branch will have the small fixes and changes that are being
made to the trunk. This keeps the changes on the branch focused on the
new feature and that will simplify the eventual merge of that branch
back to the trunk.

When you're done with the new feature, do one last merge of the trunk to
the branch and then merge the branch to the trunk using the 'merge
--reintegrate' command.

## Merging from Branches Using only working copies of code

Experience has shown that this is a more reliable way to merge with SVN.

Another way to handle the sequence of events that takes place with
managing a release on branch is to use SVN's 'merge from a working copy
feature.' To do this, first have both the trunk and branch software
checked out, updated and committed - that is, have two working copies
that are identical to the HEAD revision of the trunk and the branch,
respectively. In the branch working copy (WC) merge the trunk code to
the branch. Once this works, you can check in the result to the branch
*or* you can wait and see how the next merge operation goes before
checking it in (it's best to check in the results of the merge in the
branch WC unless it's really broken, but it is not necessary to do it
before merging the branch WC to the trunk). Then in the trunk WC, merge
the branch WC into the trunk WC. Once this works, check that in. The
advantage of this is that you can do both merges before checking in the
results.

Here's what it looks like in practice:

``` bash
cd $trunk
svn up
svn ci

cd $branch
svn up
svn ci

svn merge [--dry-run] $trunk # In the directory $branch, run merge with the $trunk directory as its argument
# Now make sure $branch builds/works
# This directory ($branch, the svn branch WC) now contains the result of merging the $trunk code to the $branch code

cd $trunk
svn merge [--dry-run] $branch # In the $trunk directory, run merge with the $branch directory as its argument
# Make sure this code builds/works
# This (svn trunk WC) now contains the merge of the differences between the $branch code and this code

svn ci # Check in the code in $trunk to svn's trunk

cd $branch
svn ci # Check in the code in $branch to svn's branch
```

## Merging process with only the destination code checked out

Although this is should be no more or less hard than the above,
experience says it's more error-prone than the above technique.

Here's the process I used to actually perform a merge from the trunk to
a branch:

1.  In a copy of the trunk:
    - update
    - check in. This makes sure you don't have anything outstanding in
      your working copy of the trunk. Of course, other people should do
      this too. It's probably not 100% necessary, but it cuts down on
      the confusion when things go awry.
2.  In a copy of the branch:
    - update
    - check in. This means that the code in your working copy is the
      same as the code at the HEAD of the branch.
    - merge the trunk to the branch. For example: \`svn merge --accept
      postpone \$svn/trunk/libdap\`. The *postpone* argument tells svn
      to write conflicts in the source and you'll fix them up later.
    - resolve the conflicts. You can find all the conflicted files using
      \`svn status \| grep '^C'\`. Regarding resolving conflicts,
      there's no easy way to sort it out. When a merge generates a
      conflict the result is ... a mess. Well, OK, not always. Often it
      is just a comment or something but sometimes you need to look at
      the various different versions and edit the file to be something
      entirely new. The Trac code browser is really useful for looking
      at different versions of the code.
    - mark the conflicts as resolved. \`svn resolved <files>\`
      \[mjohnson: svn 1.6.5 suggests using \`svn resolve --accept
      working\` over resolved\]
    - build and test the code *before* you check it in. Be sure to clean
      out *everything* and run autoreconf, configure --prefix=\$prefix,
      ... as you do these. I found the BES to be very sensitive to older
      libraries. In the end since I have both branch and trunk code
      installing into its own prefix I just removed the *bin*, *lib*,
      ..., directories. they were populated with new stuff as I ran make
      install.
    - check in the result of the merge.
3.  Now move back to the trunk and run \`merge --reintegrate
    \$svn/branch/...\`. If there are no changes added between the two
    merge operations there should be no conflicts during the reintegrate
    merge. However, ...

For merges of several related projects, I did all of them up to the
point where I would check in the result of the merge so I could test the
ensemble before saving the result.

## Minor fixes once code has been tagged but before an official release

Assumption: You're using a SVN 1.5 or greater client.

If a small set of changes, for example, to the RPM build process, is
needed after the source code has been tagged, then check those changes
into the tag. If these changes affect the dependencies of other
packages, do not do this. If these changes are going to require changes
in other packages, make them to the release branch, tags that with a new
version and release. Also, if more than just a very few lines needs to
be changed, it should really be checked into the branch and tagged with
a new version.

Since at this point the release branch will have been merged back to the
trunk already, add the new changes first to the tag and then merge that
tag back to the trunk. Use:

``` bash
svn merge --dry-run $svn/tags/<component>/<version>
```

in the trunk directory. If the dry run looks like its doing what you
expect, then go ahead with the merge. If not, you probably should have
put these changes on the branch and made a new version for release. Back
the changes out of the tag and make a new tag, version and release.

## Errors

### The *reintegrate* option

Supposed you run into:

``` bash
[jimg@zoe freeform_handler]$ svn merge --reintegrate $svn/branch/xmlrequest/freeform_handler
svn: Cannot reintegrate from 'https://scm.opendap.org/svn/branch/xmlrequest/freeform_handler' yet:
Some revisions have been merged under it that have not been merged
into the reintegration target; merge them first, then retry.
```

This message likely means that somewhere in the trunk a file was copied
or moved. I think. The fix is to do the merge the old way, by hand, as
it were. The following example is run in a working copy of the trunk
code and the first URL given to merge is the last revision of the trunk
that was merged to the branch. The second argument is the branch, which
defaults to the HEAD revision. This is essentially what --reintegrate
does.

``` bash
[jimg@zoe netcdf_handler]$ svn merge --dry-run $svn/trunk/netcdf_handler@19627 $svn/branch/xmlrequest/netcdf_handler
--- Merging differences between repository URLs into '.':
U    ncdds.cc
U    nc_handler.cc
U    NCUInt32.cc
...
```

How do you find that 'last merged trunk revision?' By sleuthing. Look at
the log for the trunk and/or go to the Trac site and browse the trunk
code. If you're lucky you're doing this 'reintegrate merge' (merging a
branch to the trunk) just after merging the trunk to the branch to make
sure the branch is up to date. In that case the last trunk version
merged to the branch will be the current HEAD revision of the trunk and
you could just say

``` bash
svn merge $svn/trunk/netcdf_handler $svn/branch/xmlrequest/netcdf_handler
```

and leave off the explicit revision number.

### Conflicts

Dealing with conflicts in the code:

``` bash
    /** set the structure values to create the FreeForm DB**/
    SetUps->user.is_stdin_redirected = 0;
    SetUps->input_file = FileName;
<<<<<<< .working

#if 0
    // See my comments in ffdds.cc 10/30/08 jhrg
#ifdef RSS
    string iff = find_ancillary_rss_formats(filename);
#else
    string iff = find_ancillary_formats(filename);
#endif
=======

#ifdef RSS
    string iff = find_ancillary_rss_formats(filename);
#else
    string iff = find_ancillary_formats(filename);
#endif
>>>>>>> .merge-right.r19853
    char *if_f = new char[iff.length() + 1];
    iff.copy(if_f, iff.length());
    if_f[iff.length()]='\0';

    SetUps->input_format_file = if_f;
#endif
```

In this case the diff doesn't completely make sense, but the first part
(*.working*) is from the working copy and the second part is from the
right hand URL given to merge, so it's the 'other' code. Aa I said
above, you sometimes have to look around at the various versions to sort
out what really should wind up the file. In most cases it's pretty
obvious.

### Merging branch to trunk again: Three Way Merge

This is the syntax for performing subsequent branch-to-trunk merges.

It is **crucial** to this use case for subversion that **each** and
**every** time you merge a branch back to the trunk that you create a
tag of the branch at that time. Then later when you need to merge the
branch back to the trunk again (to pickup additional changes that
happened on the branch since the last merge) you will use the most
recent tag. The syntax for this is described in the ***svn book**'' in
Chapter 4, in the section titled:***Merge Syntax: Full Disclosure**''.

<font size="3"> <code>


svn merge \[--dry-run\] <http://last.tag.url> <http://branch.url>
local_working_copy_of_trunk

</code> </font>

Where:

- *<http://last.tag.url>* - is the subversion url of the last tag
  (associated with a merge to the trunk) for the branch in question.
- *<http://branch.url>* - is the subversion url of the branch that need
  to be merged to the trunk.
- *local_working_copy_of_trunk* - Is a local up to date copy of the
  trunk.

First make sure it looks like it's going to do what you think it should
using the *--dry-run* option!

If your changes are only to one or two files then *--dry-run* should
tell you that only those are being updated. If you see a bunch of other
stuff **STOP** - you've got something wrong and the result will be a big
heap of FAIL.