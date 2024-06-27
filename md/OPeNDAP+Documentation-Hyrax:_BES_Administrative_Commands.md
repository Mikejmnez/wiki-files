## BES Administration Commands

The general form for all of the HAI commands is as follows: Namespace:
<http://xml.opendap.org/ns/bes/admin/1.0#>

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     ...`
` `</hai:BesAdminCmd>

### Start

Example:

<hai:Start />

Starts the master beslistener process. If a beslistner is already
running nothing will be done.

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:Start />
` `</hai:BesAdminCmd>

##### Response Examples

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:OK/>
` `</hai:BesAdminCmd>

Here are the two kinds of errors possible:

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:ParseError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:ParseError>

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BESError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:BESError>

### StopNow

Example:

<hai:StopNow/>

Stops all beslistener processes; does not wait for the current
transmissions to complete

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:StopNow/>
` `</hai:BesAdminCmd>

##### Response Examples

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:OK/>
` `</hai:BesAdminCmd>

Here are the two kinds of errors possible:

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:ParseError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:ParseError>

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BESError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:BESError>

### Exit

Element form:

<hai:Exit />

Shutdown the BES. This forces the daemon to exit, so the command
interface the HAI uses to start and stop listeners is *gone*. A user
will have to restart the daemon manually.

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:Exit />
` `</hai:BesAdminCmd>

##### Response Example

There's no response.

### GetConfig

Element form:

<hai:GetConfig />

Retrieves the BES configuration file.

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:GetConfig />
` `</hai:BesAdminCmd>

##### Response Example

    <?xml version="1.0" encoding="ISO-8859-1"?>
    <hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
        <hai:GetConfig module="bes.conf">
        ...
        </hai:GetConfig>

        <hai:GetConfig module="h4.conf">
        ...
        </hai:GetConfig>

        ...

    </hai:BesAdminCmd>

### SetConfig

Element form:

<hai:SetConfig module="''module-name''">
`     `*`...Module Name Configuration Content...`*
</hai:SetConfig>

Note that *module-name* is actually any of the configuration files
(*bes.conf*, *h4.conf*) and that while we use some conventions, a bes
really has a collection of files that are all sourced to form the
current configuration. So we're showing files and it's assumed that in
most cases those roughly correspond to the main configuration file for
the bes and smaller files specific to individual modules.

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:SetConfig module="ncml_module">
`     `*`...NcML Module Configuration Content...`*
`     `</hai:SetConfig>
` `</hai:BesAdminCmd>

##### Response Example

Returns a status indicating what happened (Was the configuration
accepted, was it valid, did the bes restart, etc)

    <?xml version="1.0" encoding="ISO-8859-1"?>
    <hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
        <hai:OK>Please restart the server for these changes to take affect.</hai:OK>
    </hai:BesAdminCmd>

Here are the two kinds of errors possible:

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:ParseError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:ParseError>

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BESError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:BESError>

### TailLog

Use this to get a 'tail' of the BES log.

Element form:

<hai:TailLog lines="''integer''"/>

Note that the value zero (0) has the special meaning to get all of the
lines in the log.

##### Request Example

    <?xml version="1.0" encoding="UTF-8"?>
     <hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
         <hai:TailLog lines="200"/>
     </hai:BesAdminCmd>

##### Response Example

Element form:

<hai:BesLog/>

    <?xml version="1.0" encoding="ISO-8859-1"?>
    <hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
        <hai:BesLog>
    [MDT Thu May 26 15:10:49 2011 id: 58293] 58293 from ip 127.0.0.1, port 62858 [set context dap_explicit_containers to no;] received
    [MDT Thu May 26 15:10:49 2011 id: 58293] 58293 from ip 127.0.0.1, port 62858 [set context errors to dap2;] received
    [MDT Thu May 26 15:10:49 2011 id: 58293] 58293 from ip 127.0.0.1, port 62858 [set container in catalog values catalogContainer,/data/nc/dapbench_test.nc;] received

    ...
    [MDT Fri May 27 12:10:00 2011 id: 86266] 86266 from ip 127.0.0.1, port 65245 [get html_form for d1 return as dap2 using http://localhost:8080/opendap/hyrax/data/nc/bears.nc] received
    </hai:BesLog>
    </hai:BesAdminCmd>

### GetLogContents

Element form:

<hai:GetLogContents />

Retrieves information about all of the existing (available) log contexts
for the BES.

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:GetLogContents />
` `</hai:BesAdminCmd>

##### Response Examples

    <?xml version="1.0" encoding="ISO-8859-1"?>
    <hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
        <hai:LogContext name="ascii" state="on"/>
        <hai:LogContext name="besdamone" state="on"/>
        <hai:LogContext name="nc" state="off"/>
        ...
    </hai:BesAdminCmd>

If there are no contexts, the response will be empty:

    <?xml version="1.0" encoding="ISO-8859-1"?>
    <hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
    </hai:BesAdminCmd>

### SetLogContents

Element form:

<hai:SetLogContents name="..." state="..." />

Set the loggin context for *name* to *state*.

##### Request Example

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:SetLogContents name="ascii" state="on" />
`     `<hai:SetLogContents name="besdaemon" state="off" />
` `</hai:BesAdminCmd>

##### Response Examples

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:OK/>
` `</hai:BesAdminCmd>

Here are the two kinds of errors possible:

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:ParseError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:ParseError>

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BESError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:BESError>