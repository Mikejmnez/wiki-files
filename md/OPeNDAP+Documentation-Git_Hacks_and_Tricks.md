## Git resources

- The [Pro GIT](http://git-scm.com/book/en/v2) book is online at:
  git-scm.com
- Good cheat sheet:
  <http://ndpsoftware.com/git-cheatsheet.html#loc=workspace>;
- Info on branching from git.com:
  <http://git-scm.com/book/en/Git-Branching-Remote-Branches>
- Migration to git:
  <http://git-scm.com/book/en/Git-and-Other-Systems-Migrating-to-Git>

## Setup a username and access token for GitHub


git config --global github.user <name>

git config --global github.token <token>

<!-- -->


where the token is made using the instructions at
<https://help.github.com/articles/creating-an-access-token-for-command-line-use>

If you want to configure a token for use with the OSX keychain, get the
credential-osxkeychain tool with brew if you need to. Test if you have
it by running **git credential-osxkeychain** and look for the
credential-osxkeychain help message. To *use* the git extension, you
need to enter **git credential-osxkeychain <command>** and then, on the
next line, enter **host=github.com** and maybe **protocol=https** and
other key/value pairs and then a blank line. See the examples below.

To use the osx keychain, first check if you have a password/token
already saved:


git credential-osxkeychain get


host=github.com

protocol=https

<cr>

Erase the password/token


git credential-osxkeychain erase


host=github.com

protocol=https

<cr>

Then set the new token


git credential-osxkeychain store


host=github.com

protocol=https

username=<your login>

password=<your token>

<cr>

Then use the **get** command to verify.

## Git Secrets

Use this tool, which is run automatically before each commit, to keep
from adding AWS and other secret keys to code that is destined for a
public repository. Doing that will 'leak' the key and Amazon _will_
notice. The remedy will involve every account changing its password and
every key pair being 'rotated' (i.e., every key pair has to be replaced
with t anew one).

<https://github.com/awslabs/git-secrets>

Scroll down to the bottom for installation instructions. On OSX, you can
use brew and do not have to clone the repo. Here's what I did:


brew install git-secrets \# install _git secrets_

git secrets --register-aws --global \# configure git so all future
cloned repos will use it

git secrets --install ~/.git-templates/git-secrets \# set up all
existing repos so they use it.

git config --global init.templateDir ~/.git-templates/git-secrets

You can read a bit more of the docs in the _git secrets_ repo and
configure a more fine grained approach.

## Subtrees: how to incorporate code from other repositories

Git subtrees are an alternative to submodules and are easier for the
users of the parent repository (in our case, typically the BES repo).
There is a fair amount of information about subtrees, although the main
thing to know is that once the code for the child repo is part of the
parent, in most cases there's nothing else to do. Only when changes get
made in the code from the child repo are extra steps to keep the
parent's copy of the code and child repo in sync needed (and then, only
if you want to keep them in sync). For normal branch-PR-merge
operations, there is no need to think about the subtree management
commands.

Here's a [discussion about the differences between submodules and
subtrees](https://winstonkotzan.com/blog/2016/09/26/git-submodule-vs-subtree.html).

### How to incorporate code from another repo

To incorporate code from another repo (the *child*) into an existing
repo (the *parent*), use these steps:

1.  name the other project *child*, and fetch: **git remote add -f
    *child* https://github.com/...**



The *-f* option runs *git fetch* automatically after the remote repo is
added. See [git remote](https://git-scm.com/docs/git-remote).

1.  prepare for the later step to record the result as a merge.: **git
    merge -s ours --no-commit --allow-unrelated-histories
    *child*/master**



The *-s* option to *git merge* selects the *ours* strategy for the
merge; *--no-commit* does not commit the merge automatically. See [git
merge](https://git-scm.com/docs/git-merge#_merge_strategies). If you are
using git 2.9+, add the option *--allow-unrelated-histories*, but older
versions of git don't support that (as of Jan. 2022, OSX was using git
2.32).

1.  read "master" branch of *child* to the subdirectory *dir-child*:
    **git read-tree --prefix=*dir-child*/ -u *child*/master**



The *-u* option causes *git read-tree* to update the files in the
working directory. See [git
read-tree](https://git-scm.com/docs/git-read-tree).

1.  record the merge result: **git commit -m "Merge *child* project as
    our subdirectory"**

### About Git subtree merges

To pull in subsequent update from *child* using "subtree" merge: **git
pull -s subtree *child* master**

To learn more about subtree merges, see [About Git subtree
merges](https://docs.github.com/en/get-started/using-git/about-git-subtree-merges).

### How to remove a submodule

If a child repo was included in a parent repo using git submodules,
here's how to remove it so that the child repo can be included using
subtrees as documented above.

- **git rm -r *path/to/submodule***
- **rm -rf .git/modules/*path/to/submodule***

If the second line isn't used, even if you removed the submodule for
now, the remnant .git/modules/the_submodule folder will prevent the same
submodule from being added back or replaced in the future.

Also, using just these two commands will leave an entry for the
submodule in *.git/config*. To remove that,

- **git config -f .git/config --remove-section
  submodule.*path/to/submodule***

Here is an older way that illustrates where all the information is held:

How do I delete a submodule?

NB: Ignore the submodule help info about *deinit* since that seems to
leave too much undone.

To remove a submodule you need to:

- Delete the relevant line from the *.gitmodules* file.
- Delete the relevant section from *.git/config*.
- Delete the submodule info in *.git/modules*: **rm -rf
  .git/modules/<path_to_submodule>**
- Run **git rm --cached path_to_submodule** (no trailing slash).
- Commit the parent repo (**git commit -m "Removed submodule ..."**)
- Delete the now untracked submodule files.

## Someone forked and issued a PR on our repo, but ...

The Travis build failed because that person is not allowed access to our
AWS.

One way is to copy their branch to our remote (aka the 'origin' remote)
and issue a PR on it.

1.  Set up their remote as one you can reference.
2.  Fetch the branches of that remote (you can fetch just the one
    branch)
3.  Checkout that remote/branch combo.
4.  Checkout with branching to move it to your default remote (which is
    liley called 'origin').
5.  Push that branch to github

Here are the commands (with a real example):

1.  git remote add <https://github.com/Bo98/libdap4.git>
2.  git fetch Bo98
3.  git checkout Bo98/libtirpc-fix // Bo98 is the remote, libtirpc-fix
    is the branch
4.  git checkout -b libtirpc-fix // That makes the code just checked out
    a branch for the 'origin' remote
5.  get push -u origin libtirpc-fix // Now that code is a branch in our
    repo and Travis will work.

## Cheat sheet items

These are simple things that are not really obvious from the git book or
other sources

How do I see the most recent commit date for all my branches?

git branch --sort=creatordate --sort=committername --format "%(align:20)
%(creatordate:relative) %(end) %(align:25) %(committername) %(end)
%(refname:lstrip=-1)"

<!-- -->

About *git rebase*; I have a branch with lots of commits and I want to squash them. How? Oh, I pushed those commits to github...

The trick is to use *git log* to find the commit hash of the starting
point for *rebase* and *git rebase --interactive* and then *git push
origin +<branch>*.

***Here's a HowTo on [Squashing
commits](Squashing_commits "wikilink")***

<!-- -->

I forked someone's repo, now I want to synch to their 'master' branch. How?
Follow these steps


Set up an 'upstream' remote:
<https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/configuring-a-remote-for-a-fork>

Then do these operations to get and merge the changes to 'master':
<https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/syncing-a-fork>

<!-- -->

I keep getting a 'Permission Denied (publickey)' error when I push!
Follow these steps to make and use a public/private key pair for github


cd ~/.ssh.

Within .ssh, there should be these two files: id_rsa and id_rsa.pub. If
those two files are not there...

To create the SSH keys, type ssh-keygen -t rsa -C
"your_email@example.com".

Open id_rsa.pub in a text editor, and copy the contents, exactly as it
appears, of id_rsa.pub

and paste it into GitHub and/or BitBucket under the Account Menu (upper
right corner) Settings \> SSH Keys.

<!-- -->

I just made a perfectly good commit to the wrong branch. How do I undo the last commit in my master branch and then take those same changes and get them into my upgrade branch?
If you haven't yet pushed your changes, you can also do a soft reset:

*git reset --soft HEAD^*

This will revert the commit, but put the committed changes back into
your index. Assuming the branches are relatively up-to-date with regard
to each other, git will let you do a checkout into the other branch,
whereupon you can simply commit:

*git checkout \[-b\] branch*

*git commit*

The disadvantage is that you need to re-enter your commit message.

<!-- -->

How to see a list of 'conflicted' files after a merge
git diff --name-only --diff-filter=U

How to see the difference between to commits
git diff <commit-hash-1> <commit-hash-2>, e.g., git diff 0da94be 59ff30c

...for a specific file: git diff <commit-hash-1> <commit-hash-2> --
<file>

...and don't forget the shorthand for the hashes: git diff HEAD^^..HEAD
-- main.c where *HEAD^* is the parent of HEAD. HEAD{n} is the Nth
parent.

How to see the different remote branches:
git remote show origin

Fetch all the branches on *origin*
git fetch origin

How do I list the remote branches (that have been fetched)?
git branch -a

How do I switch to a branch from a remote origin?
git checkout -b test origin/test

or, with newer versions of git: git checkout test

How do I see what would be pushed to a remote repo?
git push --dry-run

git diff origin/master \# Assumes you have run git fetch, I think

git diff --stat origin/master \# --stat just shows the file names stats,
not the diffs

To get a specific file from a specific branch
git show dap4:./gdal_dds.cc \> gdal_dds.dap4.cc *You can use checkout
instead of show and that will overwrite the file.*

the general syntax is *object* (that's the 'dap4:./gdal_dds.cc' part)
and it can use the ^ and ~n syntax to specify various commits on the
given branch. A SHA can also be used.

How to change the 'origin' for a remote repo
git remote set-url origin git://new.url.here (https URLs work too...)

How to push a local branch to a remote repo
git push -u origin feature_branch_name

How to make and track a new (local) branch
How to cause Travis CI to skip a build
Add *\[ci skip\]* to the log text. See the about topic on amending a
commit log, which can be handy

git checkout -b <branch name>

How to track a remote branch
git checkout --track origin/serverfix *or* git checkout -b sf
origin/serverfix

How do I make an *existing* local branch track an existing remote branch?
git branch --set-upstream upstream/foo foo where *upstream* is probably
actually *origin*.

Commited my code, then made a bunch of changes that just seem like a bad idea in retrospect. How do I go back to my previous commit for everything in a directory? *I don't care if I loose all my changes since the last commit.*
git reset HEAD --hard (Note that this is one of the very few git
commands where you really cannot undo what you have done).

How to undo a commit (that has not been pushed)
git reset --soft HEAD~1. This leaves the files in their changed state in
your working dir so that you can edit them and recommit. You can also
change to a different branch and commit there, then change back.

In the above case, To reuse the old commit message
git commit -c ORIG_HEAD \<-- This works because 'reset' copied the old
head to .git/ORIG_HEAD. If you don't need to edit the old message, use
-C instead of -c.

How to delete a remote brnach
git push origin --delete serverfix *The data are kept for a little bit -
before git runs garbage collection - so it may be possible to undo
this.*

How to delete a local branch
git branch -d the_local_branch *and delete the remote branch you were
tracking with the same name* git push origin :the_remote_branch

How to I set up a git cloned repo on a remote machine so I don't have to type my password all the time?
This page shows how to make a PKI key-pair with a secure password,
configure the machine to remember the password using ssh-agent and
upload the public key to your github account so it'll use the key for
authentication. <https://help.github.com/articles/generating-ssh-keys/>

How can I know which branches are already merged into the master branch?
*git branch --merged master* lists branches merged into master

*git branch --merged* lists branches merged into HEAD (i.e. tip of
current branch)

*git branch --no-merged* lists branches that have not been merged

By default this applies to only the local branches. The -a flag will
show both local and remote branches, and the -r flag shows only the
remote branches.

Switching remote URLs from HTTPS to SSH
*git remote -v*

\# origin https://github.com/USERNAME/REPOSITORY.git (fetch)

\# origin https://github.com/USERNAME/REPOSITORY.git (push)

*git remote set-url origin git@github.com:USERNAME/OTHERREPOSITORY.git*

''git remote -v

\# Verify new remote URL

\# origin git@github.com:USERNAME/OTHERREPOSITORY.git (fetch)

\# origin git@github.com:USERNAME/OTHERREPOSITORY.git (push)

Amending the commit message
*git commit --amend*

*git commit --amend -m "New commit message"*

How do I revert a commit after if it has been pushed?:
Given:


*e512d38 Adding taunts to management.*

*bd89039 Adding kill switch in case I'm fired.*

*da8af4d Adding performance optimizations to master loop.*

*db0c012 Fixing bug in the doohickey*

If you just want to revert the commits without modifying the history,
you can do the following:


*git revert e512d38*

*git revert bd89039*

Alternatively, if you don’t want others to see that you added the kill
switch and then removed it, you can roll back the repository using the
following (however, this will cause problems for others who have already
pulled your changes from the remote):


*git reset --hard da8af4d*

*git push origin -f localBranch:remoteBranch*

The gitlog-to-changelog script comes in handy to generate a GNU-style ChangeLog.
As shown by gitlog-to-changelog --help, you may select the commits used
to generate a ChangeLog file using either the option --since:


*gitlog-to-changelog --since=2008-01-01 \> ChangeLog*

or by passing additional arguments after --, which will be passed to
git-log (called internally by gitlog-to-changelog):


*gitlog-to-changelog -- -n 5 foo \> last-5-commits-to-branch-foo*

Amending the commit message
*git commit --amend*

Tagging stuff
*git tag* will list the existing tags

*git tag -a <tag name>* adds a new tag

*git push origin <tag name>* pushes that tag up to the server *origin*

*git push origin --tags* pushes all new tags up to *origin*

How to resolve conflicts in a submodule when you've just merged master down to a branch
Run git status - make a note of the submodule folder with conflicts

Reset the submodule to the version that was last committed in the
current branch:

git reset HEAD path/to/submodule

At this point, you have a conflict-free version of your submodule which
you can now update to the latest version in the submodule's repository:

cd path/to/submodule

git pull origin SUBMODULE-BRANCH-NAME

And now you can commit that and get back to work.

How to move a submodule into the main repo
If all you want is to put your submodule code into the main repository,
you just need to remove the submodule and re-add the files into the main
repo, follow the prescription below. If you want to see how to add the
branches, history, etc. to the repo, see
<http://stackoverflow.com/questions/1759587/un-submodule-a-git-submodule>:

*git rm --cached submodule_path* **\# delete reference to submodule HEAD
(no trailing slash)**

*git rm .gitmodules* **\# if you have more than one submodules, you need
to edit this file instead of deleting!**

*rm -rf submodule_path/.git* **\# make sure you have backup!!**

*git add submodule_path* **\# will add files instead of commit
reference**

*git commit -m "remove submodule"*

Checking out a tag
You will not be able to checkout the tags if its not locally in your
repo so first you have to fetch it all.

First make sure that the tag exists locally by doing

1.  --all will fetch all the remotes.
2.  --tags will fetch all tags as well

*git fetch --all --tags --prune*

Then check out the tag by running

*git checkout tags/<tag_name> -b <branch_name>*

Instead of origin use the tags/ prefix.

How to remove old/unused/deleted branches
*git remote prune origin* prunes tracking branches not on the remote.

*git branch --merged* lists branches that have been merged into the
current branch )but maybe including **master** so be careful about the
next part).

*xargs git branch -d* deletes branches listed on standard input.

Be **careful** deleting branches listed by *git branch --merged*. The
list could include master or other branches you'd prefer not to delete.

How do I merge just one file?
A simple command already solved the problem for me if I assume that all
changes are committed in both branches A and B

*git checkout A*

*git checkout --patch B f*

The first command switches into branch *A*, into where I want to merge
*B* 's version of the file *f*. The second command patches the file *f*
with *f* of HEAD of *B*. You may even accept/discard single parts of the
patch. Instead of *B* you can specify any commit here, it does not have
to be HEAD.

## Continuous Integration builds involving submodules

There are two ways to handle getting a CI build to run when you've
editing a submodule used by the BES. The \*\*best\*\* way is to use a
branch of the BES to run the build as part of a \*\*pull request\*\* and
is described as option number one below. Another way is to use the
master branch of the BES and is describe as the second choice.

#### Using a BES branch and a GitHub pull request

This is the better of the two ways. It requires a bit more work but does
not introduce code to the master branch before that code has passed the
CI build.

Once your work on the submodule is complete:

- Commit and push the code in your submodule's branch


git commit

git push

- Goto the top of the BES and checkout a new branch. Choose a name that
  is similar to the name of the branch used for the submodule's changes


cd bes

git checkout -b <name>

- Commit the submodule's current commit hash to the .gitmodules file
  (easier than is sounds)


'git commit -a' or 'git add <path to submodule>' and then 'git commit'

- Push this to GitHub


git push

- Goto Github and issue a pull request for the BES <name> branch.

This will trigger a CI build of that branch. This does not change the
BES master branch at all, which is the goal here - to build without
affecting the master branch.

Once the build works, merge the submodule branch to the submodule's
master. Then delete the BES <name> branch and make sure to update the
BES master so that it references the new master branch for the
submodule.

#### An alternative, that uses the BES master branch

Once your code is in committed and pushed on the master branch, go to
the top of the bes project and run ‘git commit -a’. This will prompt you
with a commit that shows a new HDF5 handler version. Add a commit
message (e.g., “New HDF5 handler version”) and then push. This works
because the new hash code for the commit that is on the master branch is
recorded in a hidden file managed by git (named ‘.gitmodules’). Since
that file is changed, the push results in changed files in bes and than
triggers a new build. The new build will reference the new commit hash
for your latest changes on master and that hash will be checked out for
the build.

## Merging in a branch where submodules have been removed and replaced with directories

Recently (02/03/17) we dropped most of the submodules in the **bes**
(except for the *hdf\*_handler* modules) and replaced them with regular
code directories.

What to do if you have an existing branch the you need to update with
the new master:

1.  Check in all of your changes.
2.  Push your branch to github.
3.  Start with a brand new clone for the bes repo from github:

    *git clone <https://github.com/opendap/bes>*
4.  DO NOT RUN *submodule init*
5.  In the new clone, checkout your branch.
6.  In the *modules* directory remove crucial submodules:

    *rm -rf csv_handler dap-server debug_functions fileout_\*
    fits_handler freeform_handler gateway_module gdal_handler
    ncml_module netcdf_handler ugrid_functions w10n_handler
    xml_data_handler*
7.  Now, merge the master to your branch:

    *git merge master*
8.  Cleanup any trouble (there should not be much)
9.  Build and test
10. Merge your branch to master when you're ready.