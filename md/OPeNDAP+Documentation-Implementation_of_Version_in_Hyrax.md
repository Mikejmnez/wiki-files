<font color="red" size="+">OBSOLETE</font>:[Jimg](User:Jimg "wikilink")
15:28, 14 November 2011 (PST)

### Implementation of Version in Hyrax

We've implemented all three of the options in libdap/BES. The MIME
header technique has been in the Hyrax server for well over two years.
In fact it has been the hassles with using that that lead to the
development of a way to specify the DAP protocol of the request (or the
desired protocol for the response) that drove the design of the
*keyword* and *projection function* approaches.

#### MIME header

Note that MIME headers for Hyrax responses are written by the OLFS, not
the BES or libdap (libdap contains support for this because some other
projects use the BES without a front end).

#### Projection Function

I added a C++ function *function_dap* with the type signature ***void
function_dap(int argc, BaseType \*argv\[\], DDS &dds,
ConstraintEvaluator &)*** (the type of a *projection function*) that
takes a numeric argument and sets the DAP protocol version in the DDS
object from libdap. Pretty straightforward. Here's how it works.

- The DDS class registers the C++ function *function_dap* with the
  *ConstraintEvaluator* instance during object instantiation.
- The function is bound to the name *dap* at the time of registration.
  See ConstraintEvaluator::add_function.
- During DDS instantiation, the DDS sets its default protocol level to
  DAP 2.0.
- When a constraint Expression is parsed, if the *dap()* function is
  called, its argument is used to set the value of the DAP protocol in
  the DDS.


Notes:

1.  The *projection function* typedef takes the DDS as an argument, so a
    projection function can modify the DDS.
2.  The constraint evaluator will wrap constants (e.g., 3.2) in the
    appropriate DAP data type.
3.  Given that projection functions are evaluated *during* the parse,
    the new value of the protocol is present for the remainder of the
    parse and all of the subsequent operations.
4.  The DDS does not include methods to trigger the formation of a 'DDS
    response object.' That code is in ResponseBuilder, see
    ResponseBuilder::send_dds(), et cetera. In there, the response MIME
    headers are built and that's where the XDAP header is built that
    indicates the protocol version of the response. Thus libdap/BES can
    build the MIME headers for the response object.
5.  In Hyrax, the MIME headers are not build by libdap/BES but instead
    by the OLFS.
6.  As part of this feature I cloned and then refactored *DODSFilter*,
    removing most of the excess code that was present to support the
    CGI-based server. The resulting new class is called
    *ResponseBuilder*

#### Keyword

I added a little code to *ResponseBuilder* to parse the CE before it is
feed to the 'real' parser. If a word is found that is a keyword, then it
is set in the *ResponseBuilder* object and removed from the CE. I have
not completed the implementation of this so that the keywords are used
to set different property values since it seems the *projection
function* technique is actually better (because it requires no change to
the parsing of the CE). But doing so would be pretty easy.

I think the keyword approach ultimately won't be used, but it's in the
code for now.