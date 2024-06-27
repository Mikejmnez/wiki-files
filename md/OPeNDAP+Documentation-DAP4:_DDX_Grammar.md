[\<\<back to OPULS Development](OPULS_Development "wikilink")

**Version: 1.0**

At the end of this document are instructions for accessing and testing a
formal grammar for the DAP4 DDX using the Relax-NG schema language. I
constructed it without any reference to any other explicit or implicit
grammars so I could record my ideas. I have since modified it based
examining the implied grammar in page [DAP4: Data
Model](DAP4:_Data_Model "wikilink") and from comments from others and
from a comparison with the xsd grammar.

**NOTE**: [Jimg](User:Jimg "wikilink") 14:19, 28 August 2012 (PDT) There
is a copy of the dap4.rng file in subversion at
<https://scm.opendap.org/svn/trunk/xml/dap/>. I think that version is
more recent than the dropbox files referenced by this document.

## Differences with DAP4 xsd Grammar

I converted the xsd-based grammar
<https://scm.opendap.org/trac/browser/trunk/xml/dap/dap4.xsd> to an
equivalent relax-ng grammar. <http://dl.dropbox.com/u/53929684/xsd.rng>
(now also at <https://scm.opendap.org/svn/trunk/xml/dap/dap4.rng>)

One major difference I see is in dimension handling.

1.  I just used the name "dimension" rather than "shareddimension"; For
    me, all dimensions (except anonymous ones and variable length) are
    shared.
2.  The xsd separates out scalars from arrays. I always allowed the
    dimensions for a variable to be optional to handle the scalar case.
3.  I attempted to be as consistent as possible, so I allowed any type
    including sequences ~~and structures~~ to be dimensioned.
4.  <del>The dimensions of a variable are currently specified in the rng
    grammar as an attribute named "dimensions" associated with
    "variables": e.g. dimensions="dr d1".
    Previously I used this:

<!-- -->

    <dimensions>
    <dimension name="dr"/>
    <dimension name="d1"/>
    </dimensions>

But this seemed kind of verbose.</del>

Other differences:

1.  The Dataset element in the xsd has a couple of extra attributes. I
    added these.
2.  The xsd appears to allow attributes to themselves have attributes.
    This needs discussion.
3.  I forgot enumerations and opaque. I added them.
4.  The URL basetype is in the xsd. What is the justification for
    keeping it?
5.  It appears that the Dataset contains a top level <group>
    declaration; I chose to treat the Dataset itself as the top-level
    group.
6.  Attribute declarations appear to have their own "namespace"
    attribute. Not sure why this is needed.
7.  I do not understand the purpose of the "NewAttribute" attribute.
8.  ~~The Grid issue, of course~~ There are still some minor differences
    in representing coordinate variables.
9.  The xsd represents attribute values thus:

<Attribute name="a">
`   `<value>`...`</value>
`   `<value>`...`</value>
</Attribute>


I provided an alternate form when there is only one value:

<Attribute name="a" value="..."/>


and I chose to use attributes in the multi-valued case because I prefer
not to use elements with content unless really necessary. So I
represented the above as this.

<Attribute name="a">
`  `<Value value="..."/>
`  `<Value value="..."/>
</Attribute>

There are also some minor differences.

1.  ~~Element names (e.g. <structure>) are capitalized in the xsd
    grammar~~ I modified the rng grammar to capitalize.
2.  There is an issue of interleaving of definitions, or equivalently,
    what elements must occur in a fixed order.
3.  Where should attributes be legal? I think the rng grammar and the
    xsd grammar agree on this: putting them almost everywhere, but it
    needs discussion.

Other differences:

1.  ~~I temporarily suppressed OtherXML because it did not translate
    correctly~~ Fixed.
2.  I dropped Blobtype; I fail to see the need for this.

### Testing the Relax-NG Grammar

**NOTE**: See subversion as described at the top of this page for more
recent versions of the grammar.

You will need to copy three files:

1.  dap4.rng - this is the grammar file; it uses the Relax-NG schema
    language (http://relaxng.org/).
    This can be obtained from
    <http://dl.dropbox.com/u/53929684/dap4.rng>
2.  test.xml - this is a test file, that I am growing to cover the whole
    grammar.
    This can be obtained from
    <http://dl.dropbox.com/u/53929684/test.xml>
3.  jing.jar - Jing is a validator that takes the grammar and a test
    file and checks that the test file conforms to the grammar.
    This can be obtained from
    <http://dl.dropbox.com/u/53929684/jing.jar>.

To use it, do the command:

`java -jar jing.jar dap4.rng test.xml`

No output is produced if the validation succeeds, otherwise, error
messages are produced.

*-Dennis Heimbigner*