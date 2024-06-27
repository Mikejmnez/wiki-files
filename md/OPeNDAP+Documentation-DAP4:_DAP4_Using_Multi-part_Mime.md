[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

As a result of a comment in the OPULS/NOAA telecon, I think we should
reconsider using multi-part mime in a limited fashion in our
serialization. Basically I propose to resurrect James' original proposal
(see [DAP 4.0 Design](DAP_4.0_Design "wikilink")).

## Proposal

As with the original proposal, the multipart-mime has two parts. The
first is for the DMR and the second is for the whole, chunked serialized
data. The general form would look something like this.

    Content-Type: multipart/related; \
                  type="application/vnd.org.opendap.dap4.dataset-metadata+xml"; \
                  start="<<start id>>"; boundary="<<boundary>>"
    --<<boundary>>
    Content-Type: text/xml; charset=UTF-8
    Content-Id: <<start id>>
    Content-Length: ddddd
    <<blank line>>
    <<DMR text>>
    --<<boundary>>
    Content-Type: application/octet-stream
    Content-Encoding: big-endian
    Content-Length: -1
    <<blank-line>
    <<chunked representation of the serialized data>>
    --<<boundary>>

Notes:

1.  Every header line should be terminated with CRLF. The
    \<<blankline>\> is also terminated with CRLF.
2.  The above representation assumes that the DMR is not in chunked
    format.
3.  The Content-Length value for the DMR counts the number of UTF-8
    characters from the end of the blank line to the initial "--" of the
    boundary line.
4.  The chunked representation is still in our existing chunked format.
5.  I do not know if the start-id is required by the multipart-mime
    format. If not, then we should remove it.
6.  I am not sure about the "type" param of the "Content-Type" line at
    the beginning.

[Dennis Heimbigner](User:dmh "wikilink") Initial draft 3/19/2013

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")