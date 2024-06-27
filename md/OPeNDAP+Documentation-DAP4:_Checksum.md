<font size="+1" color="red">This is an old document that captures the
starting point of the OPULS design work. It's out of date and should be
referenced only as a baseline for the work.</font>

[\<-- back to OPULS Development](OPULS_Development "wikilink")

Author: [Jimg](User:Jimg "wikilink")

## Checksum

### Use cases

From Peter Cornillon (2012-01-19 17:40): Suppose I downloaded 50,000 SST
fields from an archive and performed operations on them to obtain some
scientific result. Six months later, I am about to publish but find that
there is a new version of the data set. It would be great if I could
simply download the checksum for the portion of the SST archive that I
downloaded before - the same OPeNDAP request except for the checksum
only to see what part of the archive has changed and if it is likely to
affect my results. Or the data have moved to another server and I want
to make sure that they are same when referencing them in my paper. This
is a scenario that I encounter fairly often in thework that I am doing.

A use case that I thought of when first thinking about this stuff a
number of years ago is a nuclear sub driving around the ocean,
underwater as subs are wont to do. When the sub leaves port it has the
most up-to-date version of global bathymetry available. Because these
guys are cool, they use OPeNDAP to access the data from their archive on
the sub. The sub is moving into a new area and the commander gets
whoever does the petty sort of stuff to request bathymetry for the
region. The request returns several gigabytes. A checksum is returned
with the data. The sub has a small window to connect to the outside
world but can only send a small number of bytes - the captain wants to
minimize its exposure. He wants to know if the bathymetry has changed,
so his petty officer guy makes the same OPeNDAP request to bathymetry
central but only asks for the checksum to be returned. If it is
different, they all sit around trying to figger out what the next step
is. If it is the same they are all very happy.

From Frew (Jan 20, 2012, at 12:46): Interesting. I see 2 use cases
lurking in here:

1\) **server(DAP_request(old_dataset)) ?=
server(DAP_request(new_dataset))**

Cornillon: I'm not sure that this captures the problem exactly. I think
that I would have phrased it as: **old_server(DAP_request(old_dataset))
?= ?_server(DAP_request(?_dataset))**. The problem is that the user
may not know if the dataset has changed; i.e., whether or not it is a
new_dataset or the old_dataset nor whether or not the server has
changed. Suppose that I downloaded some data from site X last summer and
found some very interesting results. Now I want to publish them, but
want to be sure that someone else making the same request will get the
same data. Specifically, if I make the same request from site X now will
I get the same response? The server may have changed or the dataset may
have been updated, but this may not be obvious in the request URL.
Another case that happens quite frequently is that the data have been
moved to another disk drive or another computer so that part of the URL
has changed. Is the DAP response from the server, with the new address,
the same?

This is potentially solvable with etags. It's up to the server to decide
whether the datasets are equivalent with respect to the specific
request. This allows for the server operator to tweak things in order to
solve the scientific equivalence problem.

2\) **old_server(DAP_request(dataset)) ?=
new_server(DAP_request(dataset)**

This isn't the same as (1), since in this case the etags must be unique
**across servers**, which is not how they're currently defined (at
least, as far as I can figure out from the HTTP spec.) The consequence
is that the etags would have to be determined entirely by calculations
on the data---you couldn't use site-specific configuration info to solve
the scientific equivalence problem without also requiring that
information to travel with the dataset.

### Requirements

1.  The checksum for an object (variable, subset, metadata, ...) will
    always be returned
2.  It must be possible to ask for the checksum only. (That is, to get
    the checksum that would have been returned with a given response).
3.  Metadata must have checksums
4.  Data must have checksums - the latter will be split according to DAP
    variables, with a separate checksum for each discrete variable in
    the DDS associated with a response.
5.  The checksums must be invariant with respect to server or URL; the
    checksum for two identical *data values* in a data set **must** be
    identical. The checksum must not vary depending on the 'packaging'
    of the data, so that the checksums are invariant WRT changes in the
    protocol used to transport the data.
6.  This is a feature of DAP4, not DAP2.

#### Performance tests

Based on [some simple
tests](https://docs.google.com/a/opendap.org/spreadsheet/ccc?key=0Apek_Ri3_W0WdEk2X2g1YkdESU1scHhkdU5oVXlaMGc&authkey=CNqAlIEC&hl=en_US&authkey=CNqAlIEC#gid=0),
checksums do not seem to be too expensive. Note that these tests use the
BES alone; it's likely that there will be little or no noticeable
difference for web accesses of HTTP.

### Design

#### Data

For every variable, that variable's checksum will follow the serialized
XDR bytes. For MD5, the checksum is 32 hex digits. The XDR encoding of
each variable will be extended in DAP4 so that the XDR encoded bytes are
terminated by a newline. The next line will contain the checksum, as a
hexadecimal number represented in ASCII, which will also be terminated
by a newline.

When checksums are computed for a variable, care should be taken to not
include any bytes added for padding or any 'length bytes' used to prefix
the XDR encoded data. Similarly, for Sequences, the sentinel bytes
should not be included in the checksum computation.

> The guiding idea is that *only* data values included in the response
> should be used in the computation of the checksum and not that's an
> artifact of the encoding.

Variables are defined as anything for which
libdap::BaseType::serialize() is called *except* Structure and Grid. For
those types, each component has its checksum computed separately. *(we
will have to edit this later to remove the reference to libdap)*

#### Metadata

*How should the checksum for the DDX be computed?*

### Implementation (libdap)

I added three methods to XDRStreamMarshaller:

virtual void reset_checksum(): call before starting on the next variable
virtual string get_checksum(): call to get the computed checksum
virtual void checksum_update(const void \*data, unsigned long len): add the data to checksum currently being computed

I did not modify any of the other *Marshaller* or *UnMarshaller*
classes.

One approach would be to define two abstract classes: *Checksum* and
*ReadChecksum* and change classes like XDRStreamMarshaller to inherit
from both Marshaller and Checksum. (This trink be done using an
interface in Java.)

Also in libdap, I modified *ResponseBuilder::dataset_constraint*,
*Structure::serialize()*, *Grid::serialize()*. The code is activated
using the compile-time switch *CHECKSUMS*. Here's an example of the code
used for testing which indicates it should be easy to make this a
soft-switch selected feature:

``` cpp
void ResponseBuilder::dataset_constraint(ostream &out, DDS & dds, ConstraintEvaluator & eval, bool ce_eval) const
{
    // send constrained DDS
    dds.print_constrained(out);
    out << "Data:\n";
    out << flush;
#ifdef CHECKSUMS
    // Grab a stream that encodes using XDR.
    XDRStreamMarshaller m(out, true, false);
#else
    XDRStreamMarshaller m(out);
#endif
    try {
        // Send all variables in the current projection (send_p())
        for (DDS::Vars_iter i = dds.var_begin(); i != dds.var_end(); i++)
            if ((*i)->send_p()) {
                DBG(cerr << "Sending " << (*i)->name() << endl);
#ifdef CHECKSUMS
                if ((*i)->type() != dods_structure_c && (*i)->type() != dods_grid_c)
                    m.reset_checksum();

                (*i)->serialize(eval, dds, m, ce_eval);

                if ((*i)->type() != dods_structure_c && (*i)->type() != dods_grid_c)
                    //cerr << (*i)->name() << ": " <<
                    m.get_checksum(); //<< endl;
#else
                (*i)->serialize(eval, dds, m, ce_eval);
#endif
            }
    }
    catch (Error & e) {
        throw;
    }
}
```

I did not test changes to read the checksum from the stream.

I did not test computing a checksum for the DDX response.

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")