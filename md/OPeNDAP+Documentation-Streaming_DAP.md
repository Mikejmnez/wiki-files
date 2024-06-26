I think we should consider adding a section to the spec for a streaming
OPeNDAP server. There is a rapidly growing movement in the oceanographic
community around building ocean observatories. Growing along with this
movement is the idea that instruments are going to either get smarter,
or report to smart nodes. These more intelligent systems would provide
streams of data to one or more clients. The idea of offering a streaming
side to the spec would be to allow powerful clients (such as the ODC) to
attach to any instrument. The assumption here is that all time based
sampling (or state based sampling) instruments basically look like a
sequence that only ends when sampling terminates (either by command or
by loss of power or connectivity).

Consider the following scenario for a streaming server:

- A client asks for the DDX.
- The client _projects_ (and possibly _selects_) from the DDX and
  returns a request to the server.
- The returned DDX contains a URI pointer to the data blob. The data
  blob, in this example, is a stream. The client never really gets the
  entire blob, it keeps coming...

So the client just keeps getting more and more instances of the sequence
structure.

This interaction might be scripted in a number of ways, but I have been
thinking about as the client asking the server for a "stream
subscription" which gets returned in the bolbURL, or I suppose we could
even add an element to the data model, say the <streamURL> element.
*Nathan Potter - 26 Sep 2003*

Is it important to differentiate a BLOB from a stream? I can see that it
might be useful, but is it crucial to some clients operation? Suppose we
adopt a particular attribute (or tag, see ANewDataModel) that says this
variable is really a stream and not some finite thing already stored on
a computer. *James Gallagher - 01 Oct 2003*

I think so. The problem I see (and maybe you can comment) that we don't
establish a "connection" do we? How does a client tell there is more
data coming? Can an http url be a stream? Somehow we need to be able to
indicate to a client that the connection must be kept alive... *Nathan
Potter - 01 Oct 2003*

I don't think so. Sequences have an end of data marker. A client should
keep reading until that is found. If it wants to stop sooner, TCP/IP
should take care of letting the serer know the client has gone away. I
don't think we should label the BLOB as 'really a stream.' I think it's
better to label the variables. *James Gallagher - 01 Oct 2003*

One way to understand what you might be proposing is a "push" delivery
system, like the LDM. This can be thought of as a message passing
system. Depending on how reliable you want it to be, that can be hard to
do right. HTTP is a "pull" = request/response system.

Another possibility is analogous to streaming media. In that case,
there's a definite size of a resource to transfer, but the data is
displayed by the client as it arrives, not waiting for the entire
transfer. Its still a variation on a pull.

Somewhat related is an issue i'd like to see addressed. At least in the
Java server and client library code, the entire transfer is put into
memory before sending (server), and read entirely before returning
(client). this limits the size and flexibility of the transfer. I think
Joe W. made some mods to the server that got around that. I wonder how
hard it would be to get it into the trunk?

Another interesting feature would be for a client to be able to signal
to the server to stop sending a data stream.

Finally, an idea that I've harped on before: allow a client to ask for
the next n records in a sequence, with the ability to cancel the
transfer. *John Caron 03 Oct 2003*