[\<\< Back to OPULS Development](OPULS_Development "wikilink")

\[Updated: 3/5/2013 to reflect conversation with Ethan. See discussion
section.\]

## Background

Currently, the specification says that checksums are computed on the
contents of the top-level variables in the serialized response. I
propose that we change when and where the checksum is computed so that
it is computed at the grain of the data chunks instead of at the grain
of variable.

## Problem Addressed

Checksumming currently requires knowledge of the DMR (and potentially
any constraints) so that the contents of "top-level" variables can be
identified in the data serialization. Further, checksum errors are not
detected until the whole variable has been read.

## Proposed Solution

### General Format

Each chunk, including the DMR meta-data chunk, has a 16 byte MD5
checksum preceding the contents of the chunk, but following the length
count for the chunk. Thus, the general form of a chunk would be as
follows:

    --------------------------
    | length + tags [4bytes] |
    --------------------------
    | checksum [16 bytes]    |
    --------------------------
    | data [length-16 bytes] |
    --------------------------

The checksum is computed over the contents of the chunk, where the
content is treated as uninterpreted bytes. This would also apply to the
DMR chunk. The length field for each chunk includes the checksum as part
of its length.

### DMR Format

In keeping with our current format for the DMR chunk, all parts of this
chunk are encoded as UTF-8 characters So the general DMR chunk format
would be as follows

    ------------------------------------------
    | 0xHHHHHHHH CRLF                         |
    ------------------------------------------
    | 0xHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHH CRLF |
    ------------------------------------------
    | DMR in XML format CRLF                  |
    ------------------------------------------

Each 'H' stands for a hex digit. As in the general case, the length
includes the checksum and the chunk content (i.e. the DMR).

## Discussion

Computing the checksum at the chunk level simplifies processing because
no knowledge of the DMR is required. The computation can be carried out
completely in the chunk reading and writing code.

Additionally, computing the checksum at the chunk level can potentially
detect errors more quickly than at the variable level. To see this,
suppose that a variable crosses, say, three chunks, and there is an
error in the first chunk. If variable level grain is used, three chunks
will be read an processed before the checksum error is detected. If
chunk grain is used, then the error will be detected as soon as the
first chunk is read; the following two chunks need not be processed.

This proposal also introduces no new error processing complexity because
a checksum error at the chunk level will generate an IOException error
to the layers above. These layers must already be prepared to receive
IOException errors such as when, for example, the client-server link is
unexpectedly closed.

[Dennis](User:dmh "wikilink")

> It was my understanding that checksums were introduced into the
> specification not as a mechanism for insuring error free data
> transmittal (although that is a side benefit) but as a mechanism
> through which clients can detect changes (or the lack thereof) in data
> content. For example a data provider might reprocess their data
> holdings because more refined algorithms have become available, but
> not all variables in the dataset may be affected (thus the
> last-modified time of the holding is not necessarily the best
> indicator, at the variable level, of what's going on). If my
> understanding is accurate I'm thinking that this proposal fails to
> meet the requirements regarding change tracking at the variable level.
> If I am wrong, and checksums are all about error free transport, then
> at first read I think that this is a reasonable change.
> [ndp](User:Ndp "wikilink") 15:44, 1 March 2013 (PST)

Update: 5/3/2013 [Dennis](User:dmh "wikilink")
Nathan, you are correct. Ethan made a similar comment. However, in order
to make this usable, we need to provide a standard mechanism by which
the client can access the checksum. I propose that we add a specially
named attribute in the DMR that holds the checksum value for a variable.
I propose that the attribute be named "_<checksum algorithm>". So if we
are using MD5, the attribute is called "_MD5" \[see next update\].

The question now becomes: do we need to add any kind of error checking
checksumming or do rely on network reliability. Note that using a
checksum only for change detection does not require the client to
actually recompute the checksum; it only needs to display it to the
user's code (via e.g. an attribute).

Update: 5/3/2013 [Dennis](User:dmh "wikilink")
It is not obvious why we should be defaulting to MD5 as the checksum
algorithm. If we were doing this to avoid man-in-the-middle spoofing, we
should be using SHA1. If our goal is change detection (or even error
detection), then we can get by with, say, CRC32, which is both smaller
and faster than MD5. The corresponding attribute (see previous update)
would be "_CRC32".

[Jimg](User:Jimg "wikilink") 11:51, 6 March 2013 (PST)
The original use case was *change detection* and I think that should
remain the primary focus. If we can get additional functionality out of
the checksum without additional cost, that would be great. Thus using
CRC32 would be the best choice (I used MD5 because it was the first hash
algorithm that came to mind, and it was easy to use from a library
that's on both Linux and OS/X). Faster is better! And I like very much
the idea of adding an attribute for the information, because it is
important that the information be available to clients/users. So lets
make these changes to the specification.

NB: In our discussions added *error detection,* but I'm not sure that's
a high priority, and if the CRC code is present as an attribute, then
clients can use it for error detection if their developers want to code
that.

--[EthanDavis](User:EthanDavis "wikilink") 15:27, 6 March 2013 (PST)

What is the granularity of change detection we want to support?

Given the cross-server aspect of the [original use
case](DAP4:_Checksum#Use_cases "wikilink"), I understand the use of
checksums in the [requirements
list](DAP4:_Checksum#Requirements "wikilink"). But why is per variable
checksums included? I'm not seeing variable level change detection
mentioned in the use case.

Sure, it would be cool if those who want could figure out exactly which
variable had changed. But is that really something that will be widely
used?

Perhaps we should restrict this discussion to dataset level change
detection. And postpone it for DAP4 objects below the dataset level.

--[EthanDavis](User:EthanDavis "wikilink") 15:38, 6 March 2013 (PST)
By the way, what bytes are we going to use to compute a checksum? The
files on disk, the bytes of an array in memory, the DAP4 encoded
objects? Or should we leave that up to each server and server site
configuration to decide?

[Dennis](User:dmh "wikilink")
To answer Ethan's questions, The spec will say this:

> Checksums will be computed over the serialized data for each of the
> variables at the top-level of each Group in the response. The checksum
> value will follow the data of the variable in the serialization. The
> checksum algorithm defaults to CRC32. Checksum values will be written
> as 32-bit values using the endian representation specified for the
> serialized form.

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")