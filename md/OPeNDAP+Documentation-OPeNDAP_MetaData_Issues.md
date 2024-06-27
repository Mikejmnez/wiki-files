In the last few days it has become clear (at least to me) that the way
we choose to handle meta-data in the DAP 4 is going to dictate a large
amount of the resulting specification. I offer these questions as a
starting point. (ndp)

### Do we wish to continue to isolate the meta-data (Attributes) from the data (Variables)?

We have had a lot of success keeping the two data realms separate (in
separate documents even). However, it has been observed that the
division is somewhat artificial, and James has pointed out that HDF 5
makes no such distinction, at least at the data type level. In this
situation it is possible that the meta-data components be very large,
say a gigabyte or more.

(I've looked over some HDF5 documents and it seems that while the set of
datatypes for 'variables' and attributes are the same, this is mostly a
convenience of implementation. In the documentation I observed that the
HDF5 designers view attributes as 'small.' Here's the [User's
Guide](http://hdf.ncsa.uiuc.edu/HDF5/doc/UG/) for HDF5. Look at the
section on attributes. James Gallagher - 29 Sep 2003)
(I just read the HDF5 docs you referenced. Wow. It looks like they are
almost using Attributes the same way that we are. Is there any chance
that they read our documents?? Nathan Potter - 29 Sept 2003)

(HDF5 Attributes are supposed to be "small", also attributes cannot have
attributes. John Caron - Oct 1 2003)

This immediately brings to light some key issues:

#### Should we be sending all of the Attributes (meta-data) with every DDX?

Our current test implementation of the DAP4 does this, but that was a
by-product of couple of factors:

1.  I needed to tightly couple the meta-data and the data, so I devised
    a way to put them into the same document.

In doing so I discovered that:

1.  The original DAP specification had a rule for the correct
    construction of the DAS. This rule was never enforced at any level,

in particular at the software level. In fact we wrote code that
attempted to combine a DAS and a DDS object by making "educated guesses"
about the intent of the individual who wrote the DAS while ignoring the
content of the spec. OW! If we had written code that had used the rules
in the DAP spec to combine them people might have actually paid some
attention to the spec. Ultimately, we fell prey to a narrow
interpretation of of our rallying cry: There are no requirements for
meta-data! The truth should have been (and actually was): There are no
requirements that users *provide* meta-data, but if they do, there are
rules about how it needs to be organized to be useful.
I believe that we could return to a data model that keeps them decoupled
(in separate documents), IFF we make it clear in the spec that they must
have compatible structures and we implement code that demands that the
two documents have compatible structures.

#### Should the meta-data and the data should be based on the same set of types, and the metadata-ness or data-ness of a particular instance of a type be simply a quality (attribute) of the instance?

Making them the same could mean an pretty serious refactoring of our
core software. The changes might produce a very interesting data model:
What if all the information was based on the same set of types, in the
same document? You could project anything and select on anything.
Aliases would always point to "variables". The tricky part is how to
organize the information. I will assume that we still want to be able to
tie Attributes to variables. This would make every single type a
container, a structure. The atomic types could only hold variables that
were labeled as attributes. The constructor types could hold variables
that were labeled as either attributes or variables. The distinction
between variables and attributes would be nearly moot. Now, having said
that I can imagine that this would have a really profound effect on
serialization. The strength of the current system is that we don't send
data in a redundant manner. Currently the metadata is sent to a client
only when specifically requested. Sending it with the data could mean
inflation issues: The prototype DDX sends the Attributes and their
values with every DDX request. As I contemplate this I think it is a
somewhat evil thing. Certainly if the size of the Attributes is large,
than sending them (with each DDX) is of questionable value. In the model
I just outlined I guess I imagine that since all the types are the same
(with only a tag to indicate their metadata-ness) then they would get
serialized all together. This could be really bad for Sequence types
where the same meta-data values might apply to 100000 "rows" or
instances of the Sequence. We certainly don't want to send the meta-data
100000 times! That seems like a completely evil thing. Any ideas here?
Or am I charging down a blind alley? Nathan Potter - 24 Sept 2003

#### Do we want to allow users to apply constraints to the meta-data? If so in what way?

The current implementation of the DDX (with combined DAS/DDS content)
provides one possible model for this: It will constrain the meta-data
based on which variables are projected. If a variable is projected, then
it's Attributes are sent. The only attributes that are sent with every
DDX are the global (dataset) Attributes.

#### Do we want to allow "variable" Aliases to point to "attributes"? Vice-versa? Another way to say this is: Do we want to allow Aliases to cross the data / meta-data boundary?

I think this is a pretty serious question. I think that a yes answer
here almost certainly forces specific answers in the previous questions,
in particular a yes here would demand:

1.  meta-data and data be based on the same set of types.
2.  meta-data be constrain-able in some way
3.  we don't send meta-data unless they ask for it.
4.  we **must** have a persistent representation of the data set that
    combines the meta-data and the data (like the current prototype DDX)

Does anyone else see this in the same way?? Nathan Potter - 22 Sep 2003

There are some real differences between data and metadata, despite the
fact that you might want to use the same or similar sets of data types
to represent the two. Its quite important that the meta-data be
relatively small and efficient to retrieve. The idea is that you want to
present to the user a "logical view" of a dataset, so that they can
decide what to do. That logical view should include metadata, and it
should be efficient, so metadata needs to be small.

Conversely, when the user is reading the data, the metadata is not
needed, since any useful client would cache it. So I'd like to see one
call to get all of the DDS/DAS (now called the DDX I guess?). Then the
call to get the data should be logically separate, and need only return
the data requested, not the metadata.

I don't see any need for metadata to get arbitrarily complicated, eg im
not much convinced of the value of nested attributes. KISS unless theres
a compelling use case. So i think keeping attributes and variables
separate is a good idea. John Caron - Oct 1, 2003

I agree with what you (both, I think) are saying here. That is:

*Conversely, when the user is reading the data, the metadata is not
needed, since any useful client would cache it. So I'd like to see one
call to get all of the DDS/DAS (now called the DDX I guess?). Then the
call to get the data should be logically seperate, and need only return
the data requested, not the metadata.*

However, what I'd like to do is hold off on deciding how we package the
[Abstract Model](Abstract_Model "wikilink") elements and work on getting
a model that expresses the stuff we need. Then we should think about how
that will be used and let the use case(s) drive the packaging. James
Gallagher - 03 Oct 2003