## Installation

Installation instructions for various platforms can be found at
[Installing
git-secrets](https://github.com/awslabs/git-secrets#installing-git-secrets).

`> Checking that git-secrets is installed`
`>> Installing git-secrets`

You can check that git-secrets has been successfully installed by
running:

`$ git secrets help`
`usage: git secrets --scan [-r|--recursive] [--cached] [--no-index] [--untracked] [`<files>`...]`
`  or: git secrets --scan-history`
`  or: git secrets --install [-f|--force] [`<target-directory>`]`
`  or: git secrets --list [--global]`
`  or: git secrets --add [-a|--allowed] [-l|--literal] [--global] `<pattern>
`  or: git secrets --add-provider [--global] `<command>` [arguments...]`
`  or: git secrets --register-aws [--global]`
`  or: git secrets --aws-provider [`<credentials-file>`]`
`...snip...`

Installing git-secrets DOES NOT enable it for any existing repositories!
Once you have git-secrets installed, you MUST follow the directions
under Configuration and Apply to Existing Repos, below.

## Configuration

Once git-secrets is installed, it must still be configured to look for
AWS access keys and to apply to any new git repos you create or clone.
Run the following commands (from Advanced Configuration):

`$ git secrets --register-aws --global`
`$ git secrets --install ~/.git-templates/git-secrets`
`$ git config --global init.templateDir ~/.git-templates/git-secrets`

Once you've done this, git-secrets and the AWS access key patterns will
automatically be added to any new git repo you create or clone on this
workstation.

The git-secrets global configuration does not apply retroactively to any
git repos you already have on the system; to add to these repos, see the
following section ("Apply to Existing Repos")

## Apply to Existing Repos

If you already have git repositories on your workstation, you will need
to apply git-secrets to these as well. In each git repo that you have
cloned locally, run the following commands:

`$ git secrets --install`
`$ git secrets --register-aws`

Adding git-secrets to each git repo manually can be time consuming if
you have a large number of repositories. This script will scan your home
directory recursively for any git repositories and automatically apply
git-secrets to all of them: git-secrets-apply.sh, shown below:

    #!/bin/bash

    echo "> Applying git-secrets to existing repos (under $HOME)"
    echo "> If you have alot of data, this may take a while."
    find $HOME -type d -name .git | while read git_path; do
        repo_folder=$(dirname "$git_path")
        echo ">> Applying git-secrets to $repo_folder"

        cd "$repo_folder"

        if [ -e ".git/hooks/commit-msg" ]; then
            echo ">>> $repo_folder already has commit-msg hook installed"
        else
            echo ">>> Installing git-secrets"
            git secrets --install
        fi

        echo ">>> Registering AWS patterns"
        git secrets --register-aws
    done

`$ bash git-secrets-apply.sh`
`> Applying git-secrets to existing repos (under /Users/username)`
`> If you have alot of data, this may take a while.`
`>> Applying git-secrets to /Users/username/repo1`
`>>> Registering AWS patterns`
`>> Applying git-secrets to /Users/username/repo2`
`>>> Registering AWS patterns`
`...snip...`

## False Positives

The git-secrets tool can sometimes mistake certain strings in your code
or documentation as access keys or other sensitive information; for
example, it may flag an example key in a "How To" document as an actual
key, and refuse to allow the commit. When this occurs, you can use the
following commands to ignore a particular string/pattern:

`$ git secrets --add --allowed AKIAAFAKEKEY12345678`
`$ git secrets --add --allowed fake12345678example12345678fake123456789`

BE VERY CAREFUL when adding permitted patterns to git-secrets! Don't add
anything that you're not completely sure is benign.