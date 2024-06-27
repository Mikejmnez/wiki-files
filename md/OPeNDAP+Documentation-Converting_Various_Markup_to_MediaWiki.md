# Converting HTML

## Using Perl on your own computer

Grab the
[HTML-WikiConverter](http://search.cpan.org/dist/HTML-WikiConverter/),
build it and follow the directions. You will need to supply the HTML
file names on the command line and capture the output (the program
writes to standard output) and then open a MediaWiki page for editing
and paste the MediaWiki markup. Fairly painless unless you're working
with a complex, multipage document.

NB: This coverter does not like <code>

<div>

</code> tags.

## Using online tools

These are web pages (CGIs) which run the HTML-WikiConverter software, so
any limitations of the above software apply to this as well. However,
they save you the trouble of getting the code and building it.

- This is the best converter, since it lets you paste a URL or the HTML:
  <http://diberri.dyndns.org/wikipedia/html2wiki/index.html>

<!-- -->

- These require you to paste the HTML:
  - This uses plain HTTP: <http://labs.seapine.com/htmltowiki.cgi>
  - This uses HTTPS: <https://wiki.uww.edu/converter.php>

# Converting TWiKi

Use
[Twiki2mediawiki](http://wiki.ittoolbox.com/index.php/Code:Twiki2mediawiki)
to convert TWiKi pages to MediaWiki markup. Once converted, use copy and
paste to move the new markup into the pages after opening the page's
edit window.

# Converting Latex

We're making modifications to Latex2MediaWiki.py so that it will support
our particular latex styles. This script can be found in our subversion
archive at
<http://scm.opendap.org:8090/trac/browser/trunk/Doc/Latex2wiki.py>.

# More info...

See the [page on
WikiPedia](http://en.wikipedia.org/wiki/Wikipedia:Tools/Editing_tools#Wikisyntax_conversion_utilities)
about conversions like Word to MediaWiki, CSV to MediaWiki, et c.