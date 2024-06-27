[\<-- back to OPULS
Development](Development#OPULS_Development "wikilink")

[ndp](User:Ndp "wikilink") 16:25, 24 September 2012 (PDT)

## Background

In DAP2 the constraint expression (CE) was defined as a *projection* and
a *selection*. The *projection* being a list of dataset variables to be
returned, and the *selection* being the conditions that must be met to
return them. . The projected variables could, if they were arrays, be
subset using a square bracket notation (rigorously described elsewhere).
The *selection* was written as a list of "clauses" (my word), separated
from the *projection* and from each other by an "&" character. The
*selection* was applied to all of the requested variables, and in
practice was rarely used because the only legitimate application of the
*selection* was to constrain a Sequence object.

The DAP2 CE consumed the entire URL query string, in other words
everything after the "**?**" in the URL was considered to be the DAP2 CE
string.

## Terms

DAP4 Constraint Expression (CE)
The constraint expression that encapsulates various sub-setting of, and
possibly the application of server side functions to variables in a DAP4
dataset.

<!-- -->

Query String (QS)
Everything after the "<font size="3">`?`</font>" character in a URL.

## Problems addressed

This proposal puts forth a framework for a DAP4 Query String syntax,
which is required for DAP4 the protocol.

Throughout the web the predominate interpretation of the query string is
to view it as a collection of key-value pairs (KVP), where each pair is
separated by an "**&**" character.


<font size="3">`?key=value&key=value&key=value ...`</font>

Many web services utilize this pattern, including our friends at OGC.
Because the DAP2 CE subsumed the entire query string it doesn't fit into
this model. Tomcat (and other web server frameworks) provide specific
API methods for collecting the KVP from the query string, but again DAP2
doesn't play well with this.

DAP4 has many needs (problems to be addressed) by the syntax and
positioning of the CE. This is a place to begin addressing these issues.

## Proposed solution

### Constraint Expression

The DAP4 CE should be held in a single key value pair. The CE value
should be URI encoded\*\* as appropriate for it's character content and
all occurrences of the "**&**" must also be URI encoded. Combined with a
riff on [Dennis' Filter
Proposal](DAP4:_DAP4_Filter_Constraints "wikilink") we could get to
something like this:


`?dap4.ce=n;a[3:1:100];k|x<=y|z=y`

Where we are asking for the variable **n**, the 3rd through the 100th
elements of the array **a**, and all members of the rows of sequence
**k** for which **k.x\<=k.y** and **k.z=k.y**

\*\* In general URIs as defined by [RFC
3986](http://tools.ietf.org/html/rfc3986) may contain any of the
following characters:

    ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-._~:/?#[]@!$&'()*+,;=.

Any other character needs to be encoded with the percent-encoding (%hh).
Each part of the URI has further restrictions about what characters need
to be represented by an percent-encoded word.

### Additional Server Controls

By removing the "**&**" character from the CE we can now use the rest of
the query string to pass server controls and options. Consider if we
were to take my corollary to Dennis' filter proposal and allow filters
to be applied to arrays. The resulting data object could be expressed as
a Sequence, or as a array with a mask applied. A server could support
both and the type of result might be controlled by a KVP. You might even
specify the mask value:


<font size="3">`?dap4.ce=u[25:1:1000][75:1:1000]|u>4.0|u<12.7&dap4.arraySelection=mask(0x00)`</font>

Or, ask for a Sequence:


<font size="3">`?dap4.ce=u[25:1:1000][75:1:1000]|u>4.0|u<12.7&dap4.arraySelection=Sequence`</font>

### Extensibility

We claim the set of all query string keys beginning with the string
"`dap4.`" to be owned by the DAP4 protocol.

Examples:

- "`dap4.ce`" for the constraint expression.
- "`dap4.async`" for asynchronous response control.
- Any other key beginning with "`dap4.`" to give us future
  expandability.

This will allow us to add service controls and inputs over time without
the risk of interfering with other efforts to add service controls and
inputs to various DAP4 servers.

## Examples

Example:


`?dap4.ce=constraintExpressionString&dap4.checksums=off&dap4.arraySelection=mask(0x00)&dap4.saveAs=newgranule&dap4.async=true`

Example:


`?dap4.ce=build_data(place,u[100:200])&dap4.checksums=off&dap4.arraySelection=Sequence&dap4.saveAs=newgranule&dap4.asyncAccepted`

## Rationale for the solution

Not a solution, just a starting point.

## Discussion

This is just a draft to get my ideas down so that we can talk about
them.

[Dennis](User:dmh "wikilink") 9/27/2012
I have a number of comments about this proposal and especially about
issues that need to be addressed.

**Namespaces**:
James made the claim that this proposal did not require namespaces for
keys because (paraphrasing) "...google and facebook don't need it, why
should we".

The reason is that e.g. google has complete control over its servers
hence keyword name conflicts are handled internally to google.

We do not have that luxury. Not only do we have multiple independently
developed servers (e.g. hyrax, thredds, pydap), but each server has
multiple instances across the web: instances not under our
(opendap/unidata) control.

Each of these server types and server instances are likely to have
specific keys that pertain to their operation and are meaningless to
others. Further, there will be keys whose semantics should be the same
globally (i.e. across all servers). I claim that this forces the use of
some kind of namespace mechanism for keys.

There are a number of issues in assigning and using namespaces. First,
there are going to be several important namespaces.

1.  A default namespace.
2.  The namespace implied by the specific server.
3.  A namespace tree of keys whose meaning is intended to be defined by
    organizations other than dap. DAP (opendap+unidata) defined keys
    will be in this space, but provision should be made for other
    organizations to use this tree as well.

Let me propose that \#1 and \#2 be the same. That is, keys with no
special prefix are assumed to be local to the specific server and are
interpreted by the server in any way it wishes. Note, that if it
chooses, a server can define one of its keys to mean the same as some
externally defined (#3) key.

Let me further propose two possible ways to assign the namespace tree to
organizations.

First, we use the Java idea of defining the tree based on reverse url
components. So we might have "edu.colorado.keyx" as a key under the
control of the University of Colorado. For convenience, we might reserve
the url "dap" or the url "." for DAP use. So we might define "dap.ce" or
".ce" as the key to specify a standard constraint as defined in the DAP4
spec.

A second possible namespace tree would be based on the unix filesystem
model with "/". Again, we might reserver the root and/or /dap for DAP
specific use. This gives either "/ce" or "/dap/ce" as the constraint
key. With this model, some organization (presumably opendap+unidata) has
to assign paths in the tree to organizations. In order to reduce the
need for that, we can again use a variant of the url model and have, for
example, "/edu/colorado/keyx" be controlled by the University of
Colorado.

Personally, I prefer the second namespace model (using "/").

> <em> <font color="green"> I added a section to the proposal titled
> "Extensibility" which I believe minimally addresses this. We should
> discuss it and see if you feel more needs to be done. </font>
> [ndp](User:Ndp "wikilink") 13:15, 6 November 2012 (PST) </em>

**Parsing and Escaping**:
A minor point, but note that parsing of the sequence of keyword pairs
will have to be done before %xx escaping is performed. This is because
the significant characters ('=' and '&' and '?') may appear in the value
part of a pair. However, additional escaping using (presumably) '\\
escapes will still be required to handle escaping within values -- when,
for example, constraint expressions contain string constants.

> <em> <font color="green"> I did some testing against Tomcat using curl
> and a browser. I learned the following:
> \# The browser escapes the URL, curl does not (although you may have
> to shell escape components of a URL/CE to get command-line-curl and
> your shell to swallow it)
>
> 1.  If you utilize URL encoding to encode a '&' sign in the query
>     string, then Tomcat will ignore it when breaking the query string
>     into KVPs, but will subsequently decode it before generating the
>     keys and their values.
> 2.  Every '&' in the query string received by Tomcat that is not
>     encoded (i.e. that appears as itself in the query string) will
>     signal the Tomcat URL parser to start to generate a new KVP. I say
>     start because there could be two adjacent ampersands (&&), in
>     which case they are treated as a single ampersand (and Tomcat
>     generates a warning: "WARNING: Parameters: Invalid chunk ''
>     ignored."). Additionally, the content may lack an equals symbol or
>     a value, in which case the entire substring (between ampersands or
>     between the last ampersand and the end of the query string)
>     becomes a key whose value is the empty string.
>     This I think means that we might also consider using simple
>     keywords to control server behavior if we wished - for example
>     instead of saying: <font size="3">`&async=true`</font> we might
>     instead say simply <font size="3">`&acceptAsync`</font> with the
>     server simply testing for the presence (or lack thereof) of the
>     particular key.
>
> All of which pretty much confirms that Dennis' point regarding Parsing
> and Escaping of the DAP4 URL is spot on, and that Tomcat does exactly
> what he suggests. I believe that the specification should make clear
> that clients need to URL escape ( %hh where hh is the hexadecimal
> value of the character being escaped)(uh... UNICODE?) the keys and
> values but not the ampersands that seperate them when making requests
> to DAP4 servers.
> How does that sound?
> </font> [ndp](User:Ndp "wikilink") 15:11, 6 November 2012 (PST) </em>

**Pairs as Map Versus Ordered List**:
Another issue that that should be spelled out involves the answer to the
following two questions:

1.  Is it legal to have multiple occurrences of the same key?
    E.g. is ?x=5&x=6 legal?
2.  Does the order of occurrence of pairs matter?
    E.g. is ?x=5&y=x the same as ?y=x&x=5 ?

> <em> <font color="green"> I believe the short answer to this question
> is: "It depends." I think that when we describe the usage of a KVP
> term that we must explicitly indicate if that particular Key may occur
> multiple times and what happens if it does appear multiple times even
> when such use is not supported. </font> [ndp](User:Ndp "wikilink")
> 15:29, 6 November 2012 (PST) </em>

I will probably have other comments as I think about this more.