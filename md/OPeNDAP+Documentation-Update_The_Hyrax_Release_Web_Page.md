Currently we have a single page for each major release. Minor release
information is added to the major release page to which the minor
release is associated. For example if you are making minor release
Hyrax-1.16.5 then you would add the release information to the
Hyrax-1.16 page.

## Creating A Major Release Page

1.  If this is a major release copy the previous major release X.Y page
    to X.(Y+1).
2.  Clear out the contents of the page while maintaining the structure:
    - Release Information
      - New Features (*Remove content.*)
      - Bug Fixes (*Remove content.*)
    - Hyrax Software Downloads (*Keep contents but update as described
      in the Minor Release section below.*)
      - Required External Dependencies (*Ibid*)
      - Binary Packages (*Ibid*)
        - Installation Summary (*Ibid*)
      - Source Code (*Ibid*)
      - GitHub (*Ibid*)
      - Snapshots (*Ibid*)
    - Hyrax Server Documentation (*Ibid*)

### Edit The "Menu Setting" For The New Page

1.  Edit the **Menu Settings** for the new release page. Find them at
    the top of the right hand column in the page editor.
2.  Select the "**Provide a menu link**" check box. I
3.  Set the **Parent item** to "*----Hyrax*"
4.  Set the **Weight** to the weight of the previous release minus one.

This should ensure the that new page appears at the top of the list of
Hyrax releases in the side bar menus of the web site

### Follow the instructions for Updating The Release Page For A Minor Release below.

## Updating The Release Page For A Minor Release

### Update the New Features section

1.  Under "New Features" create a new sub-section (Heading 2) titled
    "Added in Hyrax-xx.yy.zz" for the new release. If this is a major
    release then zz==0, example: Hyrax-1.16.0
2.  Create an html anchor at the beginning of this new heading titled
    "*hyrax-.xx.yy.zz-features*".
3.  In the abbreviated table of contents at the top of the page add a
    new link in the bullet list under "New Features" titled
    "hyrax-xx.yy.zz" and point the link to your new anchor
    "#hyrax-xx.yy.zz-features"
4.  Take the entires that you made to the NEWS files for libdap, BES,
    and OLFs and enter them into this new section, format them for
    readability and navigability. (See previous releases for examples.)

### Update the Bugs/Issues Addressed section

1.  Create a new section for the release in the Bugs/Issue area.
2.  When the [Jira Release Process](Jira_Release_Process "wikilink") was
    completed you generated release page(s) for our one or both of our
    [Hyrax
    JIRA](https://opendap.atlassian.net/projects/HK?orderField=RANK&selectedItem=com.atlassian.jira.jira-projects-plugin%3Arelease-page&status=released)
    and/or our [NASA
    JIRA](https://bugs.earthdata.nasa.gov/projects/HYRAX?selectedItem=com.atlassian.jira.jira-projects-plugin:release-page&status=released).
    Locate the appropriate release page from each JIRA for this release
    and link them to the bugs/issueThese release pages should be
    referenced in a paragraph that says something like:

> **For Hyrax-1.16.5**:
>
> *Many bugs were fixed, and a lot of effort was put into continuous
> integration and testing. Rather than itemizing the tickets here, [if
> you want you can see all of the tickets we processed
> here](https://opendap.atlassian.net/projects/HK/versions/10116/tab/release-report-all-issues).*
>
> *We also worked extensively with NASA on their NGAP project. We
> processed 94 tickets during the release period. [If you have access to
> NASA's JIRA you can see the details
> here](https://bugs.earthdata.nasa.gov/projects/HYRAX/versions/26233).*

### Update the Hyrax Software Downloads section

In the *Hyrax Software Downloads* section we only provide links to the
most recent official release.

1.  Update all of the download links for the various products that were
    built and published to www.opendap.org

    Be sure to update the **gpg signature** links as well.
2.  Test each link - source and binary.

### Publish the release page

1.  Publish the release page.
2.  Check every link again!!!.

    (Seriously. Please take the time to do this. Check the on page
    navigation links, the external links, and the download links.
    Thanks...)