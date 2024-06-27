The Gateway Service provides interoperability between Hyrax and other
web services. Using the Gateway module, Hyrax can be used to access and
subset data served by other web services so long as those services
return the data in a form Hyrax has been configured to serve. For
example, if a web service returns data using HDF4 files, then Hyrax,
using the gateway module, can subset and return DAP responses for those
data.

## Special options supported by the handler

### Limiting access to specific hosts

Because this handler behaves like a web *client* there are some special
options that need to be configured to make it work. When we distribute
the client, it is limited to accessing only the local host. This
prevents misuse (where your copy of Hyrax might be used to access all
kinds of other sites). This gateway's configuration file contains a
'whitelist' of allowed hosts. Only hosts listed on the whitelist will be
accessed by the gateway.

Gateway.Whitelist: provides a list of URL of the form *protocol://host.domain:port* that will be passed through the gateway module. If a request is made to access a web service not listed on the Whitelist, Hyrax returns an error. Note that the whitelist can be more specific than just a hostname - it could in principal limit access to a specific set of requests to a particular web service.

example:

`Gateway.Whitelist=`[`http://test.opendap.org/opendap`](http://test.opendap.org/opendap)
`Gateway.Whitelist+=`[`http://opendap.rpi.edu/opendap`](http://opendap.rpi.edu/opendap)

### Recognizing responses

Gateway.MimeTypes: provides a list of mappings from data handler module to returned mime types. When the remote service returns a response, if that response contains one of the listed MIME types (e.g., *application/x-hdf5*) then the gateway will process it using the named handler (e.g., *h5*). Note that if the service does not include this information the gateway will try other ways to figure out how to work with the response.

These are the default types:

`Gateway.MimeTypes=nc:application/x-netcdf`
`Gateway.MimeTypes+=h4:application/x-hdf`
`Gateway.MimeTypes+=h5:application/x-hdf5`

### Network proxies and Performance optimizations

There are four parameters that are used to configure a proxy server that
the gateway will use. Nominally this is used as a cache, so that files
do not have to be repeatedly fetched from the remote service and that's
why we consider this a 'performance' feature. We have tested the hander
with Squid because it is widely used on both linux and OS/X and because
in addition to it's proxy capabilities, it is often used as a cache.
This can also be used to navigate firewalls.

Gateway.ProxyProtocol: Which protocol(s) does this proxy support. Nominally this should be *http*.
Gateway.ProxyHost: On what host does the proxy server operate? Often you want to use *localgost* for this.
Gateway.ProxyPort: What port does the proxy listen on? Squid defaults to *3218*; some documentation for web accelerators
Gateway.NoProxy: Provide a regular expression that describes URLs that should *not* be sent to the proxy. This is particularly useful for running the gateway on the hosts that stage the service accessed via the gateway. In this cases, a proxy/cache like squid may not process 'localhost' URLs unless its configuration is tweaked quite a bit (and there may be no performance advantage to having the proxy/cache store extra copies of the files given that they are on the host already). This parameter was added in version 1.1.0.

`Gateway.ProxyProtocol= `
`Gateway.ProxyHost=`
`Gateway.ProxyPort=`
`Gateway.NoProxy=`

#### Using Squid

Squid makes a great cache for the gateway. In our testing we have used
Squid only for services running on port 80.

Squid is a powerful tool and it is worth looking at its [web
page](http://www.squid-cache.org/).

#### Squid and Dynamic Content

Squid follows the HTTP/1.1 specification to determine what and how long
to cache items. However, you may want to force Squid to ignore some of
the information supplied by certain web services (or to different
default values when the standard information is not present). If you are
working with a web server that does not include caching control headers
in its responses but does have 'cgi-bin' or '?' in the URL, here's how
override Squid's default behavior (which is to never cache items
returned from a 'dynamic' source (i.e., one with 'cgi-bin' or '?' in the
URL). The value below will cause Squid to cache response from a dynamic
source for 1440 minutes unless that response includes an Expires: header
telling to cache to behave differently

In the squid configuration file, find the lines:

    # refresh patterns (squid-recommended)
    refresh_pattern ^ftp:       1440    20% 10080
    refresh_pattern ^gopher:    1440    0%  1440
    refresh_pattern -i (/cgi-bin/|\?) 0 0%  0
    refresh_pattern .       0   20% 4320

And change the third refresh_pattern to read:

    refresh_pattern -i (/cgi-bin/|\?) 1440  20% 10080

#### How can I tell if a service sends Cache Control headers

Here are two ways to check:

- Go to
  [<http://www.ircache.net/cgi-bin/cacheability.py>](http://www.ircache.net/cgi-bin/cacheability.py)
- *curl -i <URL> \| more* and look at the headers at the top of the
  message.

#### Using Squid on OS/X

If you're using OS/X to run Hyrax, the easiest Squid port is
[SquidMan](http://web.me.com/adg/squidman/index.html). We tested version
SquidMan 3.0 (Squid 3.1.1). Run the SquidMan application and under
*Preferences... General* set the port to something like 3218, the cache
size to something big (16GB) and Maximum object size to 256M. Click
'Save' and you're almost done.

Now in the *gateway.conf* file, set the proxy parameters like so:

`Gateway.ProxyProtocol=http`
`Gateway.ProxyHost=localhost`
`Gateway.ProxyPort=3218`
`Gateway.NoProxy=http://localhost.*`

assuming you're running both Squid and Hyrax on the same host.

Restart the BES and you're all set.

To test, make some requests using the gateway
(http://localhost/opendap/gateway) and click on SquidMan's 'Access Log'
button to see the caching at work. The first access, which fetches the
data, will say *DIRECT/<ip number>* while cache hits will be labeled
*NONE/-*.

#### Squid, OS/X and Caching Dynamic Content

By default SquidMan does not cache dynamic content that lacks cache
control headers in the response. To hack the squid.conf file and make
the change in the *refresh_pattern* described above do the following:

1.  Under Preferences... choose the 'Template' tab and scroll to the
    bottom of the text;<img src="Edit_the_squid.conf_file.png"
    title="Edit_the_squid.conf_file.png" width="200"
    alt="Edit_the_squid.conf_file.png" />
2.  Edit the line, replacing "0 0% 0" with "1440 20% 10080"; and
3.  'Save' and then 'Stop Squid' and 'Start Squid' (note the helpful
    status messages in the 'Start/Stop'
    window).<img src="Squid_1.png" title="Squid_1.png" width="200"
    alt="Squid_1.png" /><img src="Squid_2.png" title="Squid_2.png" width="200"
    alt="Squid_2.png" /><img src="Squid_3.png" title="Squid_3.png" width="200"
    alt="Squid_3.png" />

## Known Problems

For version 1.0.1 of the gateway, we know about the following problems:

1.  Squid does not cache requests to localhost, but our use of the proxy
    server does not by-pass requests to localhost. Thus, using the
    gateway to access data from a service running on localhost will fail
    when using squid since the gateway will route the request to the
    proxy (i.e., squid) where it will generate an error.
2.  Not using a caching proxy server will result in poor performance.