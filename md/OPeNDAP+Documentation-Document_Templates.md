To use the templates, you include the template using MediaWiki's {{ ...
}} syntax. In a normal template substitution, the mediawiki rendering
engine goes and gets the template text every time the page is rendered.
If, however, the template name is preceded with *subst:* then the
mediawiki markup is copied to the destination page and can be edited
without affecting, or being affected by, the original template code. See
<http://en.wikibooks.org/wiki/MediaWiki_User_Guide/Templates> and
<http://www.mediawiki.org/wiki/Help:Templates>

*To include the markup for a skeleton use case, as defined by the
template below, include **{{subst:Template:Use Case}}** in a blank page
and then Save the page.*

## Current Document Templates

- [Template:Use Case](Template:Use_Case "wikilink")
- [Template:Project
  Description](Template:Project_Description "wikilink")
- [Template:Handler
  Documentation](Template:Handler_Documentation "wikilink")
- [Template:DAP4 Design
  Proposal](Template:DAP4_Design_Proposal "wikilink")