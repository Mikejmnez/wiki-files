<h2>

<span class="editsection">\[\<ahref="/index.php?title=QuickStart&action=edit&section=5"title="Edit
section: Sequence Data"\>edit</a>\]</span> <span
class="mw-headline">Sequence Data</span>

</h2>

> ` `<font size="+1"><b>`Note`</b>`: We're currently updating our documentation. The`
> ` information in this section references old demonstration servers we used to`
> ` host at the University of Rhode Island which are no longer running. Please`
> ` bear with us while we update this Guide. Thanks. 10/31/08 `</font>

Gridded data works well for satellite images, model data, and data
compilations such as the Reynolds data we've just looked at. Other data,
such as data measured at a specific site, is not so readily stored in
that form. OPeNDAP provides a data type called a Sequence to store this
kind of data.


A Sequence can be thought of as a relational data table, with each
column representing a different data value, and each row representing a
different data "instance." For example, an ocean {temperature profile}
can be stored as a Sequence of pressure and temperature pairs, and a
weather station's data can be stored as a Sequence with time in one
column, and each weather variable occupying another column.

Let's look at a set of two Sequences. The first gives the locations of a
set of inverted echo sounders, and the second gives their observations
over time. The data is here:

    http://dods.gso.uri.edu/cgi-bin/nph-dods/dods/ies/iess/ies_locations.dat

    http://dods.gso.uri.edu/cgi-bin/nph-dods/dods/ies/iess/iess.dat

First, let's take a look at the data structure by appending <b>.das</b>
to the URLs for the data, like this:

    http://dods.gso.uri.edu/cgi-bin/nph-dods/dods/ies/ies_locations.dat.das

    http://dods.gso.uri.edu/cgi-bin/nph-dods/dods/ies/iess.dat.das

The results look like this:

<figure>
<img src="Iess_location_das.png" title="Iess_location_das.png"
width="500" />
<figcaption>Iess_location_das.png</figcaption>
</figure>

and like this:

<figure>
<img src="Iess_dat_das.png" title="Iess_dat_das.png" width="500" />
<figcaption>Iess_dat_das.png</figcaption>
</figure>

We can get more information by looking at the DDS:

    http://dods.gso.uri.edu/cgi-bin/nph-dods/dods/ies/ies_locations.dat.dds

<figure>
<img src="Iess_location_dds.png" title="Iess_location_dds.png"
width="500" />
<figcaption>Iess_location_dds.png</figcaption>
</figure>

    http://dods.gso.uri.edu/cgi-bin/nph-dods/dods/ies/iess.dat.dds

<figure>
<img src="Iess_dat_dds.png" title="Iess_dat_dds.png" width="500" />
<figcaption>Iess_dat_dds.png</figcaption>
</figure>

Now let's take a look at the actual data:

    http://dods.gso.uri.edu/cgi-bin/nph-dods/dods/ies/iess.dat.asc

<figure>
<img src="Iess_dat_asc.png" title="Iess_dat_asc.png" width="500" />
<figcaption>Iess_dat_asc.png</figcaption>
</figure>

We see that some sampling locations didn't produce interesting data
right away. So let's restrict our query to the last few sampling
locations, by adding a constraint to the URL:

    ../dods/ies/iess.dat.asc?data_in.dyear,data_in.day,data_in.AET_inst08,data_in.AET_inst09,data_in.AET_inst10