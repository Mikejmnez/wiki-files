## What is PPT

PPT stands for Point to Point Transport. It is based on TFTP, which
stands for Trivial File Transfer Protocol. PPT is a protocol developed
for OPeNDAP by Jose Garcia at UCAR and is based on RFC 1350 (see
<http://en.wikipedia.org/wiki/TFTP> for more information). Our
implementation uses strings as tokens, but that is not part of the
protocol, you may implement your tokens anyway you want.

PPT started as an implementation of the RFC for TFTP using UDP, then
moved to TCP in order to avoid the ACK required for each packet
transmitted.

Here is some information about TFTP and how PPT differ:

- TFTP uses UDP (port 69) as its transport protocol (unlike FTP which
  uses TCP port 21). - PPT uses tcp.
- TFTP cannot list directory contents. - PPT does not deal with any
  commands, it passes those to the next layer up.
- TFTP has no authentication or encryption mechanisms. - We added that
  using standard SSL/X509
- TFTP is used to read files from, or write files to, a remote server. -
  Same deal, notice that for PPT, files are data objects.
- TFTP supports three different transfer modes, "netascii", "octet" and
  "mail", with the first two corresponding to the "ASCII" and "image"
  (binary) modes of the FTP protocol; the third is now obsolete and is
  hardly ever used. - PPT uses only tuneable binary buffers.
- The original TFTP protocol has a file size limit of 32 MB, although
  this was extended when RFC 2347 introduced block-size negotiation in
  1998 (allowing a maximum of 4 GB and potentially higher throughput). -
  No limit on size for PPT.
- Since TFTP utilizes UDP, it has to supply its own transport and
  session support. Each file transferred via TFTP constitutes an
  independent exchange. That transfer is performed in lock-step, with
  only one packet (either a block of data, or an 'acknowledgement') ever
  in flight on the network at any time. Due to this lack of windowing,
  TFTP provides low throughput over high latency links. - Obsolete for
  PPT.
- Due to the lack of security, it is dangerous over the open Internet.
  Thus, TFTP is generally only used on private, local networks. - It
  used to be that for this reason PPT was behind HTTPS and GridFTP. Now
  PPT has its own layer of security BUT it can be set up to be run
  without security BEHIND HTTPS and GridFTP (three tier model)

The BES architecture is a client/server architecture in which a server
application sits listening for OPeNDAP requests. These requests can be
for OPeNDAP data structures just as the CGI version serves (DAS, DDS,
DataDDS, etc...) or it can serve requests for other data formats and
other requests. An OPeNDAP client in this architecture connects to an
OPeNDAP server via TCP or UNIX sockets. The client sends a message to
the server requesting a PPT connection using a simple token. The server
responds with a token letting the client know whether the connection is
accepted or rejected. Once a connection has been established the client
sends OPeNDAP requests to the server.

The initial implementation of the PPT protocol used tokens to signify
the end of a transmission. This token would be placed at the end of a
transmission between client to server and server to client. The
receiving end would need to search the entire buffer, character for
character, for this end-of-transmission token. For smaller transmissions
this isn't such a big issue. But for large data transmissions, for
example in response to a 'get dods' request, the receiving end would
spend quite a bit of processing time searching for this
end-of-transmission token.

To get around this we decided to switch to chunking, where the first
characters received by an application would be the size of the chunk
followed by that many bytes of data. To signify the end of the
transmission, a chunk is sent with chunk size of zero.

## Initial handshaking in the PPT

In the PPT scheme the server is the software that is listening for a
connection, accepting commands, and providing responses. The client is
the software that initiates a connection, issues commands, and receives
responses.

1.  The client opens a socket connection to the server.
2.  The server accepts the connection.
3.  The client sends the string "PPTCLIENT_TESTING_CONNECTION" to the
    server.
4.  The server responds with one of the following tokens:
    1.  "PPTSERVER_CONNECTION_OK" - If it will accept the session
        connection for PPT based message exchange.
    2.  "PPT_PROTOCOL_UNDEFINED" - If it is busy or otherwise
        unavailable.
    3.  "PPTSERVER_AUTHENTICATE" - Authentication is required for
        connection

Any other response from the server indicates that initiating the session
failed and that the client should abandon the connection.

## PPT Authentication

If PPTSERVER_AUTHENTICATE is returned, then the client must make an SSL
connection to the server in order to authenticate. The hand-shaking
continues in this case as follows:

1.  The client sends the token "PPTCLIENT_REQUEST_AUTHPORT", which lets
    the server know that it understands the request for authentication
    and to request the SSL port to connect to.
2.  The server responds with the SSL port to connect to
3.  The client opens an SSL connection to the server on this port
4.  SSL authentication proceeds as normal.

For SSL authentication, the client and the server must be configured,
both through the use of a BES configuration file. The server side
requires the following parameters:

`BES.ServerSecure=yes|no`
`BES.ServerSecurePort=`<port number>
`BES.ServerCertFile=/full/path/to/serverside/certificate/file.pem`
`BES.ServerCertAuthFile=/full/path/to/serverside/certificate/authority/file.pem`
`BES.ServerKeyFile=/full/path/to/serverside/key/file.pem`

The client side requires the following paramters:

`BES.ClientCertFile=/full/path/to/clientside/certificate/file.pem`
`BES.ClientCertAuthFile=/full/path/to/clientside/certificate/authority/file.pem`
`BES.ClientKeyFile=/full/path/to/clientside/key/file.pem`

Once the secure connection has been accepted, authentication is
successful, the SSL connection is dropped and responses and requests are
handled through the initial non-secure connection.

Once the connection is established all further communications will be
chunked as described in the following sections.

## PPT Chunking

### Chunking Scheme

           chunked-body     =  *chunked-section last-chunk

           chunked-section  = chunk | chunk-extension

           chunk-extension  = chunk-size "x"  1*(chunk-ext-name [ "=" chunk-ext-val ] ";")

           chunk            = chunk-size "d" chunk-data

           chunk-size       = 7HEXDIG
           last-chunk       = 7("0") "d"

           chunk-ext-name   = token
                              ; sequence of 7-bit ASCII printable chars

           chunk-ext-val    = token | quoted-string
                              ; quoted-string is DQUOTE token DQUOTE

           chunk-data       = chunk-size(OCTET)
                              ; exactly chunk-size bytes

### Graphical representation of chunking scheme

<figure>
<img src="BES_chunking_7_1.jpg" title="Image:BES chunking 7 1.jpg" />
<figcaption>Image:BES chunking 7 1.jpg</figcaption>
</figure>

size - Stored in the first seven bytes are the size of the chunk (in
bytes). The size does not include the first 8 bytes which are the chunk
size and the chunk type (x or d). The size is a 16 bit integer encoded
in 7 bytes as 7 ASCII characters that represent the size as a 7
hexadecimal (base 16) digits. If the size is 0, this signifies the end
of the transmission, no more chunks follow.

type - The eighth byte is the type of chunk that follows. The type can
be one of

- x - extension, one or more name=value; pairs
- d - data, actual data

data - The data part of the chunk, meaning either extensions or data,
not both

Extensions are a name/value pair and can represent information needed by
the underlying communication layer. For example: status=error; would
mean that an error has occurred. This chunk would not contain the
error/exception information itself. Following chunks would hold that
information and would be of type d (for data).

It is possible that the chunk may not come across in one read call to
the underlying socket layer. The first 8 bytes represent information
about the chunk, the first seven bytes being the size of the chunk and
the eighth byte being the type of data the chunk contains. Read should
be called until the entire chunk has been received.

Read/receive should be called until the last chunk is received. The last
chunk is represented by chunk size of 0.

### Client/server exit

The exit handshake used to take place with an exit token. Now, instead,
it is done with an exit extension. The chunk will look like this:

`0000014xstatus=PPT_EXIT_NOW;`

followed by the last chunk signifying the end of the transmission.

`0000000d`

### Chunking State Diagram

<figure>
<img src="ChunkedStreamReaderStateDiagram.png"
title="ChunkedStreamReaderStateDiagram.png" width="1000" />
<figcaption>ChunkedStreamReaderStateDiagram.png</figcaption>
</figure>

### Error response

Built in to the BES currently is the error message response to a BES
client request. Specifically, if the BES client issues a command and the
BES server has a problem with it (either with the command itself, the
processing of the response to the command, or sending of the response)
then the BES server will send an extension chunk to the BES client with
the name/value pair "status=error;", and, following that chunk, data
chunks will contain the error information followed by the last chunk.
The extension chunk with the error status could come following a series
of data chunks containing the partial response.

## Communication Flow

<figure>
<img src="Communication_flow.jpg"
title="Image:Communication flow.jpg" />
<figcaption>Image:Communication flow.jpg</figcaption>
</figure>

## State Diagram

![Image:Client state
diagram.jpg](Client_state_diagram.jpg "Image:Client state diagram.jpg")
                             ![Image:Server state
diagram.jpg](Server_state_diagram.jpg "Image:Server state diagram.jpg")

## The API

There are two versions of the PPT available. The PPT C++ API is provided
with the BES, either source tarball or binary distributions. The PPTCAPI
package is a separate package, not yet available as a source tarball or
binary distribution. Both API's are available from
[svn](http://scm.opendap.org/svn/trunk/bes).

The C API is a client-side only API, whereas the C++ API is both client
and server.

### C++ API

To build this test use the following:

`g++ -o ppt_cpp_test -I/full/path/to/bes/include ppt_cpp_test.cc -L/full/path/to/bes/library/directory -lbes_ppt -lbes_dispatch`

    // ppt_cpp_test.cc

    #include <iostream>
    #include <string>
    #include <map>

    using std::cout ;
    using std::cerr ;
    using std::endl ;
    using std::string ;
    using std::map ;

    #include <PPTClient.h>
    #include <BESError.h>

    int
    main( int, char ** )
    {
        int return_status = 0 ;
        PPTClient *client = 0 ;
        try
        {
        string host = "localhost" ;
        int port = 10022 ;
        int timeout = 5 ;
        client = new PPTClient( host, port, timeout ) ;
        // string unix_socket = "/full/path/to/unix/socket" ;
        // client = new PPTClient( unix_socket, timeout ) ;

        // initialize the connection. This will perform the initial
        // handshaking
        client->initConnection() ;

        // send a request
        string cmd = "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request reqID=\"some_unique_value\" ><showVersion /></request>" ;
        map<string,string> extensions ;
        client->send( cmd, extensions ) ;

        bool done = false ;
        while( !done )
        {
            done = client->receive( extensions, &cout ) ;
            if( extensions["status"] == "error" )
            {
            // If there is an error, just flush what I have
            // and continue on.
            cout.flush() ;
            }
        }
        }
        catch( BESError &e )
        {
        cerr << "PPT C++ Client failed: " << e.get_message() << endl ;
        return_status = 1 ;
        }
        catch( ... )
        {
        cerr << "PPT C++ Client failed, unknown reason" << endl ;
        return_status = 1 ;
        }

        if( client )
        {
        client->closeConnection() ;
        delete client ;
        client = 0 ;
        }

        return return_status ;
    }

### C API

To build the C test use the following:

`gcc -o tryme -I/full/path/to/pptcapi/include tryme.c -L/full/path/to/pptcapi/library/directory -lbes_pptcapi`

    // ppt_c_test.c

    #include <stdlib.h>
    #include <stdio.h>
    #include <string.h>
    #include <fcntl.h>
    #include <errno.h>

    #include "pptcapi.h"

    void
    handle_error( char *msg, char *error )
    {
        if( error )
        {
        fprintf( stdout, "%s: %s", msg, error ) ;
        free( error ) ;
        }
        else
        {
        fprintf( stdout, "%s: %s", msg, "unknown error" ) ;
        }
    }

    int
    main( int argc, char **argv )
    {
        char *error = 0 ;

        // for debugging you would call the following function
        // error = pptcapi_debug_on( "./pptcapi.debug" ) ;
        // if( error )
        // {
        //     handle_error( "failed to turn debugging on", error ) ;
        //     return 1 ;
        // }

        // advanced tcp tuning to maximize tcp communication
        int tcp_receive_buffer_size = 0 ;
        int tcp_send_buffer_size = 0 ;
        int connection_timeout = 5 ;
        struct pptcapi_connection *connection =
        pptcapi_tcp_connect( "localhost", 10022, connection_timeout,
                             tcp_receive_buffer_size,
                     tcp_send_buffer_size,
                     &error ) ;
        //pptcapi_socket_connect( "/tmp/bes.socket", 5, &error ) ;
        if( !connection )
        {
        handle_error( "failed to connect", error ) ;
        return 1 ;
        }

        int result = pptcapi_initialize_connect( connection, &error ) ;
        if( result != PPTCAPI_OK )
        {
        handle_error( "failed to initialize connection", error ) ;
        pptcapi_free_connection_struct( connection ) ;
        return 1 ;
        }

        char cmd[128] ;
        sprintf( cmd, "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request reqID=\"some_unique_value\" ><showVersion /></request>" ) ;
        int len = strlen( cmd ) ;
        result = pptcapi_send( connection, cmd, len, &error ) ;
        if( result != PPTCAPI_OK )
        {
        handle_error( "failed to send command", error ) ;
        return 1 ;
        }

        result = PPTCAPI_OK ;
        char *buffer = 0 ;
        int buffer_len = 0 ;
        int bytes_read = 0 ;
        int bytes_remaining = 0 ;
        while( result != PPTCAPI_RECEIVE_DONE )
        {
        struct pptcapi_extensions *extensions = 0 ;
        result = pptcapi_receive( connection, &extensions, &buffer,
                      &buffer_len, &bytes_read,
                      &bytes_remaining, &error ) ;

        if( result == PPTCAPI_ERROR )
        {
            handle_error( "failed to receive response", error ) ;
            pptcapi_free_connection_struct( connection ) ;
            return 1 ;
        }

        int got_error = 0 ;
        struct pptcapi_extensions *next_extension = extensions ;
        while( next_extension && !got_error )
        {
            if( extensions->name &&
                !strncmp( extensions->name, PPT_STATUS_EXT, PPT_STATUS_EXT_LEN))
            {
            if( extensions->value &&
                !strncmp( extensions->value, PPT_STATUS_ERR,
                PPT_STATUS_ERR_LEN ) )
            {
                got_error = 1 ;
            }
            }
            next_extension = next_extension->next ;
        }
        pptcapi_free_extensions_struct( extensions ) ;
        extensions = 0 ;

        if( bytes_read != 0 )
        {
            write( 1, buffer, bytes_read ) ;
        }
        }

        result = pptcapi_send_exit( connection, &error ) ;
        if( result != PPTCAPI_OK )
        {
        handle_error( "failed to send exit", error ) ;
        pptcapi_free_connection_struct( connection ) ;
        return 1 ;
        }

        result = pptcapi_close_connection( connection, &error ) ;
        if( result != PPTCAPI_OK )
        {
        handle_error( "failed to close connection", error ) ;
        pptcapi_free_connection_struct( connection ) ;
        return 1 ;
        }

        pptcapi_free_connection_struct( connection ) ;

        // if debugging was turned on you would need to turn it off here
        // pptcapi_debug_off() ;

        return 0 ;
    }