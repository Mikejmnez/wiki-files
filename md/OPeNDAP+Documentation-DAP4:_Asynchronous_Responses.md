[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Background

Sometimes servers are unable to respond to a clients request for
(meta)data in a timely manner (timely could be defined as the maximum
time that a user will normally wait or the time it takes for the tcp
connection to time out, or something else). The primary reasons that we
imagine are data centers that hold data in near-line storage, and
servers that are going to perform a complex computation on large dataset
in order to build the response. We need a mechanism through which we can
tell the client that the response will be delayed, and to return to
particular place after a particular time (or during a particular period)
to retrieve the requested information. The key thing is that the server
can figure it out, but clients don't get much choice.

## Problems Addressed

Provides a mechanism through which the server may instruct the client
that the response will be delayed, and how to access the response when
it becomes available.

## Proposal

The "story" goes something like this:

- Server gets a Request
- Something in the server evaluates the request and determines if it has
  to be an async response. A pseudo-code example might look like:

<font size="2">

``` java
 if ( ( fileSize>ITS_A_NEAR_LINE_FILE  &&  serverProcessing==REPROJECT_THE_WORLD) ||
       fileOnTape)
      isAsyncResponse = true;
```

</font>


Whatever test makes sense…

- If the response is going to be asynchronous, the server returns a
  specialization of the Dataset response like this:

<font size="2">

``` xml
<Dataset xml:base="balahblahblah">
   <async
       xlink:href="http://host.server.gov/opendap/tmp/file.h5.xml" >
       <accessDelay>60000</accessDelay>
       <accessDuration>3600</accessDuration>
   </async>
</Dataset>
```

</font>


And similarly for the Data response:

<font size="2">

``` xml
<Dataset xml:base="balahblahblah">
   <async
       xlink:href="http://host.server.gov/opendap/tmp/file.h5.dap" >
       <beginAccess>2012-11-05T13:15:30Z</beginAccess>
       <endAccess>2012-11-05T14:15:30Z</endAccess>
   </async>
</Dataset>
```

</font>

- **Notes:**
  - ~~The Dublin Core specification supports both simple dates and
    ranges, so the <dc:date/> element can be use to indicate when the
    response might/will be available as well as when an ephemeral
    response might be removed.~~The beginAccess element specifies when
    the data set will be available for access, and the endAccess
    specifies when the data set may no longer be available. Both of
    these times are in ISO 8601 date format.
  - In the DAP4 Dataset and Data responses returned from the
    <font size="2">`xlink:href`</font> in these examples, the value of
    <font size="2">`xml:base`</font> should be the same as the original
    request: <font size="2">`xml:base="balahblahblah"`</font>. In other
    words, the value of the <font size="2">`xml:base`</font> should be
    the URL that references the original dataset and not the URL to the
    response object.
  - The staged dataset must maintain the same authentication and
    authorization controls of the original data source.

### Regarding the use of HTTP headers

When the transport is HTTP the server should return a 202 *Accepted*
(http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3)
response with a *Location:* header pointing to the new URL (also stored
as the value of the <font size="2">`xlink:href` attribute of the
<font size="2"><async /> response element. ). Accessing the new URL
should return either a **404 Not Found** if the response is not
available; **200 OK** if it is.~~; and **410 Gone** if it has been
generated and deleted after some time.~~

(This scheme for applying HTTP status and HTTP headers to asynchronous
responses was originally suggested by Roberto De Almeida (roberto at
dealmeida.net), and has been modified to suit or needs.)

## Discussion

[Jimg](User:Jimg "wikilink") 09:56, 4 April 2012 (PDT) Random thoughts
about the 410-Gone response:

- How will servers know that a purged 'ephemeral' response is 'Gone?' To
  implement this, the server will need to store some information about
  each response for a while beyond the response's lifetime. That's
  doable, but I think to keep server implementors from feeling like they
  should store information about each ephemeral response forever, we can
  say that the 410 response is only supported for a day (to be
  arbitrary) and after the ephemeral response is gone for more than 24
  hours, trying to access it returns a 404. Servers can stretch that
  time out longer if they want to, but the purged asynchronous response
  has to return 410 for at least 24 hours.
- I can see the allure of the 410 response, but I wonder how many
  clients will really make use of it and, if they do, how? The big
  benefit seems to be that a client can realized that either it or the
  person using it has missed the window of opportunity for the response.
  Contrast this with using 404, the 404 just tells you it's not there -
  not that it was but now it's gone. So a smart client can say, you need
  to make the request again.
- As an implementation note, this 404, 200, 410 behavior could be coded
  using a lookup scheme. If the URL references something that's there,
  return it (200), if not look in a data store of some sort and if a
  'gone' record is present, return 410, else return 404. Not too hard,
  but is does mean that the server can't just write the response to a
  file and let Apache/Tomcat just serve the file. That's what I'd want
  as a server-writer. That way there's no web service to hassle with, no
  data store to maintain and I can let cron purge my 'cache' of
  asynchronous responses every hour using whatever Perl/Python/sh
  program I or the data provider want.
- I think simplicity argues for just using the 404 in both cases and
  betting that a smart client will use the time ranges in the response.

[ndp](User:Ndp "wikilink") 08:42, 5 April 2012 (PDT)


I agree with James, the 410 is a tough response to code, although I
could see implementing a server to use a file based semaphore: When the
file is purged from the cache the server "touches" a file of the same
name and a prepended ". Then if a client asks for "x" and the server
finds ".x" then the client gets a 410. If the server stages again "x" it
just removes ".x" once "x" is staged. But oh god - the concurrency
nightmare that this creates. Like I said: I agree with James, the 410
response might be unreasonably tough to code.

[ndp](User:Ndp "wikilink") 19:40, 18 April 2012 (PDT)


I worked with the response prototype and discovered a few things:

1.  Dublin Core date ranges (as mentioned above) are problematic for
    clients because they have to be parsed. In other words instead of a
    single date the DC date range is expressed as date1/date2. For
    example:
    <font size="2"><dc:date>`2007-03-01T13:00:00Z/2008-05-11T15:30:00Z`</dc:date></font>
    This is a monumental pain in the a\*\* for XSLT and only moderately
    less of a drag for javascript.
2.  The ISO 8601 date format is not parseable in javascript land with
    out the use of special functions. That's kind of a drag!
3.  Then there's always clock synchronization problems between the
    server and the client, timezones, etc.

These things lead me to believe that what we might want to consider
dumping the whole date thing and instead send a value in say, seconds,
that must elapse before the response is available, and another value in
say, seconds, that defines the duration of the availability. So for a
dataset that will become available in 10 minutes and that will remain
available for 24 hours after that:

<font size="2">

``` xml
<Dataset xml:base="http://host.server.gov/opendap/data/hdf/file.h5">
   <async xlink:href="http://host.server.gov/opendap/tmp/file.h5.dap" >
       <beginAccess>600</beginAccess>
       <accessDuration>86400</accessDuration>
   </async>
</Dataset>
```

</font>

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")