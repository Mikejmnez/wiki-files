We're going to have to structure the inside of a DAPBLOB object/response
so that while a server is writing the object, it can report an error,
dump the ErrorX into the DAPBLOB and a client can be sure it'll get the
ErrorX.

One way to do this is 'chunk' the data in the DAPBLOB. In this scheme,
the DAPBLOB announces its chunk size at the beginning (using a MIME
header?) and guarantees that the client can read at least that many
bytes. If an error is found while preparing the next chunk, write an
ErrorX, else write the next chunk of data. Repeat until done. If the
first chunk contains an error, respond with an ErrorX and not a DAPBLOB.

Does MIME support chunked messages? No (James Gallagher - 26 Sep 2003)

I also think that the DAPBLOB should contain a reference to the DDX URL
which describes the types the DAPBLOB contains. A back pointer of
sorts... James Gallagher - 19 Sep 2003

I've spent a little time poking around and I think the best way to
handle this is to put the BLOB in a MIME document and make that document
have a `Content-Type` of `multipart/mixed` (see [RFC
2046](http://www.faqs.org/rfcs/rfc2046.html), Sec 5). Each 'chunk' of
binary data would be enclosed in a `boundary` section which could have a
header indicating that it is `application/octet-stream` information. We
can include other headers (such as `Content-Description`) which would be
used to tell if the stuff a given part was more binary data or an ErrorX
object. Note that parts in a multipart MIME document don't *have* to
include headers, but they *can*. James Gallagher - 26 Sep 2003

This seems like an HTTP specific solution. Is that what we want? Nathan
Potter - 26 Sep 2003

I don't think it is HTTP specific. We move stuff around inside MIME
documents. Those documents can be moved by just about anything. I don't
think we should buy into the 7-bit clean stuff, though... James
Gallagher - 26 Sep 2003