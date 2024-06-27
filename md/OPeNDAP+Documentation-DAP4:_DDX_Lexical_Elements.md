[\<\<back to OPULS Development](OPULS_Development "wikilink")

This document describes the lexical elements that occur in the DAP4
grammar. It is expected that this definition will end up in the DAP4
specification document. It is also expected that it will be implemented
by server and client code using some equivalent regular expression
mechanism.

Within the [Relax-NG (rng) DAP4
grammar](http://dl.dropbox.com/u/53929684/xsd.rng), there are markers
for occurrences of primitive type such as integers, floats, or strings.
The markers typically look like this when defining an attribute that can
occur in the DAP4 DDX.

    <attribute name="namespace"><data type="string"/></attribute>

The "<data type="string"/>" specifies the lexical class for the values
that this attribute can have. In this case, the namespace attribute is
defined to have a String value. Similar notation is used for values
occurring as text within an xml element. The lexical specification later
in this document defines the legal lexical structure for such lexical
items. Specifically, it defines the format of the following lexical
items.

1.  Constants, namely: string, float, integer, and character.
2.  Identifiers

The specification is written using the ISO/IEC 9945-2:2003 Information
technology -- Portable Operating System Interface (POSIX) -- Part 2:
System Interfaces (see
[1](http://www.iso.org/iso/iso_catalogue/catalogue_ic/catalogue_detail_ics.htm?csnumber=38790)).
This is the extended Posix regular expression specification.

I have augmented it in the following ways.

1.  Names are assigned to regular expressions using the notation
    *name = regular-expression*
2.  Named expressions can be used in subsequent regular expressions by
    using the notation {name}. Such occurrences are equivalent to
    textually substituting the expression associated with name for the
    {name} occurrence: More or less like a macro.

### DAP4 Lexical elements

Notes:

1.  The definition of {UTF8} is deferred to the next section.
2.  Comments are indicated using the "//" notation.
3.  Standard xml escape formats (&xDD) are assumed to be allowed
    anywhere.

**Basic character set definitions**

    CONTROLS   = [\x00-\x1F] // ASCII control characters
    WHITESPACE = [ \r\t\f]+
    HEXCHAR    = [0-9a-zA-Z]
    // ASCII printable characters
    ASCII      = [0-9a-zA-Z !"#$%&'()*+,-./:;<=>?@[\\\]\\^_`|{}~]

**Ascii characters that may appear unescaped in Identifiers**
This is assumed to be basically all ASCII printable characters except
the characters ' ', '.', '/', '"', ' ' ', and '&'. Occurrences of these
characters are assumed to be representable using the standard xml '&xx;'
notation.

    IDASCII    = [0-9a-zA-Z!#$%'()*+,-:;<=>?@[\\\]\\^_`|{}~]

**The numeric classes: integer and float**

    INTEGER    = {INT}|{UINT}|{HEXINT}
    INT        = [+-][0-9]+{INTTYPE}?
    UINT       = [0-9]+{INTTYPE}?
    HEXINT     = {HEXSTRING}{INTTYPE}?
    INTTYPE    = ([BbSsLl]|"ll"|"LL")
    HEXSTRING  = (0[xX]{HEXCHAR}+)
    FLOAT      = ({MANTISSA}{EXPONENT}?)|{NANINF}
    EXPONENT   = ([eE][+-]?[0-9]+)
    MANTISSA   = [+-]?[0-9]*\.[0-9]*
    NANINF     = (-?inf|nan|NaN)

**The Character classes**

    STRING     = ([^"\&]|{XMLESCAPE})*
    CHARACTER  = ([^'\&]|{XMLESCAPE})

Note that the character type only supports ASCII characters because it
can only hold a single 8-bit byte.

**The Identifier class**

    ID         = {IDCHAR}+
    IDCHAR     = ({IDASCII}|{XMLESCAPE}|{UTF8})
    XMLESCAPE  = &x{HEXCHAR}{HEXCHAR};

Note that the above lexical element classes are not disjoint. For
example, the sequence of characters 1234 can be either an identifer,a
float, or an integer. So the order of testing is assumed to be this.

1.  INTEGER
2.  FLOAT
3.  ID
4.  STRING

### UTF-8 Character Encodings

We discuss UTF-8 character encoding in the context of this document:
[2](http://www.w3.org/2005/03/23-lex-U).

The most correct (validating) version of UTF8 character set is as
follows.

    UTF8 =   ([\xC2-\xDF][\x80-\xBF])
           | (\xE0[\xA0-\xBF][\x80-\xBF])
           | ([\xE1-\xEC][\x80-\xBF][\x80-\xBF])
           | (\xED[\x80-\x9F][\x80-\xBF])
           | ([\xEE-\xEF][\x80-\xBF][\x80-\xBF])
           | (\xF0[\x90-\xBF][\x80-\xBF][\x80-\xBF])
           | ([\xF1-\xF3][\x80-\xBF][\x80-\xBF][\x80-\xBF])
           | (\xF4[\x80-\x8F][\x80-\xBF][\x80-\xBF])

The lines of the expression cover the UTF8 characters as follows:

1.  non-overlong 2-byte
2.  excluding overlongs
3.  straight 3-byte
4.  excluding surrogates
5.  straight 3-byte
6.  planes 1-3
7.  planes 4-15
8.  plane 16

Note that ASCII and control characters are not included.

The above reference also defines some alternative regular expressions.

The most relaxed version of UTF8 is this.

    UTF8 = ([\xC0-\xD6].)
          |([\xE0-\xEF]..)
          |([\xF0-\xF7]...)

The partially relaxed version of UTF8 is this.

    UTF8    = ([\xC0-\xD6][\x80-\xBF])
            | ([\xE0-\xEF][\x80-\xBF][\x80-\xBF])
            | ([\xF0-\xF7][\x80-\xBF][\x80-\xBF][\x80-\xBF])

We deem it acceptable to use this last relaxed expression for validating
UTF-8 character strings.

### Discussion

[Jimg](User:Jimg "wikilink") 13:05, 28 March 2012 (PDT)

When we get around to this one, I think we will need to decide if the
characters in a variable as represented in the DDX and in a URL's query
string (i.e., the constraint) have to be the same. In particular,
characters like dot (.) and space ( ) present special problems.
Specifically, a dot has a special meaning in the syntax of an identifier
and a space, while it means nothing in the syntax of a constraint, does
odd things to a scanner. What's more, but convention plus (+) is used in
a URL for space while dots are normally not escaped, but would be for
DAP. So there's several layers of escaping that will be done by several
different systems. It's solvable, of course, but I think we might want
two formal definitions for identifiers, one for 'in the DDX' and
equivalent representations and one for 'in the URL'.

I think the idea of a formal spec for identifiers is a very good thing.