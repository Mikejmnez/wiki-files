This is a place to start discussing peoples desires/needs for a Hyrax
Administrators Interface (HAI).

## Background

Members of the Hyrax users community are developing robust, highly
available data services in operational settings, and have asked for an
administrators interface that will allow them to monitor, control,
reconfigure, and debug the Hyrax frontend and backend servers from a
single console.

Additionally, to support external services integration with the Hyrax
data catalog and service, Hyrax needs to provide an administration
interface for service configuration and data catalog changes (e.g.
adding new data sets to a catalog). Example of an external service
integration with Hyrax is an organisation's digital library service for
registering and maintaining access to data products. Once the user
registers a new data set including data location and data access rights,
the user may select an access service and protocol such as Hyrax OPeNDAP
or WMS. This external service will connect to a Hyrax service using the
Hyrax Administrators Interface to add a new data set to the catalog.

This page is the starting point for organizing the design work for the
Hyrax Administrators Interface (HAI).

## Definitions

*Administrator*
The *administrator* is the human that operates the HAI.

<!-- -->

*User*
The *user* is a human that uses client software such as Kepler, Matlab
OPeNDAP Ocean Toolbox, a web browser, and others to make requests (HTTP,
SOAP, etc.) of the Hyrax server.

<!-- -->

*Operator Console*
The 24x7 operator's console to monitor and control the data services as
well as the user data requests, and receive service notifications.

<!-- -->

*Administrator Console*
The system administrator's console to monitor, control, reconfigure, and
debug the Hyrax frontend and backend servers, and receive service
notifications.

## Logging and Control Features

### Secure log-in for administration - Implementation

Provide an secure login services for Hyrax that allow an administrator
to login to (and eventually control) the server.

#### Use Cases

##### [Administrator Logs into the Hyrax Admin Interface](HAI_Use_Case:_Administrator_Logs_into_the_Hyrax_Admin_Interface "wikilink")



First draft checked into SVN. In order to do this it simply required a
carefully defined set of security constraints in the web.xml for Hyrax
in addition to configuring and enabling tomcat to use
SSL.--[ndp](User:Ndp "wikilink") 13:53, 28 April 2011 (PDT)

### Control the server (starting, stopping, reconfiguration) - Implementation

<font color="green">Done.</font>

Provide a secure user interface that allows an authorized user to start,
stop, and reconfigure the server.

#### Use Cases

##### [Stop and Start a BES](HAI_Use_Case:_Stop_and_Start_a_BES "wikilink")


[BES Control Design Page](HAI_Design:_Stop_and_Start_a_BES "wikilink")

<font color="green">Prototype complete</font>

##### [Examine BES processes](HAI_Use_Case:_Examine_BES_processes/connections "wikilink")

<font color="green">Prototype complete</font>

### Access to the running logging information - Implementation

Provide a secure user interface that allows an authorized to view (and
eventually control the granularity of) the servers logging output.

#### Use Cases

##### [View OLFS and BES logs](HAI_Use_Case:_View_OLFS_and_BES_logs "wikilink")



First draft of the OLFS log viewer checked into svn. Log monitoring can
be stopped and started via a web based gui. The gui needs to support
modifying what is getting logged (which packages/classes) and at what
log level (debug,info,warn,error,all). --[ndp](User:Ndp "wikilink")
13:53, 28 April 2011 (PDT)

<font color="green">Prototype complete</font>

##### [Control OLFS debugging](HAI_Use_Case:_Turn_on/off_OLFS_debugging_and_view/save/stream_the_output "wikilink")

<font color="green">Prototype complete</font>

##### [Control BES debugging](HAI_Use_Case:_Turn_on/off_BES_debugging_and_view/save/stream_the_output "wikilink")

<font color="green">Done.</font>


[Design: Control BES
Debugging](Design:_Control_BES_Debugging "wikilink")

### Add new datasets to the server - Design & prototype implementation

#### Use Cases

This topic is a bit of red herring since users cannot add datasets to
Hyrax and adding datasets to a server can be accomplished by storing the
data files anywhere a BES can read them (including using symbolic
links). However, what BOM wants is maybe better characterized as
'authorization'. This is covered in our design for the authentication
and authorization services (link below).

##### [Add a new dataset to the Hyrax catalog](HAI_Use_Case:_Add_a_new_dataset_to_the_Hyrax_catalog "wikilink")

## User Management Features

<font color="green">Response size limits done; Design for authentication
done</font>

### Limit response sizes for non-authenticated users - Implementation

Provide a mechanism that allows the administrator to configure hyrax so
that users that are not authenticated will only be allowed to receive
data responses that are smaller than an adjustable threshold size.

#### Use Cases

### Secure user sessions - Design

Secure user sessions involves two basic activities: Authentication (Ac)
and Authorization (Az). Authentication is the process in which the
server identifies a particular user. Authorization is the process that
determines what a user can do or see on the server.

[DAP: Authentication
Discussion](DAP:_Authentication_Discussion "wikilink")

#### Use Cases

##### [User gets an authenticated session.](HAI_Use_Case:_User_securely_authenticates_and_gets_an_open_session "wikilink")

##### [User gets a secure session.](HAI_Use_Case:_User_securely_authenticates_and_gets_a_secure_session "wikilink")

##### [Authentication mechanism compatibility with OC and Java DAP libraries](HAI_Use_Case:_Authentication_mechanism_compatibility_with_OC_and_Java_DAP_libraries "wikilink") (This is a high priority case)

### Create administration interface to control users' capabilities - Design

Description here...

#### Use Cases

##### [Manage user access rules.](HAI_Use_Case:_View/set_user-roles_and_their_mapping_to_URLs "wikilink")

##### [Limit response sizes.](HAI_Use_Case:_Limit_response_size_for_non-authenticated_users "wikilink")


[Design: Limit Response Sizes](Design:_Limit_Response_Sizes "wikilink")

<font color="green">Prototype partly complete</font>

## NetCDF File Support

### Features

1.  Support for linking the handler with the new API (Adds chunking and
    compression) - Implementation <font color="green">Complete</font>
2.  Support for the new API features that are compatible with DAP2 -
    Implementation

## Deliverables

## Period of use

The API features are to be permanent features in the Hyrax data service
for use in building administrator and operator console applications to
support an operational data service. The operational Hyrax data service
is to support various service level access (SLA) requirements and
provide timely, reliable data delivery with transaction logging.

## [Additional Desired Hyrax Admin Interface Features](Other_Hyrax_Admin_Interface_Features_That_Would_Be_Nice_But_Need_Funding "wikilink")

[Category:HAI](Category:HAI "wikilink")