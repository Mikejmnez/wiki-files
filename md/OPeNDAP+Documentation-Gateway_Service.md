## Overview

<figure>
<img src="Gateway_service.jpg" title="Gateway_service.jpg" />
<figcaption>Gateway_service.jpg</figcaption>
</figure>

The Gateway Service provides Hyrax with the ability to apply DAP
constraint expressions and server side functions to data available
through any network URL. This is accomplished by encoding the data
source URL into the DAP request URL supplied to the gateway_service. The
gateway_service decodes the URL and uses the BES to to retrieve the
remote data resource and transmit the appropriate DAP response back to
the client. The system employs a white list to control what data systems
the BES will access.

A Data Service Portal (DSP), such as Mirador will:

- Provide the navigation/search/discovery interface to the data source.
- Generate the data source URLs.
- Encode the data source URLs.
- Build a regular DAP query as the DAP dataset ID.
- Hand this to the client (via a link or what have you in the DSP
  interface)

## BES gateway_module

The gateway_module handles the gathering of the remote data resource and
the construction of the DAP response.

The gateway_module:

- Evaluates the data source URL against a white list to access
  permission.
- Retrieves remote data source .
- Determines data type by:

1.  Direct identification via the BesXmlApi. (You tell it.)
2.  HTTP Content-Disposition header
3.  Applying the BES.TypeMatch string to the last term in the path
    section of the data source URL.

The BES will **not** persist the data resources beyond the scope of each
request.

## OLFS gateway_service

The gateway_service is responsible for:

- Decoding the incoming dataset URLs.
- Building the request for the BES.
- Returning the response from the BES to the client.

## Encoding data source URLs

The data source URLs need to be encoded in the DAP data request URL that
is used to access the gateway_service.

There are many ways to encode something in this context.

### Prototype Encoding

As a prototype encoding we'll use an hex ascii encoding. In this
encoding each character in the data source URL is expressed as is
hexadecimal value using ascii characters.

Here is
[hexEncoder.tgz](http://www.opendap.org/pub/gateway_service/hexEncoder.tgz)
([sig](http://www.opendap.org/pub/gateway_service/hexEncoder.tgz.sig)),
a gzipped tar file containing a java application can perform the
encoding and decoding duties from the command line. Give it a whirl -
it's a java application in a jar file. There is a bash script
(hexEncode) that should launch it.

The source code for the EncodeDecode java class used by hexEncode is
available here:
<http://scm.opendap.org/svn/trunk/olfs/src/opendap/gateway/EncodeDecode.java>

#### Example 1

source string: <http://www.google.com>

<!-- -->

stringToHex(): 687474703a2f2f7777772e676f6f676c652e636f6d

<!-- -->

hexToString(stringToHex(): <http://www.google.com>

#### Example 2

Source string: <http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET?service=WCS&version=1.0.0&request=GetCoverage&coverage=H2OMMRStd&crs=WGS84&bbox=-107.375000,51.625000,-102.625000,56.375000&format=netCDF&TIME=2002-10-03&resx=0.25&resy=0.25&interpolationMethod=Nearest%20neighbor>

<!-- -->

stringToHex():

687474703a2f2f67306475703035752e6563732e6e6173612e676f762f6367692d62696e2f63656f7041495258325245543f736572766963653d5743532676657273696f6e3d312e302e3026726571756573743d476574436f76657261676526636f7665726167653d48324f4d4d52537464266372733d57475338342662626f783d2d3130372e3337353030302c35312e3632353030302c2d3130322e3632353030302c35362e33373530303026666f726d61743d6e65744344462654494d453d323030322d31302d303326726573783d302e323526726573793d302e323526696e746572706f6c6174696f6e4d6574686f643d4e6561726573742532306e65696768626f72

hexToString(stringToHex(): <http://g0dup05u.ecs.nasa.gov/cgi-bin/ceopAIRX2RET?service=WCS&version=1.0.0&request=GetCoverage&coverage=H2OMMRStd&crs=WGS84&bbox=-107.375000,51.625000,-102.625000,56.375000&format=netCDF&TIME=2002-10-03&resx=0.25&resy=0.25&interpolationMethod=Nearest%20neighbor>

#### Example 3

source string: foobar

<!-- -->

stringToHex(): 666f6f626172

<!-- -->

hexToString(stringToHex(): foobar