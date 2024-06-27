Goto the [Dataset List](Dataset_List "wikilink") now.

## About the Dataset List

This wiki-based dataset list is an attempt to harness the power of the
community of data providers to build and maintain a list of all OPeNDAP
data servers. All "OPeNDAP" servers can be listed here; this is not a
list for just Hyrax! If you are running a public server that DAP clients
can access, we would like you to list it here! Similarly, if you are
here looking for data and see a link that's broken or a site that is
missing links, please use the contact information to let someone at that
site know their information here needs to be updated. If all else fails,
drop us a line at *support at opendap.org*.

Goto the [Dataset List](Dataset_List "wikilink") now.

## Information to include with all datasets

### Contact

For each dataset you list, please include *contact* information! This is
the single most important piece of information from the standpoint of
keeping the list accurate and your work to a minimum. If you have
contact info bound to your datasets, then it's easy for people to get in
touch with you, which means they can help you keep your datasets
current. When you include this information, please try an use an email
address that won't go stale quickly!

### Info

The *info* link references a web page that describes your data. You can
organize this anyway you would like, however, be aware that this
provides often very important information to both people and web
crawlers (that build indexes like Google that people then us to find
your data) so it's potentially a very link important!

### A GCMD entry link

This link is similar to the *info* link in that it provides a general
description of a particular data source. Unlike *info* which is a (link
to a) web page, this is a link to a page/entry in NASA's Global Change
Master Directory (GCMD). The GCMD is a list of data sources where
information about each data source is monitored and maintained by
specialists in different research areas such as physical oceanography.
Include this information if you data set(s) have been cataloged by the
GCMD staff!

### Directory

The link labeled *dir* should point to a directory page for your server.
Usually when DAP is used to serve data, those data are stored in
collections of files that are stored on disk. The value of this link
should be the top directory in the file system that holds those files.
It's fine to list several links if that's how your data are organized.

### The HTML for interface

The *HTML* is not for a web page that describes the entire data server's
contents (use *info* for that) but instead is a link to the HTML form
interface that all DAP servers provide. This provides an easy way to
build URLs that access data. Hyrax servers provide ways to get data in
NetCDF files and as ASCII among other things. This might not be
appropriate for some really large sites (servers with many data sets)
because the page will get cluttered very quickly.

### Summary of suggested links

contact
email or other contact information for the person in charge of the
server.

info
A link to an HTML page that people can read (and crawlers can grovel).

gcmd
A link into the [Global Change Master Directory](http://gcmd.nasa.gov/)
that describes this dataset.

dir
A link to the directory page provided by the server.

html
A little confusing, this is not a page of static HTML but a form that a
person can fill in, see a DAP URL built up in response to changes in
that form, and then get data as binary, ASCII or a netCDF file. A useful
tool for exploring a dataset.

## Adding/editing an entry is easy

### A site with one server

If your site runs only one data server it's best to list each major
dataset under a heading named after you organization. Use a heading made
up of two equals signs like:

`== OPeNDAP's Test Server ==`

Under that heading, include an *info* link if you have a web page that
explains your server/site. This is a great way to help people looking
for data and a also an easy way to get web crawlers like Google to index
your site. If you prefix the link with a colon, the entry will be nicely
indented. Like this:

     : [http://test.opendap.org/ info]

For each major dataset that you want to list individually, prefix them
with an asterisk and then include *info*, *dir*, and other links as you
see fit. It's a good idea to choose a name that short but informative
(of course!). However, keep in mind that most people who use this list
will not be familiar with your specific data. Here's an example for some
of our test data:

    * Freeform Test Data: [http://test.opendap.org/dap/data/ff/ dir]

Note that we don't have an appropriate *info* page for this, it is a
simple directory listing, so we've just included a *dir* entry for the
data set (which is really a collection of things we use for testing
client applications). Here's a complete example and following it, the
resulting page as it appears in a browser:

     == OPeNDAP's Test Server ==
     : [http://test.opendap.org/ info]
     * Freeform Test Data: [http://test.opendap.org/dap/data/ff/ dir]
     * NetCDF Test Data: [http://test.opendap.org/dap/data/nc/ dir]
     * COADS Climatology Test Data: [http://test.opendap.org/dap/data/nc/coads_climatology.nc.html html]

And what it looks like in a browser:

------------------------------------------------------------------------

<font size="+1"> OPeNDAP's Test Server </font>


[info](http://test.opendap.org/)

- Freeform Test Data: [dir](http://test.opendap.org/dap/data/ff/)
- NetCDF Test Data: [dir](http://test.opendap.org/dap/data/nc/)
- COADS Climatology Test Data:
  [html](http://test.opendap.org/dap/data/nc/coads_climatology.nc.html)

------------------------------------------------------------------------

### Sites with more than one server use subheadings

If your organization runs several servers (and it's fine to list several
different kinds of servers like Hyrax, TDS, PyDAP, GDS, ..., here) then
use two headings, one for your organization and separate subheadings for
each server. To do this, make the organization entry using two equal
signs (*==*) and the serve entries using three (*===*). Here's a partial
example from NASA's JPL facility. Note that for clarity I added newlines
between the various links in the listing below. It looks more polished
if you leave those out in a real entry, but it makes the listing harder
to read on this page.

    == NASA/JPL - Physical Oceanography Distributed Active Archive Center (PODAAC) ==

    === SSM/I derived global heat and momentum fluxes (Atlas, Jusem, Ardizzone); Product #004 ===
    : [http://dods.jpl.nasa.gov/dods-bin/nph-hdf/pub/heat_flux/ssmi/data/ dir]
    [http://gcmd.gsfc.nasa.gov/getdif.htm?PODAAC_SSMI_Heat_Flux_004&interface=FROMDODS gcmd]
    [http://podaac.jpl.nasa.gov:2031/DATASET_DOCS/ssmi_flux.html info]
    === Ocean Wind ===
    * SSM/I Pathfinder gridded ocean wind speed level 3 (NOAA, NASA, MSFC, Wentz); Product #057:
    [http://dods.jpl.nasa.gov/dods-bin/nph-hdf/pub/ocean_wind/ssmi/msfc_pathfinder/level3/data/ dir]
    [http://gcmd.gsfc.nasa.gov/getdif.htm?PODAAC_SSMI_gridded_wind_057&interface=FROMDODS gcmd]
    [http://podaac.jpl.nasa.gov:2031/DATASET_DOCS/ssmi_pathfindr_wind.html info]
    * NSCAT scatterometer science product, levels 1.7, 2, 3 (JPL); Product #066:
    [http://dods.jpl.nasa.gov/dods-bin/nph-hdf/pub/ocean_wind/nscat/data/ dir]
    [http://gcmd.gsfc.nasa.gov/getdif.htm?JPL_PODAAC_NSCAT_Product_066&interface=FROMDODS gcmd]
    [http://podaac.jpl.nasa.gov:2031/DATASET_DOCS/nscat_nsp.html info]

Note that if you want, you can use the heading and subheading format
with one server - there's no rule against that - and, similarly, you can
use this in other ways to organize your data.