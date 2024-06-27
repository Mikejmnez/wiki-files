## libdap code/features to remove

- In DODSFilter
  - ~~Remove 'COMPRESSION_FOR_SERVER3' code~~ Done.
  - ~~Remove the FILE\* methods - this will break some of the older 'cgi
    handlers' but those should be removed anyway.~~ Done
  - ~~CGI processing code in general - how much of this is being used at
    all?~~ Done
  - ~~read_ancillary_das() methods - replace with direct calls to
    Ancillary?~~ Done
  - ~~Look at the DODSFilter unit tests to make sure that some of the
    CGI code is not used there.~~ Done
- cgi-util.cc
  - ~~Remove the FILE\* functions and/or replace with ostream ones if
    those are not already there.~~ Mostly done - there are some FILE\*
    functions that we need. Renamed to mimeUtil.cc/h and change the
    cgiUtil.h file to include mimeUtil.h
- ~~AIS - remove completely~~ <font color="red">Done</font>
- ~~XDRFileMarshaller - We can remove this~~ It's still in the library,
  but aside from tests it is not used.
- XDRFileUnMarshaller - We can remove this once we have an
  XDRStreamUnMarshaller and...
- Look at Response, HTTPResponse, and see how they use FILE\*. Can this
  be changed to an ostream easily? More of a refactor...
  <font color="red">Well, no, not easily. The problem is that the
  flex-based scanners all use FILE\*s to read data and those buffer
  their reads. This complicates things a bit. Instead of a single thing
  to change, look below at the *libcurl* section</font>

## libdap code/features to refactor

- We need a XDRStreamUnMarshaller - this will let me remove lots of the
  FILE\* code. <font color="green">Update: I wrote this as a class that
  takes an istream and then wrote a streambuf that supports underflow -
  what you need to support an istream - and that takes file descriptor
  to be used an an input source. the new streambuf classes work, but
  when used with my XDRStreamUnMarshaller that class does not work. The
  XDRStreamUnMarshaller *does* seem to work with ifstream
  instances.</font> The importance of this is that if we're going to
  have a data payload that is chunked, and we have to do that to
  implement reliable error responses, then we need control over the
  stream and we don't get that with the FILE\* XDR interface.
  <font color="red">We could build a FILE\* XDR interface that processes
  chunked data, however.</font>
- To completely replace FILE\* with C++ streams, we will need to:
  - Replace FILE\* in the flex scanners and in the DDX parser although
    the latter will not be hard.
  - change how we use libcurl so that we get back a file descriptor
  - Complete the XDRStreamUnMarshaller code

<!-- -->

- Change HTTPConnect so that it works with data in memory when possible.
  That is, stop writing the data from libcurl to a file and instead pass
  libcurl a pipe for it to dump the data into. I think for this to work
  we need to fork() or use threads. In the later case, will the code run
  on separate cores?
- Modify the XDRStreamMarshaller so that it works with data using
  smaller buffers. The vector code first allocates a buffer to hold the
  whole vector, copies the binary data to that buffer, then encodes
  that, sends it and then frees the buffer. The upshot is that for a
  large variable, the entire thing to send is in memory twice. We could
  use a smaller buffer and encode sections.
- We can adopt a multithreaded reader for the client side (I have my
  doubts about the utility of this since most clients are now using Java
  or C).

<img src="Connect_synchronous_processing.png"
title="Connect_synchronous_processing.png" width="300"
alt="Connect_synchronous_processing.png" />
<img src="Connect_proposed_asynchronous_processing.png"
title="Connect_proposed_asynchronous_processing.png" width="300"
alt="Connect_proposed_asynchronous_processing.png" />