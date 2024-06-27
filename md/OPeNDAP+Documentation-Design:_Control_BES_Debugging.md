## Control BES Debugging

The HAI will be able to send a command to the BES daemon that enables it
to see a list of available debug contexts as well as their status (on or
off).

The HAI will be able to send a message to the BES daemon that
sets/clears the state of any given context.

### Getting the list of contexts

<hai:GetLogContexts/>

#### Responses

If the BES daemon is successful, it will return a response that lists
each debug context and its current state:

<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`    `<hai:LogContext name="''name''" state="''on|off''"/>
`    ...`
</hai:BesAdminCmd>

Other possible responses (errors) are:

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:ParseError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:ParseError>

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BESError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:BESError>

Here's a real example:

    <?xml version="1.0" encoding="ISO-8859-1"?>
    <hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
        <hai:LogContext name="ascii" state="off"/>
        <hai:LogContext name="besdaemon" state="on"/>
        <hai:LogContext name="csv" state="off"/>
        <hai:LogContext name="dap" state="off"/>
        <hai:LogContext name="ff" state="off"/>
        <hai:LogContext name="fits" state="off"/>
        <hai:LogContext name="fonc" state="off"/>
        <hai:LogContext name="gateway" state="off"/>
        <hai:LogContext name="h4" state="off"/>
        <hai:LogContext name="h5" state="off"/>
        <hai:LogContext name="nc" state="off"/>
        <hai:LogContext name="ncml" state="off"/>
        <hai:LogContext name="usage" state="off"/>
        <hai:LogContext name="www" state="off"/>
    </hai:BesAdminCmd>

### Setting the state of a context

<hai:SetLogContext name="''name''" state="''on|off''"/>

#### Responses

Success:

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BesAdminCmd xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `<hai:OK/>
` `</hai:BesAdminCmd>

Errors:

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:ParseError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:ParseError>

<?xml version="1.0" encoding="UTF-8"?>

` `<hai:BESError xmlns:hai="http://xml.opendap.org/ns/bes/admin/1.0#">
`     `*`message text`*
` `</hai:BESError>