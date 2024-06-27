# Overview of the OPeNDAP Client

A OPeNDAP client is any web client that makes a service request to a
OPeNDAP server. Since several of the OPeNDAP services return ASCII and
HTML data, any web browser, such as Netscape Navigator can be considered
a OPeNDAP client, so long as it is in the process of making a suitable
request to a OPeNDAP server. The clients of interest in this appendix,
however, are clients that use the OPeNDAP DAP (Data Access Protocol)
library to make their requests for data.

Of these clients, there are two varieties: clients that have been
written expressly for OPeNDAP, and clients that existed in some form
already, and that have been adapted to use with a OPeNDAP client
library.

<center>

<figure>
<img src="orig-client.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Original Program

</center>

The Original Program, untouched by OPeNDAP. The application's code
accesses data by calls to the netCDF library functions, linked with the
program. Data access is direct, with the application program accessing
local disk files to read data.

<center>

<figure>
<img src="client-arch1.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Modified Program

</center>

The Modified Program, using the OPeNDAP netCDF client library. The
application's code now accesses data by calls to the OPeNDAP netCDF
library functions. These are written to be functionally identical to the
original netCDF functions, but instead of using a local disk to retrieve
data, this library invokes functions from the OPeNDAP DAP library, which
makes HTTP GET requests to a OPeNDAP server. The client code is
unchanged.

<center>

<figure>
<img src="client-arch2.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Another Way

</center>

Another Way. An application program can also call the OPeNDAP DAP
directly, eliminating the need for a client library. When starting from
scratch, this is probably easiest, unless you are an old hand at one of
the supported data access APIs.