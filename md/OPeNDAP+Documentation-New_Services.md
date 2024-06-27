A service is a grouping of commands that are provided by a module and
can be handled by other modules, like data handlers. For example,
OPeNDAP provides the `dap` service with the commands
`das, dds, ddx, and dods`. These commands can be handled by the
different data handlers that can serve up OPeNDAP responses for these
commands. The format for each of these commands is `dap2`.

Services can be added to the BES in addition to the `dap` service. For
example, the [CEDAR project](http://cedarweb.hao.ucar.edu) adds the
service `cedar` with the commands `flat, tab, info, and stream`, each of
those commands with the format `cedar`.

You can also add commands to a service. For example, the dap-server
modules add the three commands `ascii, info_page, and html_form` to the
`dap` service because they use the base services provided by the `dap`
service. In other words, the `ascii` response, for example, takes a
`data` response and converts it to ascii.

And, you can add formats to the different commands. For example, the
fileout_netcdf module can take a data response `dods` and return it as a
netcdf file. So it adds the format `netcdf` to the `dods` command of the
`dap` service.

To add a service to the BES you register the service, each command and
the format of the command, with the BESServiceRegistry class. This is
done in the `initialize` method of your `BESModule` class.

[Services](Category:Extending_BES "wikilink")