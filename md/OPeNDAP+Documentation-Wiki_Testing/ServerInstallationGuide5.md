# Constructing the URL

After a dataset has been installed, and the handler programs installed,
you need to know what its address is. If you've tested the web server
installation as in
([Chapter2](Wiki_Testing/ServerInstallationGuide2 "wikilink")), you know
the beginning of the URLs that will point to data at your site.

(If you're using the DRDS Java server, see ([Section
4.3.3](Wiki_Testing/ServerInstallationGuide4 "wikilink")).)

Suppose your web server's document root is at:

    /var/www/html/

And you are serving netCDF data that you've stored at:

    /var/www/html/datasets/atlantic/cooldata.nc

Following the test URL in
([Chapter2](Wiki_Testing/ServerInstallationGuide2 "wikilink")), this
would be the URL to use in a client.

    http://yourmachine/cgi-bin/nph-dods/datasets/atlantic/cooldata.nc

Note that this URL will work for a DAP client, but not for a standard
web browser. If you want to use a standard web browser to test your
installation, read on, and also take a look at [<cite>The OPeNDAP
QuickStart Guide</cite>](http://docs.opendap.org/index.php/QuickStart) .
See note

## Constructing a OPeNDAP/JGOFS URL

Suppose that you have installed the OPeNDAP/JGOFS handler, as described
above, on the machine <font color='green'>test.opendap.org</font>. And a
partial listing of the the data objects dictionary contained the
following definitions:

    test0=def(/var/www/html/data/test0.data)
    s87_xbt=def(/var/www/html/data/xbt.catalog)
    s87_ctd=def(/var/www/html/data/ctd.catalog)
    htf=autoedges(/fronts/hatteras-to-florida/edges)
    htn=autoedges(/fronts/hatteras-to-nova-scotia/edges)
    glk=autoedges(/fronts/great-lakes/edges)

A URL that references the data object 's87_xbt' would look like:

    http://test.opendap.org/cgi-bin/nph-dods/s87_xbt