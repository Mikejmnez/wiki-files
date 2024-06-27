[\<\< back to HowTo Guides](HowTo_guides "wikilink")

## How to make a Matlab ocean toolbox release

##### Make sure that the source code you're working with is up to date with what is in subversion

` As the GUI developer you need to  insure that the code in subversion is consistent with what you want  to release.  You can use 'svn status' to check whether or not your  version is up to date with the repository.  If any of your files show  'M' returned from 'svn status', that means they're locally modified  and your local code is different from what's in the repository.  In  that case you should double-check to make sure your local code is correct and use 'svn commit' to upload your code to the repository.   Once the repository is up to date with your local code you can continue with the next step.`

##### Since this is a shared repository, any new GUI version should be checked out as thoroughly as possible (on Matlab) before committing the changes to subversion.

##### In regards to naming and numbering conventions:

- If this is a new GUI being added to the toolbox, the name of the GUI
  should reflect the spelling and capitalization used in the original
  data set. For example, if an acronym is used such as MODIS or GOES,
  the name should reflect this capitalization.
- If you are using a function that is also used in another Matlab Ocean
  Toolbox GUIette, this function should be in the Common directory. If
  it is not, then you should move it to the common directory and notify
  the developer of the other GUIette(s) to do the same. Both of you
  should remove it from the individual GUIette directories.
- Make sure that all file names (e.g., \*.m files) in a GUI directory
  are unique and are not replicated in other GUI directories. This is
  important because Matlab is not able to differentiate between files of
  the same name in different sub-directories.
- Version numbers should adhere to a two element numbering scheme. The
  subversion number will be equivalent to a third number, which is
  reserved only for minor changes being kept track of in subversion. As
  such, most of the versions released will end in zero, with only the
  second digit usually being changed.

##### Use the 'zip' command to create an archive containing the GUI code from your local SVN directory.

##### Email the zipped file to cwolfteich@gso.uri.edu for him to test and upload to dev1.opendap.org

##### Copy the current repository code as a release.

To do that you would 'cd' to the directory containing your local SVN
code for the GUI you are releasing, then use SVN to make a copy, as in
this example:

``` sh
svn copy . https://scm.opendap.org/svn/tags/ml-ocean-toolbox/HYCOM/1.13.5
```

This will copy all the files from the current directory ('.') to a
subdirectory named '1.13.5' under ...svn/tags/ml-ocean-toolbox/HYCOM.
This way, each time a release is made, a copy of the new code is placed
in a directory named with the release version number.