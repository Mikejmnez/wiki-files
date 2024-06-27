[\<\< Back to OPULS Development](OPULS_Development "wikilink")

## Domain Specific Models

A domain specific model is one which has constructs that are specific to
a particular domain. Examples are the CDM [Coordinate System and
Scientific Feature
Type](http://www.unidata.ucar.edu/software/netcdf-java/CDM) models,
which augment CDM with features such as lat-lon based indexing, grids,
and point data.

It is often the case that a domain specific model is implemented by
providing an API that in turn is implemented with respect to some
underlying, more generic data model. Again, CDM is an example, where the
*Coordinate System* and *Scientific Feature Type* models are implemented
using the underlying CDM *Data Access Layer* model.

DAP4 also provides a good, generic model capable of supporting domain
specific models. In fact, it should be possible to implement the
equivalent of the CDM *Coordinate System* and *Scientific Feature Type*
models on top of DAP4.

[centered](File:dm.png "wikilink")

<center>

Figure 1. Notional Domain Model over DAP4 Architecture

</center>

Figure 1 shows a notional architecture for using DAP4 as the basis for
domain specific modeling.

The client is given an API supporting a domain specific model. It is
assumed here that an instance of the domain model meta-data is
represented as a traversable abstract syntax tree. So, the model API
would provide the following operations.

1.  Request the meta-data for a specific dataset. The result would be a
    reference to the root of the abstract tree for that meta-data. This
    is analogous to asking DAP2 for a DDS.
2.  Traverse the meta-data tree.
3.  Request some subset of the data associated with the dataset. The
    request would be in terms of the domain-specific model.

The domain-specific library implementing the API would be responsible
for making requests to the server for information. Those requests would
indicate to the server the domain model being used as well as the
dataset being requested.

The reply from the server would be in the form of a DAP4 DDX and data
with annotations (i.e. attributes and extra variables) sufficient to
allow the client-side domain model library to convert the returned
information to the domain model for presentation to the client.

The server side operation is similar. It is assumed that request from a
client contains sufficient identifying information to allow the server
(a servlet server such as Tomcat) to forward the request to the servlet
capable of intrepreting such a domain specific model request.

The domain specific model servlet is capable of translating the dataset
into DAP4 as the reply to the client's request. As expected by the
client, the reply is annotated with sufficient information to allow the
generic DAP4 reply to be converted to the domain specific model.

[Dennis Heimbigner](User:dmh "wikilink")

[Development](Category:Development "wikilink")
[DAP4](Category:DAP4 "wikilink")