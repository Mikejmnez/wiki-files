[\<\<back to OPULS Development](OPULS_Development "wikilink")

## Background

The character escaping mechanisms in DAP2 have, in retrospect, caused
signficant confusion and led to conflicting implementations.

Character Escaping (aka escapes) occur in several places.

1.  Identifiers: some characters in identifiers (blanks, for example)
    require escaping in certain syntactic situations in order to
    properly be interpreted.
2.  String and character constants: at least the surrounding quote
    character (typically " or ' ) requires some form of escape so that
    it can occur inside string or character constants.
3.  Queries: a number of characters ('&','.',etc) have special meaning
    when they occur as part of a DAP2 query, and so they require
    escaping if they are, for example, part of an identifier or
    constant.

In retrospect, the DAP2 escape mechanism was chosen to be the same as
the standard URL escape mechanism, when a character was converted to two
hex digits and represented as %HH, where H is a hex digit. Especially
when do escapes in queries, this led to confusion about when one was
doing DAP2 escaping and when one was doing URL escaping.

## Proposal

The primary proposal is to ensure that we use escaping mechanisms that
are clearly not the same as the standard URL escaping mechanism.

For DAP4, the problem simplifies significantly because the DDX uses XML,
so we can directly use the standard XML Entity escape mechanism, which
in its most general form is &#DDD;, that is, an ampersand followed by a
sharp followed by some number of decimal digits followed by a semicolon.

In practice, only four escape characters are needed.

1.  & (&)
2.  \> (\>)
3.  \< (\<)
4.  " (")

Note that XML entities may also occur in attribute values, so it can be
used as the general escape mechanism in XML and is quite distinct from
the URL encoding format.

The issue that needs to be addressed is how do to escapes in queries
(i.e, anything after the left-most '?' in the URL. I would propose we
choose one of the two following options.

1.  Standard C/Java, etc '\\ escaping, where encountering \c is changes
    the interpretation of character c.
2.  <ins>Use some other escape mechanism that supports general
    representation as a sequence of hex, octal, or decimal digits
    preceded by some marker. Examples include the following.</ins>
    1.  <ins>XML notation</ins>
    2.  <ins>\xHH notation</ins>
    3.  <ins>%HH notation (seriously deprecated because of the problems
        we saw with DAP2.</ins>

<ins>After some discussion, James and Dennis believe that using the
backslash escape is the correct choice.</ins>

## Discussion

<ins>Using the backslash escaping mechanism has the advantage that it is
well known and is easy to parse. It has the disadvantage that it still
leaves the offending character in the string (i.e. "\\", for example
still leaves the '/' as part of the string; not a big point, but may be
worth thinking about to see if it causes other problems.</ins>

Using, for example, the XML escape mechanism has the advantage of
consistency. It is, however, slightly more complicated to parse. It is
also somewhat more difficult for a user to build by hand a url
containing a query.

*Dennis Heimbigner*

[Jimg](User:Jimg "wikilink") 15:56, 10 April 2012 (PDT) I favor the
backslash (\\ escaping for DAP4 with the assumption that some clients
will apply the HTTP/URL escaping as they see fit. The servers will first
'unescape' for the HTTP encoding. During processing they will use the
DAP4-escaped data as appropriate. For example, a parser for the
constraint expression would need to have dots (.) in variable names
escaped so that it will correctly parse (while 'structural' dots would
not be escaped).

### Why we need two escaping mechanisms

The answer is that we don't - we need only one, but we must realize that
in addition to the DAP4 escaping, some clients will employ HTTP/URL
escaping and it's out of our control. DAP4 will provide the *application
layer* escaping while HTTP may provide *transport layer* escaping. The
clients need know nothing about this. The servers must know if the HTTP
frameworks with which they work will undo the HTTP/URL escaping (Apache
does not). If the serveruses a framework that does not handler the HTTP
escaping, the server becomes responsible for 'undoing' the escaping.

### Alternate proposal

One way to handle the DAP4 escaping is to allow identifier names to be
quoted, like this: *"my variable"."my field"\[0:10\]*

DAP's parsers (scanners, really) can easily deal with this (mine already
do; I forgot about it until just now; it was a bit of a hack ;-) The
only issue is what you do for an identifier with a double quote in its
name. My solution was to backslash escape that (but that's the only
character in an identifier name that has to be escaped, which means that
when a person types a URL, that's the only character they have to
remember to escape). E.G.: *"my variable"."my field"."\\My, my\\, he
said."\[0:10\]* NB: The last period is part of the variable name.

I implemented this a long time ago in the C++ scanners because I needed
a way to write tests w/o going insane WRT HDF4 variable names. Of
source, it looks like hell when HTTP gets ahold of it, but that's not
our problem and, truth be told, plenty of HTTP processing stages are
pretty relaxed about characters. It was/is also a logical extension of
character constants in functions that are really expressions of one sort
or another.