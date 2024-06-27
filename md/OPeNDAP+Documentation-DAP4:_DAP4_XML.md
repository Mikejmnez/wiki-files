\[Most recent modifications are underlined or struck out\]

We have agreed to use some form of XML to represent the DXD metadata and
the capabilities document(s). I would like to present some ideas about
the "kind" of XML we use. These are my ideas, and are certainly not
intended to be the last word on this subject.

First, note that our XML is not intended to be a stand-alone document.
It exists in a very specific context and for a specific purpose. This
means that we are not obliged to adhere to any existing XML document
"standards". <ins>Further, there is a strong presumption that it should
be defined for ease of machine processing, not for ease of
reading</ins>.

I believe the rule that should guide our use of XML is to be as simple
as possible and no simpler (to paraphrase Einstein).

Specifically, I think we should consider the following ideas:

1.  no DOCTYPE declarations: We all agree that DOCTYPE things are not
    for this application
2.  ~~no XML declaration~~ We decided that this is of minimal cost and
    it's useful to have the documents work in lots of different
    contexts, if for no other reason than it make development easier.
3.  ~~no namespace declarations.~~ This is useful when these documents
    are used in various contexts

None of the above constructs is necessary because they can all be
inferred from the context.

Additional ideas:
1. We avoid the use of other kinds of cruft such as xlinks. They provide
more structure than is needed for DAP4 and slow up parsing.

<ins>2.Attribute values should never contain structural information.
This means that they can be validated as of some type (simple
identifier, string, integer, etc.) but that they never need to be
parsed. One example of this involves representing the dimensions of a
declared variable. Consider this example.

    <Int32 name="var">
      <Dimensions>
        <Dimension name="/g1/d1"/>
      </Dimensions>
    </Int32>

As opposed to this.

    <Int32 name="var">
      <Dimensions>
        <Dimension name="d1">
          <Path group="g1"/>
        </Dimension>
      </Dimensions>
    </Int32>

In the first example the dimension name "/g1/d1" needs to be parsed to
extract the structure, namely the path to the group containing the
definition of "d1". In the second example, that parsing is not necessary
because it is represented by the "<Path>" element. I propose we use the
second approach.</ins>

*- Dennis Heimbigner*

## Discussion

[Jimg](User:Jimg "wikilink") 16:32, 27 March 2012 (PDT) At the meeting
on 3/27/12 we decided to adopt an *As simple as possible, but no
simpler* stance. We'll decide things like *xlink* on a case-by-case
basis when they come up.