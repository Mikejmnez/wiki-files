[back](ReleaseSprintNotes "wikilink")

This is a work in progress... (jhrg 12/05/18)

## OPeNDAP JIRA

### Get all the closed tickets for this release

<figure>
<img src="Screenshot_2018-12-05_15.36.01.png"
title="Screenshot_2018-12-05_15.36.01.png" width="200" />
<figcaption>Screenshot_2018-12-05_15.36.01.png</figcaption>
</figure>

Get closed tickets, numbers, et c., from Jira. Go to the Issues and
Filters page and Look at recently closed issues. Edit the query by
clicking on the 'Advanced' link at the upper right, and then get the
list of issues as an Excel/CSV file. This makes the issues, their
obscure ticket numbers, et c., easy to copy and paste into the release
web page.

These steps worked when were not also using Jira for our 'Build
releases,' but now that we are, editing the 'fixVersion' makes those
more work and does not seem to add much to the 'releases for people
outside of NGAP' since they cannot see the 'NASA Jira' and thus have no
way of knowing about this change.

Let's stop making this change unless/until a way to do so that works
with the Build Releases presents itself.

## Don't Do this <s>Update the Release Version</s>

<figure>
<img src="Screenshot_2018-12-05_15.16.27.png"
title="Screenshot_2018-12-05_15.16.27.png" width="200" />
<figcaption>Screenshot_2018-12-05_15.16.27.png</figcaption>
</figure>

Add a new 'Release version' on the Jira 'Releases' page. Then 'release'
the current version, moving all unclosed tickets up to the next logical
version.

## Don't do this <s>NASA JIRA</s>

1.  Go to the [Hyrax JIRA page at
    NASA](https://bugs.earthdata.nasa.gov/secure/RapidBoard.jspa?rapidView=901&projectKey=HYRAX&view=planning.nodetail&issueLimit=100).
2.  Find the Version for the pending Hyrax release. If you can only find
    the Version for a previous release make a new Version title
    Hyrax-<numbers> for the new release.
3.  Locate the tickets closed since the last Hyrax release date. You can
    easily find this out by using the [saved issue filter "Closed
    Since"](https://bugs.earthdata.nasa.gov/issues/?filter=23327). You
    may have to edit the filter so the "since date" reflects the date of
    the previous Hyrax release.
4.  Examine each ticket to determine if the work done resulted in a
    change to Hyrax or to a change to some part of the NGAP stack. Add
    the Hyrax Version tag to the tickets that have become part of the
    publicly available Hyrax code. Do not include changes that are
    associated with NGAP CI/CD, NGAP deployments, or NGAP Cloud
    provisioning. The idea is to only tag changes that were made to
    Hyrax that took place in our GitHub repository and that changed the
    server that we release publicly. In theory this process should be
    happening as we go along in the NGAP JIRA process, so this step
    should really be a "sweep" to make sure nothing important was
    missed.
5.  When this has been completed you can go to the Hyrax Version page
    and make it a Release.
6.  Copy the URL for the release page for inclusion in the Hyrax release
    page on www.opendap.org