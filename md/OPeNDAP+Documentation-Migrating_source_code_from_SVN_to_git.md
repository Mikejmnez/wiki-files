Each project in SVN will become a git repository. Using the technique
described below, the complete history and all of the branches and tags
will be migrated to the new git repo. Once done, the resulting git repo
can then be pushed up to GitHub (or another remote repo server) and used
by other people.

## Update

Github now has a really complete import tool. As of June 15, 2016, look
at [Importing from
Subversion](https://help.github.com/articles/importing-from-subversion/)
and paste the URL to the SVN repository in the upper text box and the
new (git) repo name in the lower one.

As the import progresses, github will ask about *commit authors.* Wait
until the import is complete and then supply that information. The
information in ![](Users.txt "Users.txt") might be helpful when looking
for those email addresses. It contains the user names and email
addresses for many of the people recorded in our SVN logs.

## <font color="red">Old information

All of the following is moot given the new import features of
Github.</font>

#### Note for OSX: Fix the SVN perl stuff on OSX 10.9

This is a needed fix for the SVN/Perl code on OSX 10.9 (and maybe other
versions of OSX). Also, on OSX you may need to remove a copy of svn
installing in /usr/local/... or /opt/... This can also cause weird
issues with eclipse and checkouts in existing dirs that used svn 1.8
(given that OSX is currently using svn 1.7).

sudo ln -s
/Applications/Xcode.app/Contents/Developer/Library/Perl/5.16/darwin-thread-multi-2level/SVN
/System/Library/Perl/Extras/5.16/SVN

sudo ln -s
/Applications/Xcode.app/Contents/Developer/Library/Perl/5.16/darwin-thread-multi-2level/auto/SVN/
/System/Library/Perl/Extras/5.16/auto/SVN

## SVN Users and their Emails

To make the import of a SVN project to git work smoothly, make a file of
SVN users and their emails. If there are any SVN users that have
committed code and they are not listed here, the whole process fails.
There may be a way to accept a default value in lieu of a name and
email, but I don't know how.

For the following, also see
<http://git-scm.com/book/en/Git-and-Other-Systems-Migrating-to-Git>
where there's more explanation.

First, build a text file that lists all of the users who have made
commits in our SVN repo. To do that, run the following over the entire
repo


*svn log \$svn --xml \| grep -e "\<author"\| sort -u \| perl -pe
's/<author>(.\*?)\<\\author\>/\$1 = /' \> users.txt*

where *\$svn* is the SVN repo URL, e.g., *<https://scm.opendap.org/svn>*

The result will be a file listing each username followed by '=' but what
git needs is


*username = real name <email>*

so edit the *users.txt* file so it matches the format git expects. For
the following commands to work, every name that git comes across when
processing code must be defined and that definition must include both a
real name and an email. Here's a sample of a file


benno = Benno Blumenthal \<benno@iri.columbia.edu\>

brent = Brent Baker \<bsquared@peak.org\>

carlw = Carl Wolfteich \<c.wolfteich@gso.uri.edu\>

caron = John Caron \<caron@ucar.edu\>

ccancellieri = Carlo Cancellieri \<ccancellieri@gmail.com\>

...

If the *users.txt* file is missing a name, or has a malformed name, the
*svn2git* command will bail, but that most likely will not happen right
away. Instead the failure will happen after much time has passed and the
entire operation will be wasted.

## Clone the SVN project to a new local git repo

I used two techniques to build (aka clone) the code and load it into a
local git repo. The first used *git svn clone* and the second used
*svn2git*. I think the latter was a bit smoother, and I'll document it
here (I'll put using *git svn clone* in an appendix just in case...).
Assuming that *users.txt* is in the cwd


*mkdir netcdf_handler*

*cd netcdf_handler*

*svn2git \$svn --trunk trunk/netcdf_handler --branch
branch/netcdf_handler --tags tags/netcdf_handler --no-minimize-url
--authors ../users_sorted.txt \| tee svn2git.log*

This will leave a new copy of the project (netcdf_handler, e.g.) in the
newly made directory. Also in there will be a git repo in the
subdirectory *.git*. Look at the branches and tag using *git branch* and
*git tag*. The code in the directory corresponds to the *master* branch.
Note that the process is pretty involved and you'll see a fair amount of
stuff scroll by in the terminal, including what appear to be checkouts
of unrelated code. Have no fear and just let the command run to
conclusion.

At OpenDAP, we use a less common subversion repo layout where each
'project' is held in a directory under 'svn/trunk' (e.g.,
svn/trunk/olfs) and the branches and tags are stored under
'svn/branch/<project>/<branch name>' and
'svn/tags/<project>/<tag name>'. The *--trunk*, *--branch* and *--tags*
options to *svn2git* seem like they work just like the options of the
same name to the *git svn* command.

If this does fail (e.g., a username was not defined in the *users.txt*
file), start over after fixing the users.txt file. To start over, you
need to remove the existing (and borked) current directory (e.g.,
netcdf_handler), make a new one and start over. Alternatively, you can
drop using the users.txt file and live with a really ugly commit
history/log. Don't do that if at all possible; get the users.txt file
right in the first place.

## Push the local repo up to a server

At this point the software is stored in a local git repo and we would
like it to be on a server. For this, I'm using GitHub. First, go to the
server and make an empty repository. For GitHub, this means logging into
an account, and choosing the 'Create New' menu item: ![<File:Migration>
Fig 1.png](Migration_Fig_1.png "File:Migration Fig 1.png")

Follow the instructions noting the repo URL. Here's what 'Create New'
looks like for a repo named 'test': ![<File:Migration> Fig
2.png](Migration_Fig_2.png "File:Migration Fig 2.png")

Click the big green 'Create repository' button and you'll see (but read
the text and don't just copy GitHub's suggestion for what to do):
![<File:Migration> Fig
3.png](Migration_Fig_3.png "File:Migration Fig 3.png")

In the directory with the local git repo, run the following commands,
which are almost exactly what GitHub suggests, but not quite


*git remote add origin <https://github.com/opendap/csv_handler.git>*

*git push --all --set-upstream origin*

Where the *git push* command is a bit different. First, -u is the same
as --set-upstream, but the latter is more verbose, which might come in
handy. Second, --all means push up all the branches, something we
certainly want, and third, we omit the 'refspecs' *master* because we
used *--all*.

Done.

## Appendix A

Using git svn clone to migrate code. This is based on notes from an
earlier attempt

git svn clone \$svn --trunk=trunk/svn_git_test
--branches=branch/svn_git_test --tags=tags/svn_git_test
--authors-file=users.txt --no-metadata svn_git_migrated

(It takes a long time where it's mostly getting various infrastructure
things, then the checkouts happen pretty fast, esp. given this project
is small)

These commands fix the branches and tags:

git for-each-ref refs/remotes/tags \| cut -d / -f 4- \| grep -v @ \|
while read tagname; do git tag "\$tagname" "tags/\$tagname"; git branch
-r -d "tags/\$tagname"; done

git for-each-ref refs/remotes \| cut -d / -f 3- \| grep -v @ \| while
read branchname; do git branch "\$branchname"
"refs/remotes/\$branchname"; git branch -r -d "\$branchname"; done

These set up the GitHub remote repos:

git remote add origin <https://github.com/jhrg/svn_git_migration.git>
git push origin --all git push origin --tags

## Appendix B

11/4/14

Here's how to use git svn init and git svn fetch (this will make more
sense if you read the Pro git book parts about init and fetch):

git svn init \$svn --trunk=trunk/libdap --branches=branch/libdap
--tags=tags/libdap --no-minimize-url libdap_git4

Then

cd libdap_git4

git svn fetch

Then here's a screen capture:

\[nori:libdap_git4 jimg\$\] git branch

- master

But it's actually all there...

This line makes the tags fit the git pattern:

git for-each-ref refs/remotes/tags \| cut -d / -f 4- \| grep -v @ \|
while read tagname; do git tag "\$tagname" "tags/\$tagname"; git branch
-r -d "tags/\$tagname"; done

This line similarly fixes the branches:

git for-each-ref refs/remotes \| cut -d / -f 3- \| grep -v @ \| while
read branchname; do git branch "\$branchname"
"refs/remotes/\$branchname"; git branch -r -d "\$branchname"; done

It looks bad, but:

``` bash
[nori:libdap_git4 jimg$] git branch
* master
  origin/3.10.0
  origin/3.11
  origin/3.11.1
  origin/3.11.2
  origin/3.9.0
  origin/dap4
 ...
  origin/trunk
  origin/ugrid
  origin/width_change
```

although I did not test to see if the tags were there...