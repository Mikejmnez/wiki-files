# Web page customization

Hyrax can be easily customized to fit the end users needs. The public
"face" of Hyrax can be easily modified to match an organizations look
and feel. Almost all of the web pages can be completely customized by
the site administrator by editing a combination of HTML, Image, and CSS
files.

## Where to make the changes

All of the default versions of the HTML, Image, and CSS files come
bundled with Hyrax in the *\$CATALINA_HOME/webapps/opendap/docs*
directory. \$CATALINA_HOME is the directory that you installed tomcat
in. If you did the default install of tomcat, your \$CATALINA_HOME will
be "/usr/local/apache-tomcat-6.x.x".

CD to you \$CATALINA_HOME directory (again default would look like (on
Linux machine): cd /usr/local/apache-tomcat-6.x.x) (The -6.x.x is the
version number of tomcat you are running, ex. if you are running tomcat
6.0.24 then it would be just that.)

Make a new directory \$CATALINA_HOME/content/opendap/docs/. (This would
be: mkdir content, cd content, mkdir opendap ...)

Copy (do not move) the files in \$CATALINA_HOME/webapps/opendap/docs to
\$CATALINA_HOME/content/opendap/docs by using: cp -r
\$CATALINA_HOME/webapps/opendap/docs/
\$CATALINA_HOME/content/opendap/docs/

Warning: *Do NOT remove files from this new directory (Or the old one for that matter). Each file, in its location, is required by Hyrax. You can make changes to the files but you should not rename or remove them.*

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
browser. To be used this file needs to be installed into Tomcat:cp -r
$CATALINA_HOME/webapps/opendap/docs/images/favicon.ico to
$CATALINA_HOME/webapps/ROOT/ (OR this is where you can pick another
image of your choosing and place in this directory.)</p></td>
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






