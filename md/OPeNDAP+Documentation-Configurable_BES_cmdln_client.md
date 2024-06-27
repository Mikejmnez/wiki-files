Currently, only the commands that are available by default in the BES
and the DAP modules are usable within the BES cmdln client when using
the interactive string commands (set container in catalog values
c,data/nc/fnoc1.nc;) These string commands are translated into the
appropriate BES XML request documents.

Considering that the BES is configurable, and new commands can be added
to the BES, it would be advantageous if we could add translations to the
BES cmdln client to be able to translate these new commands into their
appropriate BES XML request documents.

This would also apply to the BES standalone version considering that it
also uses the same translation mechanism.

Here's what needs to happen:

1.  The BES command line client already has a requirement to use a
    configuration file IF secure authentication is required by the
    server. For this reason, we need to create a bes_client.conf file
    that can load configuration files from a directory much like the
    bes.conf file does (etc/bes/modules for bes.conf, and
    etc/bes/clients for the client) This client configuration could
    contain the key files, and modules to load for the cmd line client.
    This will also become handy when we develop different communication
    protocols for the BES.
2.  These client command modules will be dynamically loaded, just as is
    the case for the BES, and will allow additional translations to be
    added to the command framework.
3.  The CmdTranslation class already allows for the addition of
    translation functions.

[Configurable BES cmdln client](Category:Development "wikilink")
[Configurable BES cmdln client](Category:Hyrax_Development "wikilink")