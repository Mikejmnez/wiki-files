There are several ways in which Hyrax can be customized:

- Web interface look and feel can be changed, as can the pages served.
- Custom DispatchHandlers for the OLFS
- Custom RequestHandlers for the BES.




# Web page customization

Hyrax's public "face" is the web pages that are produced by servlets
running in the Tomcat servlet engine. Almost all of these pages can be
completely customized by the site administrator by editing a combination
of HTML, XSLT, and CSS files.

## Where to make the changes

All of the default versions of the HTML, XSLT, and CSS files come
bundled with Hyrax in the *\$CATALINA_HOME/webapps/opendap/docs*
directory. You can make changes there, but installing new versions of
the OLFS software will overwrite your modifications.

However, if the *docs* directory is copied (preserving its structure) to
*\$CATALINA_HOME/content/opendap/* (creating the directory
*\$CATALINA_HOME/content/opendap/docs*), then Hyrax will serve the files
from the new location.

Warning: *Do NOT remove files from this new directory (Or the old one for that matter). Each file, in its location, is required by Hyrax. You can make changes to the files but you should not rename or remove them.*

Nothing inside the *\$CATALINA_HOME/content* directory is
(automatically) changed when installing new versions of Hyrax.

**The rest of these instructions are written with the assumption that a
copy of the *docs* directory has been made as described above.**

## What to change

### HTML Files

The HTML files provide the static content of a Hyrax server.

<table>
<tbody>
<tr class="odd">
<td><p><u>File</u></p></td>
<td><p>      </p></td>
<td><p><u>Location</u></p></td>
<td><p>      </p></td>
<td><p><u>Description</u></p></td>
</tr>
<tr class="even">
<td><p><strong>index.html</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs</em></p></td>
<td></td>
<td><p>The documentation web page for the top level of Hyrax. As shipped
it contains a description of Hyrax and links to documentation and
funders. The contents.html pages (aka the OPeNDAP directories) links to
this document.<br />
<br />
</p></td>
</tr>
<tr class="odd">
<td><p><strong>error400.html</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs</em></p></td>
<td></td>
<td><p>Contains the default error page that Hyrax will return when the
client request generates a <strong>Bad Request</strong> error
(Associated with an HTML status of 400)<br />
<br />
</p></td>
</tr>
<tr class="even">
<td><p><strong>error403.html</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs</em></p></td>
<td></td>
<td><p>Contains the default error page that Hyrax will return when the
client request generates a <strong>Forbidden</strong> error. (Associated
with an HTML status of 403)<br />
<br />
</p></td>
</tr>
<tr class="odd">
<td><p><strong>error404.html</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs</em></p></td>
<td></td>
<td><p>Contains the default error page that Hyrax will return when the
client request generates a <strong>Not Found</strong> error. (Associated
with an HTML status of 404)<br />
<br />
</p></td>
</tr>
<tr class="even">
<td><p><strong>error500.html</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs</em></p></td>
<td></td>
<td><p>Contains the default error page that Hyrax will return when the
client request generates an <strong>Internal Server Error</strong>.
(Associated with an HTML status of 500)<br />
<br />
</p></td>
</tr>
<tr class="odd">
<td><p><strong>error501.html</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs</em></p></td>
<td></td>
<td><p>Contains the default error page that Hyrax will return when the
client request generates an <strong>Not Implemented</strong>.
(Associated with an HTML status of 501)<br />
<br />
</p></td>
</tr>
<tr class="even">
<td><p><strong>error502.html</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs</em></p></td>
<td></td>
<td><p>Contains the default error page that Hyrax will return when the
client request generates an <strong>Bad Gateway</strong>. (Associated
with an HTML status of 502)<br />
<br />
</p></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>




### CSS Files

<table>
<tbody>
<tr class="odd">
<td><p><u>File</u></p></td>
<td><p>      </p></td>
<td><p><u>Location</u></p></td>
<td><p>      </p></td>
<td><p><u>Description</u></p></td>
</tr>
<tr class="even">
<td><p><strong>contents.css</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/css</em></p></td>
<td></td>
<td><p>The contents.css style sheet provides the default colors and
fonts used in the Hyrax site. It is referenced by all of the HTML and
XSL files to coordinate the visual aspects of the site.<br />
<br />
</p></td>
</tr>
<tr class="odd">
<td><p><strong>thredds.css</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/css</em></p></td>
<td></td>
<td><p>The thredds.css style sheet provides the default colors and fonts
used by the THREDDS component of Hyrax.<br />
<br />
</p></td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>




### Image Files

There are a number of image files shipped with Hyrax. Simply replacing
key image files will allow you to customize the icons and logos
associated with the Hyrax server.

<table>
<tbody>
<tr class="odd">
<td><p><u>File</u></p></td>
<td><p>      </p></td>
<td><p><u>Location</u></p></td>
<td><p>      </p></td>
<td><p><u>Description</u></p></td>
</tr>
<tr class="even">
<td><p><strong>logo.gif</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/images</em></p></td>
<td></td>
<td><p>Main Logo for the directory view (produced by contents.css and
contents.xsl)</p></td>
</tr>
<tr class="odd">
<td><p><strong>favicon.ico</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/images</em></p></td>
<td></td>
<td><p>The cute little icon preceding the URL in the address bar of your
browser. To be used this file needs to be installed into Tomcat <a
href="http://docs.opendap.org/index.php/Hyrax_-_Installation_Instructions#Miscellaneous">as
described here</a></p></td>
</tr>
<tr class="even">
<td><p><strong>BadDapRequest.gif, BadGateway.png,<br />
favicon.ico, folder.png,<br />
forbidden.png, largeEarth.jpg,<br />
logo.gif, nasa-logo.jpg,<br />
noaa-logo.jpg, nsf-logo.png,<br />
smallEarth.jpg, sml-folder.png,<br />
superman.jpg</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/images</em></p></td>
<td></td>
<td><p>These files are referenced by the default collection of web
content files (described above) that ship with Hyrax.</p></td>
</tr>
</tbody>
</table>




### XSL Transform Files

These files are used to transform XML documents used by Hyrax. Some
transforms operate on source XML from internal documents such as BES
responses. Other transforms change things like THREDDS catalogs into
HTML for browsers.

*All of these XSLT files are software, and should be treated as such.
They are intimately tied to the functions of Hyrax. The likelihood that
you can change these files and not break Hyrax is fairly low.*

<font size="+1" style="bold">Current Operational XSLT</font>

<table>
<tbody>
<tr class="odd">
<td><p><u>File</u></p></td>
<td><p>      </p></td>
<td><p><u>Location</u></p></td>
<td><p>      </p></td>
<td><p><u>Description</u></p></td>
</tr>
<tr class="even">
<td><p><strong>catalog.xsl</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/xsl</em></p></td>
<td></td>
<td><p>The catalog.xsl file contains the XSLT transformation that is
used to transform BES showCatalog responses into THREDDS
catalogs.</p></td>
</tr>
<tr class="odd">
<td><p><strong>contents.xsl</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/xsl</em></p></td>
<td></td>
<td><p>The contents.xsl file contains the XSLT transformation that is
used to build the <a
href="http://docs.opendap.org/index.php/ServerDispatchOperations#OPeNDAP_Directory_Response">OPeNDAP
Directory Response</a> (<em>see <a
href="http://docs.opendap.org/images/6/6e/DirectoryView.png">img</a></em>)<br />
<br />
</p></td>
</tr>
<tr class="even">
<td><p><strong>dap_3.2_ddxToRdfTriples.xsl</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/xsl</em></p></td>
<td></td>
<td><p><em>Experimental</em> - This XSLT is used to produce an RDF
representation of a DAP 3.2 DDX.</p></td>
</tr>
<tr class="odd">
<td><p><strong>dataset.xsl</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/xsl</em></p></td>
<td></td>
<td><p>This transform is used to in conjunction with the
opendap.threddsHandler code to produce HTML pages of THREDDS catalog
dataset element details.</p></td>
</tr>
<tr class="even">
<td><p><strong>error400.xsl</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/xsl</em></p></td>
<td></td>
<td><p>The error400.xsl contains the XSLT transformation that is used to
build the web page that is returned when the server generates a Bad
Request (400) HTTP status code. If for some reason this page cannot be
generated then the HTML version
(<em>$CATALINA_HOME/content/opendap/docs/error400.html</em>) will be
sent.<br />
<br />
</p></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><p><strong>error500.xsl</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/xsl</em></p></td>
<td></td>
<td><p>The error400.xsl contains the XSLT transformation that is used to
build the web page that is returned when the server generates a Internal
Server Error (500) HTTP status code. If for some reason this page cannot
be generated then the HTML version
(<em>$CATALINA_HOME/content/opendap/docs/error500.html</em>) will be
sent.<br />
<br />
</p></td>
</tr>
<tr class="odd">
<td><p><strong>thredds.xsl</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/xsl</em></p></td>
<td></td>
<td><p>This transform is used to in conjunction with the
opendap.threddsHandler code to produce HTML pages of THREDDS catalog
details.</p></td>
</tr>
<tr class="even">
<td><p><strong>version.xsl</strong></p></td>
<td></td>
<td><p><em>$CATALINA_HOME/content/opendap/docs/xsl</em></p></td>
<td></td>
<td><p>This transform is used to provide a single location for the Hyrax
version number shown in the public interface.</p></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>


<font size="+1" style="bold">Experimental XSLT</font>


{\| \|<u>File</u> \|       \|<u>Location</u> \|      
\|<u>Description</u> \|- \| **dapAttributePromoter.xsl** \| \|
*\$CATALINA_HOME/content/opendap/docs/xsl* \| \| *Experimental* - This
XSLT file can be used to promote DAP Attributes whose names contain a
namespace prefix to XML elements of the same name os the Attribute. *Not
currently in use.* \|- \| **dapAttributesToXml.xsl** \| \|
*\$CATALINA_HOME/content/opendap/docs/xsl* \| \| *Experimental* - This
XSLT file might be used to promote DAP Attributes encoded with special
XML attributes to represent any XML to the XML the Attribute was encoded
to represent. *Not currently in use.* \|- \|
**dap_2.0_ddxToRdfTriples.xsl** \| \|
*\$CATALINA_HOME/content/opendap/docs/xsl* \| \| *Experimental* - This
XSLT can be used to produce an RDF representation of a DAP2 DDX. *Not
currently in use.* \|- \| **dap_3.3_ddxToRdfTriples.xsl** \| \|
*\$CATALINA_HOME/content/opendap/docs/xsl* \| \| *Experimental* - This
XSLT can be used to produce an RDF representation of a DAP 3.3 DDX. *Not
currently in use.* \|- \| **namespaceFilter.xsl** \| \|
*\$CATALINA_HOME/content/opendap/docs/xsl* \| \| *Experimental* - This
XSLT can be used to filter documents so that only elements in a
particular namespace are returned. *Not currently in use.* \|- \|
**wcs_coveragePage.xsl** \| \|
*\$CATALINA_HOME/content/opendap/docs/xsl* \| \| *Experimental* - This
XSLT is used by the prototype CEOP WCS gateway client to produce an HTML
page with coverage details. *Not currently in use.* \|- \|
**wcs_coveragesList.xsl** \| \|
*\$CATALINA_HOME/content/opendap/docs/xsl* \| \| *Experimental* - This
XSLT is used by the prototype CEOP WCS gateway client to produce an HTML
page with a list of available coverages. *Not currently in use.* \|- \|
**xmlToDapAttributes.xsl** \| \|
*\$CATALINA_HOME/content/opendap/docs/xsl* \| \| *Experimental* - This
XSLT can be used to covert any XML content into a set of specially
encoded DAP Attributes. The resulting Attribute elements have XML *type*
attributes that are not currently recognized by any OPeNDAP software.
*Not currently in use.* \|- \|}


# Software Customization

## OLFS Customization

[Power Point Presentation From the 2007 Software Development Workshop
hosted by the Australian Bureau of
Meteorology.](http://www.opendap.org/support/bom_sdw/SDW_2r0_OLFSExtensions.ppt)


## BES Customization

[Power Point Presentation From the 2007 Software Development Workshop
hosted by the Australian Bureau of
Meteorology.](http://www.opendap.org/support/bom_sdw/SDW_4r0_BESExtensibility.ppt)
