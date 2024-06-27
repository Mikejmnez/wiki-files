|                                                    |                                              |                                                                                                              |               |
|----------------------------------------------------|----------------------------------------------|--------------------------------------------------------------------------------------------------------------|:-------------:|
| \[writing_client.html ![Top](previous.gif "Top")\] | \[writing_client.html ![Top](up.gif "Top")\] | \[writing_client_2.html ![2 Writing your own OPeNDAP client](next.gif "2 Writing your own OPeNDAP client")\] | **1 Preface** |

# 1 Preface

This tutorial describes the steps required to enable your client
application to interact with the OPeNDAP Data Access Protocol by using
the Java or C++ classes provided in the OPeNDAP class libraries. It also
describes the trade offs between using these toolkits and a
client-library interface such as netCDF.

The C++ and Java toolkis are class libraries which provide for direct
interaction with a remote OPeNDAP server. You'll need to write some
code.

If your client application currently uses one of the netCDF, GDAL or OGR
APIs, then you only need to relink your application with the
OPeNDAP-enabled version of the API library, allowing you to skip this
tutorial entirely.

------------------------------------------------------------------------

James Gallagher \<jgallagher@gso.uri.edu\>, 2004/04/24, Revision: 1.10



|                                                    |                                              |                                                                                                              |               |
|----------------------------------------------------|----------------------------------------------|--------------------------------------------------------------------------------------------------------------|:-------------:|
| \[writing_client.html ![Top](previous.gif "Top")\] | \[writing_client.html ![Top](up.gif "Top")\] | \[writing_client_2.html ![2 Writing your own OPeNDAP client](next.gif "2 Writing your own OPeNDAP client")\] | **1 Preface** |