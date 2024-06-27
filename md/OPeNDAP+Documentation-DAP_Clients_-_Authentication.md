## Overview

Many users access DAP servers using a browser as their primary software
interface. However there is also a growing group of users that utilize
either:

- A "smart" tool. Where "smart" means that the tool understands how to
  interact with a DAP service and construct DAP queries for data and use
  that in higher level client side activities like GUI based graphs,
  image display, selection, navigation etc.
- A command line tool such as "wget" or "curl" that can be used to
  extract data from a DAP service, but the URL construction is left to
  other software.

In both these examples we want these client software applications to be
able to manage authentication without user intervention, otherwise the
automation benefits of these tools is lost.

### Special Note To [NASA Earthdata Login (aka URS)](https://urs.earthdata.nasa.gov) Users

*Throughout this document the Earthdata Login is referred to as and is
synonymous with **URS** (NASA changed the name of this service shortly
before it's use became mandatory for all data access requests.)*

#### 'Approving' The DAP Server

Regardless of which software client you decide to employ, before you can
access any new Earthdata Login authenticated server you must first add
that sever to the list of **Approved Applications** in your Earthdata
Login profile.

To do this you will need the Earthdata Login Application name (aka UID)
under which the DAP server is registered with Earthdata Login and your
Earthdata Login credentials.

- With your browser, navigate to your Earthdata Login profile page.
- Click the **My Applications** tab.

On the **My Applications** page:

- Click the **Approve More Applications** button.

This will display the application search page:
![broder](UrsApplicationSearch.png "broder")

<div style="clear: both">
</div>

- Enter some or all of the name you picked (which became the UID) of
  your new application and click the **Search For Applications** button,
  this will bring you to the Earthdata Login Application Approval page:

<figure>
<img src="UrsApproveApplication.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

- Select your new application and click the **Approve Selected
  Applications** button.

You will be returned to the **My Applications** page where you should
now see your new application on the list of **Approved Applications**.
![broder](UrsApprovedApplicationList.png "broder")

<div style="clear: both">
</div>

## *curl* (a.k.a. *lib_curl*)

### URS

I was able to use command line *curl* to retrieve URS authenticated
resources using the following technique.

First in my home directory I created a *.netrc* file and set its file
permissions to read only for owner:

``` bash
[spooky:~] ndp% touch .netrc
[spooky:~] ndp% chmod 600 .netrc
[spooky:~] ndp% ls -l .netrc
-rw-------@ 1 ndp  staff  92 Nov 13 06:08 .netrc
```

Then I edited the *.netrc* file and associated my URS credentials with
the URS IdP instance utilized by my target DAP server:

``` bash
machine uat.urs.earthdata.nasa.gov
    login my_urs_uid
    password my_urs_password
```

I could then retrieve a DDS object in the URS authentication enabled
Hyrax server with the following *curl* command:

``` bash
curl -k -n -c ursCookies -b ursCookies -L --url https://54.172.97.47/opendap/data/nc/fnoc1.nc.dds
```

What is happening here?

-k
This tells *curl* to accept self-signed certificates. This is ok for
working with trusted (as in your own) "test" services but should be
removed for working with production systems. Because: Security,
Chain-Of-Trust, etc.

<!-- -->

-n
This tells *curl* to use that *~/.netrc* file I created.

<!-- -->

-c ursCookies
This tells *curl* to stash cookies in the file *ursCookies*

<!-- -->

-b ursCookies
This tells *curl* to read cookies from the file *ursCookies*

<!-- -->

-L
Also known as *--location*, this option tells *curl* to follow
redirects, which is a must for any OAuth2 flow.

**Note** - *The `--location-trusted` option should not be used as it
will cause **curl** to spread user credentials to servers other than to
which they were associated.*

<!-- -->

--url <https://54.172.97.47/opendap>
The desired URL, protected by the URS OAuth2 authentication flow.

In order to retrieve multiple URLs with out reauthenticating you can use
multiple instances of the *--url* parameter:

``` bash
curl -k -n -c ursCookies -b ursCookies -L --url https://54.172.97.47/opendap --url https://54.172.97.47/opendap/data/nc/fnoc1.nc.dds --url https://54.172.97.47/opendap/data/nc/coads_climatology.nc.dds
```

Or, since *curl* is actually pretty smart about using cookies and such
you can also make multiple *curl* requests with the same cookies and it
won't have to reauthenticate with URS once it's authenticated the first
time:

``` bash
curl -k -n -c ursCookies -b ursCookies -L --url https://54.172.97.47/opendap
curl -k -n -c ursCookies -b ursCookies -L --url https://54.172.97.47/opendap/data/nc/fnoc1.nc.dds
curl -k -n -c ursCookies -b ursCookies -L --url https://54.172.97.47/opendap/data/nc/coads_climatology.nc.dds
```

### LDAP

I was able to use command line *curl* to retrieve LDAP authenticated
resources using the following technique.

First in my home directory I created a *.netrc* file and set its file
permissions to read only for owner:

``` bash
[spooky:~] ndp% touch .netrc
[spooky:~] ndp% chmod 600 .netrc
[spooky:~] ndp% ls -l .netrc
-rw-------@ 1 ndp  staff  92 Nov 13 06:08 .netrc
```

Then I edited the *.netrc* file and associated my LDAP credentials with
the LDAP authenticated DAP server:

``` bash
machine 130.56.244.153
    login tesla
    password password
```

I could then access the top level directory of the LDAP authentication
enabled Hyrax server with the following *curl* command:

``` bash
curl -k -n -c ldapCookies -b ldapCookies  --url https://130.56.244.153/opendap
```

What is happening here?

-k
This tells *curl* to accept self-signed certificates. This is ok for
working with trusted (as in your own) "test" services but should be
removed for working with production systems. Because: Security,
Chain-Of-Trust, etc.

-n
This tells *curl* to use that *~/.netrc* file I created.

-c ldapCookies
This tells *curl* to stash cookies in the file *ldapCookies*

-b ldapCookies
This tells *curl* to read cookies from the file *ldapCookies*

--url <https://130.56.244.153/opendap>
The desired URL, protected LDAP authentication.

Note that the credentials are sent with every request so secure
transport is a must if user account are to be protected.

### Shibboleth

#### .netrc

I was not able to use command line *curl* to retrieve Shibboleth
authentication resources using the *.netrc* technique described in the
LDAP and URS sections.

Analysis of the HTTP conversation between the idp.testshib.org server
and *curl* shows that curl correctly follows the series of 302 redirects
issued to it, first by the Apache service bound to the Hyrax server and
then from the idp.testshib.org server. In every request to the
idp.testshib.org server the *curl* client correctly offers the
credentials via the HTTP Authorization header:

``` bash
0000: GET /idp/Authn/UserPassword HTTP/1.1
0026: Authorization: Basic bXlzZWxmOm15c2VsZg==
0051: User-Agent: curl/7.21.4 (universal-apple-darwin11.0) libcurl/7.2
0091: 1.4 OpenSSL/0.9.8z zlib/1.2.5
00b0: Host: idp.testshib.org
00c8: Accept: */*
00d5: Cookie: _idp_authn_lc_key=efbb6e2a9d893b47fb802ed575329ce69c101b
0115: 3ea8beb6744fab64fc406c358f; JSESSIONID=5A1731EDE00613B13803968CF
0155: AF06284
015e:
```

But the Shibboleth system doesn't respond to them. This may be a simple
configuration issue on the Shibboleth end, or it could be that the
Shibboleth protocol specifically forbids accepting credentials via HTTP
Authorization headers.

#### certificates

## *wget*

### URS

The *wget* documentation indicates that *wget* understands to use the
*.netrc* file that we created for *curl*, and happily it appears to
work, as long as other things are in place. Consider this *wget*
command:

``` bash
wget  --load-cookies cookies --save-cookies cookies --keep-session-cookie --no-check-certificate https://54.172.97.47/opendap/data/nc/fnoc1.nc.dds
```

What's happening here?

--load-cookies cookies
Load cookies from the file "cookies"

--save-cookies cookies
Save cookies to the file "cookies"

--keep-session-cookie
Save session cookies.

--no-check-certificate
Do not check the authenticity of the (self signed) certificates. This is
good for testing against your own servers running with self-signed
certificates in that this switch will allow you to experience success
when interacting with such servers. However, this switch breaks the
**chain of trust** and may allow bad things to happen if used on the
open internets. Thus, for regular use, do not include this switch!

<https://54.172.97.47/opendap/data/nc/fnoc1.nc.dds>
The URL to retrieve.

Here's the output of said *wget* request:

    [spooky:olfs/testsuite/urs] ndp% wget  --load-cookies cookies --save-cookies cookies --keep-session-cookie --no-check-certificate https://54.172.97.47/opendap/data/nc/fnoc1.nc.dds
    --2014-11-14 11:22:18--  https://54.172.97.47/opendap/data/nc/fnoc1.nc.dds
    Connecting to 54.172.97.47:443... connected.
    WARNING: cannot verify 54.172.97.47's certificate, issued by `/C=US/ST=RI/L=Narragansett/O=OPeNDAP Inc./OU=Engineering/CN=54.172.97.47/emailAddress=support@opendap.org':
      Self-signed certificate encountered.
    HTTP request sent, awaiting response... 302 Found
    Location: https://uat.urs.earthdata.nasa.gov/oauth/authorize?app_type=401&client_id=04xHKVaNdYNzCBG6KB7-Ig&response_type=code&redirect_uri=https%3A%2F%2F54.172.97.47%2Fopendap%2Flogin&state=aHR0cHM6Ly81NC4xNzIuOTcuNDcvb3BlbmRhcC9kYXRhL25jL2Zub2MxLm5jLmRkcw [following]
    --2014-11-14 11:22:19--  https://uat.urs.earthdata.nasa.gov/oauth/authorize?app_type=401&client_id=04xHKVaNdYNzCBG6KB7-Ig&response_type=code&redirect_uri=https%3A%2F%2F54.172.97.47%2Fopendap%2Flogin&state=aHR0cHM6Ly81NC4xNzIuOTcuNDcvb3BlbmRhcC9kYXRhL25jL2Zub2MxLm5jLmRkcw
    Resolving uat.urs.earthdata.nasa.gov... 198.118.243.34, 2001:4d0:241a:4089::91
    Connecting to uat.urs.earthdata.nasa.gov|198.118.243.34|:443... connected.
    WARNING: certificate common name `uat.earthdata.nasa.gov' doesn't match requested host name `uat.urs.earthdata.nasa.gov'.
    HTTP request sent, awaiting response... 401 Unauthorized
    Connecting to uat.urs.earthdata.nasa.gov|198.118.243.34|:443... connected.
    WARNING: certificate common name `uat.earthdata.nasa.gov' doesn't match requested host name `uat.urs.earthdata.nasa.gov'.
    HTTP request sent, awaiting response... 302 Found
    Location: https://54.172.97.47/opendap/login?code=a590cfc189783e29a7b8ab3ce1e0357618cbab3f590e7268a26e7ad1f7cf899d&state=aHR0cHM6Ly81NC4xNzIuOTcuNDcvb3BlbmRhcC9kYXRhL25jL2Zub2MxLm5jLmRkcw [following]
    --2014-11-14 11:22:20--  https://54.172.97.47/opendap/login?code=a590cfc189783e29a7b8ab3ce1e0357618cbab3f590e7268a26e7ad1f7cf899d&state=aHR0cHM6Ly81NC4xNzIuOTcuNDcvb3BlbmRhcC9kYXRhL25jL2Zub2MxLm5jLmRkcw
    Connecting to 54.172.97.47:443... connected.
    WARNING: cannot verify 54.172.97.47's certificate, issued by `/C=US/ST=RI/L=Narragansett/O=OPeNDAP Inc./OU=Engineering/CN=54.172.97.47/emailAddress=support@opendap.org':
      Self-signed certificate encountered.
    HTTP request sent, awaiting response... 302 Found
    Location: https://54.172.97.47/opendap/data/nc/fnoc1.nc.dds [following]
    --2014-11-14 11:22:21--  https://54.172.97.47/opendap/data/nc/fnoc1.nc.dds
    Connecting to 54.172.97.47:443... connected.
    WARNING: cannot verify 54.172.97.47's certificate, issued by `/C=US/ST=RI/L=Narragansett/O=OPeNDAP Inc./OU=Engineering/CN=54.172.97.47/emailAddress=support@opendap.org':
      Self-signed certificate encountered.
    HTTP request sent, awaiting response... 200 OK
    Length: unspecified [text/plain]
    Saving to: `fnoc1.nc.dds'

        [ <=>                                                                                                                                                                                                            ] 197         --.-K/s   in 0s

    2014-11-14 11:22:22 (7.23 MB/s) - `fnoc1.nc.dds' saved [197]

    [spooky:olfs/testsuite/urs] ndp% more fnoc1.nc.dds
    Dataset {
        Int16 u[time_a = 16][lat = 17][lon = 21];
        Int16 v[time_a = 16][lat = 17][lon = 21];
        Float32 lat[lat = 17];
        Float32 lon[lon = 21];
        Float32 time[time = 16];
    } fnoc1.nc;

It appears that *wget* followed the first redirect to
`uat.urs.earthdata.nasa.gov`, where the URS server responded with a "401
Unauthorized" (thanks to the the app_type=401 query parameter in the
redirect URL provided by mod_auth_urs). After getting the 401 *wget*
resubmits the request with the authentication credentials and the URS
server accepts them and redirects *wget* back to the *mod_auth_urs*
server to complete the request.

### LDAP

### Shibboleth

## *ncdump*

ncdump utilizes the NetCDF-C library to access DAP resources so ncdump
is a litmus test for any command line application that uses the netCDF C
library. Because the netCDF C library is the software component that is
performing the authentication, the configuration steps outlined here
should directly translate to any application that uses netCDF C. Note,
however, that these steps were tested against the version of netCDF C
retrieved from GitHub on 1 May 2105. That software likely corresponds to
netCDF version 4.3.3.1 or later. Contact Unidata for the latest
information.

### Earth Data Login (URS)

The following works with the ncdump (and oc client) code bundled with
NetCDF-4.3.3.1 Previous versions including 4.3.2 and 4.3.1 will not
work.

Edit (create as needed) the file *.netrc* in your HOME directory, and
set its file permissions to read only for owner:

``` bash
[spooky:~] ndp% touch .netrc
[spooky:~] ndp% chmod 600 .netrc
[spooky:~] ndp% ls -l . netrc
-rw-------@ 1 ndp  staff  92 Nov 13 06:08 . netrc
```

Add your Earth Data Login credentials to the *.netrc* file, associating
them with the Earth Data Login server that you normally authenticate
with, like this:

``` apache
machine urs.earthdata.nasa.gov
login <your Earth Data Login user name>
password <your Earth Data Login password>
```

Next, edit the *.dodsrc* file in your HOME directory so that it tells
DAP clients to use the *.netrc* file for password information:

``` apache
HTTP.COOKIEJAR=/Users/jimg/.cookies
HTTP.NETRC=/Users/jimg/.netrc
```

Here is a typical *.dodsrc* file.

``` apache
# OPeNDAP client configuration file. See the OPeNDAP
# users guide for information.
USE_CACHE=0
# Cache and object size are given in megabytes (20 ==> 20Mb).
MAX_CACHE_SIZE=20
MAX_CACHED_OBJ=5
IGNORE_EXPIRES=0
CACHE_ROOT=/Users/jimg/.dods_cache/
DEFAULT_EXPIRES=1
ALWAYS_VALIDATE=1
# Request servers compress responses if possible?
# 1 (yes) or 0 (false).
DEFLATE=0
# Proxy configuration:
# PROXY_SERVER=<protocol>,<[username:password@]host[:port]>
# NO_PROXY_FOR=<protocol>,<host|domain>
# AIS_DATABASE=<file or url>

# Earth Data Login and LDAP login information
HTTP.COOKIEJAR=/Users/jimg/.cookies
HTTP.NETRC=/Users/jimg/.netrc
```

### LDAP

To configure ncdump (and thus just about every client application that
uses netCDF C) for LDAP-back HTTP/S-Basic authentication, follow the
same exact procedure as outline above for URS, except that in the
*.netrc* file, use the OpenDAP server's machine name or IP number in
place of the URS authentication site. Here's a summary, with an example:

Edit (create as needed) the file *.netrc* in your HOME directory, and
set its file permissions to read only for owner:

``` bash
[spooky:~] ndp% touch .netrc
[spooky:~] ndp% chmod 600 .netrc
[spooky:~] ndp% ls -l . netrc
-rw-------@ 1 ndp  staff  92 Nov 13 06:08 . netrc
```

Add your URS credentials to the *.netrc* file, associating them with the
URS server that you normally authenticate with, like this:

``` apache
machine <OpenDAP server>
login <your login name>
password <your password>
```

Next, edit the *.dodsrc* file in your HOME directory so that it tells
DAP clients to use the *.netrc* file for password information:

``` apache
HTTP.COOKIEJAR=/Users/jimg/.cookies
HTTP.NETRC=/Users/jimg/.netrc
```

### Shibboleth

Does not support Shibboleth ECP profile.

## *Integrated Data Viewer (IDV)*

The Integrated Data Viewer is GUI driven data client that is based
around the CDM/NetCDF data model and utilizes that NetCDF-Java (and thus
the Java DAP implementation) to access remote DAP datasets. Because it
has a GUI it can retrieve (and cache for later) users credentials
directly from the user. Since IDV utilizes the Java-NetCDF library to
access DAP resources then in theory if it works for IDV then it should
work for all the other clients that use the Java-NetCDF library.

I [downloaded the latest version of
IDV](http://www.unidata.ucar.edu/downloads/idv/current/index.jsp) (5.0u2
on 11/19/14) and installed it on my local system.

### URS

For URS testing I utilized my AWS test service, configured to require
URS authentication for all access of Hyrax.

In IDV I attempted to choose a new dataset by starting with the "Data"
menu: Data \> Choose Data \> From A Web Server

In the resulting pane I entered the AWS test service URL for our friend
*coads_climatology.nc*:

<https://54.172.97.47/opendap/data/nc/coads_climatology.nc>

When I committed the edit (aka hit Enter) IDV popped up a dialog box
that indicated that the *uat.urs.earthdata.nasa.gov* server wanted my
credentials:

<figure>
<img src="IDVAuthDialog.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

I entered them, clicked the save password check box, and clicked the
*OK* button. IDV was then able to access the requested resource. After
the first successful access other resources at the AWS server were also
available, but without an additional authentication challenge being
presented to the user.

### LDAP

For testing I utilized an ANU/NCI puppet instance configured to require
LDAP authentication for all access of Hyrax.

In IDV I attempted to choose a new dataset by starting with the "Data"
menu: Data \> Choose Data \> From A Web Server

In the resulting pane I entered the AWS test service URL for our friend
*coads_climatology.nc*:

<https://130.56.244.153/opendap/data/nc/coads_climatology.nc>

When I committed the edit (aka hit Enter) IDV popped up a dialog box
that indicated that the *130.56.244.153* server wanted my credentials:

<figure>
<img src="IDV-LDAP.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

I entered them, clicked the save password check box, and clicked the
*OK* button. IDV was then able to access the requested resource.

### Shibboleth

*Summary: Failed To Authenticate*

For Shibboleth testing I utilized an AWS VM, configured to require
Shibboleth authentication for all access of Hyrax.

In IDV I attempted to choose a new dataset by starting with the "Data"
menu: Data \> Choose Data \> From A Web Server

In the resulting pane I entered the AWS VM service URL for our friend
*coads_climatology.nc*:

<https://54.174.13.127/opendap/data/nc/coads_climatology.nc>

When I committed the edit (aka hit Enter) IDV popped up a dialog box
that indicated that there was an error loading the data:

<figure>
<img src="IDV-Shibboleth.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

## *ToolsUI*

The ToolsUI application is a simple is GUI driven data client that is
based around the CDM/NetCDF data model and utilizes that NetCDF-Java
(and thus the Java DAP implementation) to access remote DAP datasets.
Because it has a GUI it can retrieve (and cache for later) users
credentials directly from the user.

I [downloaded the latest version of
ToolsUI](ftp://ftp.unidata.ucar.edu/pub/netcdf-java/v4.5/toolsUI-4.5.jar)
(4.5 on 11/19/14) and installed it on my local system. I launched
ToolsUI using the command line:

``` bash
java -Xmx1g -jar toolsUI-4.5.jar
```

### URS

*Summary: Authentication Successful*

For testing I utilized my AWS test service, configured to require URS
authentication for all access of Hyrax.

In ToolsUI selected the *Viewer* tab, and entered the AWS test service
URL for our friend *coads_climatology.nc*:

<https://54.172.97.47/opendap/data/nc/coads_climatology.nc>

When I committed the edit (aka hit Enter) ToolsUI popped up a dialog box
that indicated that the *uat.urs.earthdata.nasa.gov* server wanted my
credentials.

<figure>
<img src="ToolsUIAuthDialog.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

I entered them and clicked the *OK* button. ToolsUI was then able to
access the requested resource.

### LDAP

*Summary: Authentication Successful*

For testing I utilized an ANU/NCI puppet instance configured to require
LDAP authentication for all access of Hyrax.

In ToolsUI selected the *Viewer* tab, and entered the AWS test service
URL for our friend *coads_climatology.nc*:

<https://130.56.244.153/opendap/data/nc/coads_climatology.nc>

When I committed the edit (aka hit Enter) ToolsUI popped up a dialog box
that indicated that the *uat.urs.earthdata.nasa.gov* server wanted my
credentials.

<figure>
<img src="ToolsUI-LDAP.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

I entered them and clicked the *OK* button. ToolsUI was then able to
access the requested resource.

### Shibboleth

*Summary: Failed To Authenticate*

For Shibboleth testing I utilized an AWS VM, configured to require
Shibboleth authentication for all access of Hyrax.

In ToolsUI selected the *Viewer* tab, and entered the AWS test service
URL for our friend *coads_climatology.nc*:

<https://54.174.13.127/opendap/data/nc/coads_climatology.nc>

When I committed the edit (aka hit Enter) ToolsUI popped up a dialog box
that indicated that there was an error loading the data:

<figure>
<img src="ToolsUI-Shibboleth.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

## *Panoply*

The Panoply application is a sophisticated GUI driven data client that
is based around the CDM/NetCDF data model and utilizes that NetCDF-Java
(and thus the Java DAP implementation) to access remote DAP datasets.
Because it has a GUI it can retrieve (and cache for later) users
credentials directly from the user.

I [downloaded the latest version of
Panoply](http://www.giss.nasa.gov/tools/panoply/download_mac.html)
(4.0.5 on 11/20/14) and installed it on my local system. I launched
Panoply (clicking it's icon in my Applications folder)

### URS

*Summary: Authentication Successful*

For testing I utilized my AWS test service, configured to require URS
authentication for all access of Hyrax.

From the *File* menu, I selected "Open Remote Dataset.." and in the pop
dialog I entered the URL for our friend *coads_climatology.nc*:

<https://54.172.97.47/opendap/data/nc/coads_climatology.nc>

When I committed the edit (aka hit Enter) Panoply popped up a dialog box
that indicated that the *uat.urs.earthdata.nasa.gov* server wanted my
credentials.

<figure>
<img src="PanoplyAuthDialog.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

I entered them, clicked the save password check box, and clicked the
*OK* button. Panoply was then able to access the requested resource.

### LDAP

*Summary: Authentication Successful*

For testing I utilized an ANU/NCI puppet instance configured to require
LDAP authentication for all access of Hyrax.

From the *File* menu, I selected "Open Remote Dataset.." and in the pop
dialog I entered the URL for our friend *coads_climatology.nc*:

<https://130.56.244.153/opendap/data/nc/coads_climatology.nc>

When I committed the edit (aka hit Enter) Panoply popped up a dialog box
that indicated that the *uat.urs.earthdata.nasa.gov* server wanted my
credentials.

<figure>
<img src="Panoply-LDAP.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

I entered them, clicked the save password check box, and clicked the
*OK* button. Panoply was then able to access the requested resource.

### Shibboleth

*Summary: Failed To Authenticate*

For Shibboleth testing I utilized an AWS VM, configured to require
Shibboleth authentication for all access of Hyrax.

From the *File* menu, I selected "Open Remote Dataset.." and in the pop
dialog I entered the URL for our friend *coads_climatology.nc*:

<https://130.56.244.153/opendap/data/nc/coads_climatology.nc>

When I committed the edit (aka hit Enter) Panoply popped up a dialog box
that indicated that there was an error loading the data:

<figure>
<img src="Panoply-Shibboleth.png" title="broder" />
<figcaption>broder</figcaption>
</figure>

<div style="clear: both">
</div>

## Matlab, Ferret, Other applications that use NetCDF C

Check the version of the netCDF C library that the application uses;
once they have updated to 4.3.3.1 or later, authentication configuration
should be the same as the *ncdump* example above. That is, both URS and
LDAP-backed HTTP/S-Basic authentication should work by reading
credentials from the *.netrc* file given that the *.dodsrc* file is set
to point to them.

### URS & LDAP

Here's a short summary of the configuration Add your URS/LDAP
credentials to the *.netrc* file, associating them with the URS/OpenDAP
server that you normally authenticate with, like this:

``` apache
machine urs.earthdata.nasa.gov
login <your login name>
password <your URS password>

machine <OpenDAP server>
login <your login name>
password <your URS password>
```

Next, edit the *.dodsrc* file in your HOME directory so that it tells
DAP clients to use the *.netrc* file for password information:

``` apache
HTTP.COOKIEJAR=/Users/jimg/.cookies
HTTP.NETRC=/Users/jimg/.netrc
```

### Shibboleth

This is certain to not work until the netCDF C library is modified to
explicitly support it.

## PyDAP

The PyDAP software (pydap.org) provides one interface for python
programs to read from OpenDAP servers (the other is the netCDF4 python
module, which uses the netCDF-C library to actually access data, include
data from OpenDAP servers). PyDAP includes an extension mechanism so
that it can interact with different kinds of authentication systems.
This system is very flexible and we were able to use it to add support
for both LDAP-backed HTTP/S Basic authentication and ELA/URS. The same
scheme could be used to add support for Shibboleth, although it would
take additional development work (described in general below).

### URS & LDAP

To use PyDAP with a server the requires either LDAP or ELA/URS
authentication, first enter host, username and password credentials in
the .netrc file stored in your home account. If it does not yet exist,
make a file using a text editor. The format of this file is the
following set of three lines repeated for each host:

``` bash
machine <host name>
login <username>
password <password>
```

Note that for LDAP-backed HTTP/S Basic authentication, each host that
might prompt for credentials must be listed (and the username and
password repeated, even if it is the same for several hosts). For
ELA/URS, list only the ELA/URS site and the username and password you
use for it. Here's an example .netrc file:

``` bash
machine urs.earthdata.nasa.gov
login jhrg
password ****

machine uat.urs.earthdata.nasa.gov
login jhrg
password ****

machine 130.56.244.153
login tesla
password password
```

Once the .netrc file is configured, start python, run the function
install_basic_client() and then access servers. Here's a python script
that will open a PyDAP virtual connection to an authenticated server:

``` python
# Set up PyDAP to use the URS request() function

from pydap.util.urs import install_basic_client
install_basic_client()
from pydap.client import open_url
d = open_url('https://52.1.74.222/opendap/data/hdf4/S3096277.HDF')
...
```

### Shibboleth

This will require a new patch function, similar to
*install_basic_client()* be written. It will be a bit more complex
because of the increased complexity of Shibboleth, but the operation for
end-users will likely be the same.