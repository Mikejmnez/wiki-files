I'd like to use this topic to discuss the abstract data model, as
suggested by John Caron. I'll try to get some UML here for what I think
the ADM should be.

John (and anyone else), can you add UML for other data models like
netCDF and HDF5? What I'm concerned about is making sure that our model
has a way to represent the concepts present in HDF4/5 and netCDF
reasonably well. I'd still like to pursue the goal of having HDF4 and
HDF5 client libraries... James Gallagher - 03 Oct 2003

## DAP's ADM

<figure>
<img src="DAP_4_ADM.jpg‎" title="DAP_4_ADM.jpg‎" />
<figcaption>DAP_4_ADM.jpg‎</figcaption>
</figure>

Notes:

- I used aggregation for Value because it seems those have a lifetime
  that is, in the abstract sense, different from the lifetime of a
  variable in the DAP. I used 0..\* because there are times when a
  variable has no value.
- I wonder about making Attribute use Type...
- I'm not totally comfortable with UML, so make any changes you want. I
  used Poseidon to draw the figures; I can put the stuff in CVS if you'd
  like.

## NetCDF Data Models

Ok, Im attaching a recent UML diagram of both the current model and a
draft of a new model as we merge/munge/mangle with HDF5. John Caron - 03
Oct 2003

<figure>
<img src="Netcdf1.jpg" title="Netcdf1.jpg" />
<figcaption>Netcdf1.jpg</figcaption>
</figure>

<figure>
<img src="Netcdf4.jpg" title="Netcdf4.jpg" />
<figcaption>Netcdf4.jpg</figcaption>
</figure>

## Sequences and Selection

I see sequences as a nice addition that will allow variable length
arrays to be efficiently represented. Allowing them to be selected in
all cases seems to me to be problematic. If I have a large sequence, i
cannot efficiently implement the equivalent of a SQL WHERE clause,
unless i have built an index. It seems to me that this ability is an
additional capability similar to a function, which should only be
allowable if its advertised in the capability object. John Caron - 16
Oct 2003