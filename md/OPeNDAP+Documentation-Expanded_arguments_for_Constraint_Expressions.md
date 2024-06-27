[\<\< back](Development#General_Development "wikilink")

## Use Cases

#### Pass in a Constraint Expression (CE) using POST

1.  The DAP client builds a CE using the existing syntax as a UTF-8
    string
2.  The client binds the POST information (request document body) to the
    URL and dereferences the URL
3.  The server detects an inbound request using POST
4.  The server rejects the POST request if the size of the request
    document body is larger then some upper limit set in a configuration
    file, returning an HTTP error code to the client
5.  The server processes the request and returns a response
6.  The client reads the response

#### Pass in a CE using both POST and GET

This is a variant on the first use case; the DAP client must combine the
request document body with the query string and use them both with POST.

#### Build an array as an argument for a server function

1.  The BES evaluates a server function
2.  If the server function takes an array as an argument, it should use
    a new function named make_array()
3.  The make_array() function takes 2 arguments, specifying the type and
    shape of the array, followed by M values that are the elements of
    that array in row major order.
    1.  The type and shape arguments are all string arguments
    2.  The type must be the name of a value DAP2 simple type (Byte,
        ..., Float64).
    3.  The type name does not have to be quoted
    4.  The shape will be a valid array shape subexpression of the form
        "\[size0\]\[size1\]...\[sizeN\]" where the size0, etc. is an
        unsigned integer and size0 \* size1 \* ... \* sizeN is M
    5.  The shape expression will be quoted
    6.  The element values will be strings that will convert into
        elements of type *type*
    7.  The element values do not have to be quoted
4.  make_array will build a DAP2 Array with the values and return a
    BaseType\* to that array
5.  The current expression evaluator will pass that Array \* into the
    outer function.

## Definitions

## Background

This should be completed before 26 June 2013.

The MIIC project would like to be able to pass modest sized arguments
into server functions they have defined. The arrays should be passed
into the functions as single BaseType Array objects, not as collections
of 100s ++ of BaseType objects.

## Design

### Constraints that are large

POST support in the OLFS and passing large XML documents back to the
BES.

### DAP Array arguments in CE functions

There are two ways to implement this: First using a function that takes
a long series of BaseType objects that hold individual values and using
those to build a single array, and second, to modify the CE evaluator so
that a long string of constant values can be formed into a DAP Array
without the intermediate step of storing each of the values in a
BaseType object.

Implementation of the first design is strait forward.

Implementation of the second design is more complicated.

### Client support

Modify *getdap* so that it will from a request using a document instead
of a CE passed in on the comand line. For example, *getdap -D <url> -c
<string>* will be extended so that *getdap -D <url> -f <file>* will make
a *dods* request where the CE will be read from *<file>* and submitted
to *<url>* using HTTP POST.

### A note about using the query string

This was originally a Google doc
(https://docs.google.com/a/opendap.org/document/d/1GWTt64As6q6O74Mg2EMXijSB9oybAR9P-4m6Ydn29_M/edit)
describing the syntax developed by GDS that we supported in Hyrax in mid
2013. It includes a syntax that supports the same semantics but uses the
query string. FWIW

The GDS/F-TDS/Hyrax Syntax:

<http://>\<machine/<path>\[_expr{#1}{#2}{#3}\].<response_object>\[?DAP2_constraint\]

Where:

1.  A URL to be referenced in \#2. This provides a way for a single call
    to use values from two (or more?) datasets. \[NB: This same idea was
    represented in the original DAP2 syntax using as well but dropped
    because it was never used and presented some complex security
    issues. For those reasons we ignore anything here
2.  An expression, using the syntax of the underlying compute engine
    (Ferret or GDS). In GDS the result of the expression(s) is stored in
    a GDS variable named result while in F-TDS the expression itself
    names the variable using Ferret's LET reserved word. In Hyrax, this
    is one or more calls to a server function where the functions are
    responsible for naming the results and the expression evaluator
    stores the results in a DAP2 DDS object/response.
3.  This contains limits on the domain of the values used when
    evaluating the expression in \#2. The limits are typically in terms
    of latitude, longitude, time, ...

This is used only be GDS; F-TDS uses its lazy evaluation to achieve the
same goal and Hyrax uses a similar technique.

Some features of this syntax

Layering constraints onto previous requests

It is possible to provide an expression (using argument \#2) and make
several requests for both metadata and data. For example:

``` HTML
http://machine/dataset_expr{}{ugrid_subset(...)}{}.das;
http://machine/dataset_expr{}{ugrid_subset(...)}{}.dds; and then
http://machine/dataset_expr{}{ugrid_subset(...)}{}.dods; or
http://machine/dataset_expr{}{ugrid_subset(...)}{}.dods?u,v,lat,lon
```

where it might be that dataset processed by ugrid_subset() has lots more
variables. The first call gets them all while the second call gets only
four.

The implication is that the web service can do this without running the
function four times!

Domain restrictions

All of the compute engines have some way of limiting the extent of the
domain over which the function is evaluated, where domain is meant both
in the sense of Earth-Science domain like latitude and longitude and in
the mathematical sense of a function being a mapping of a domain onto a
range.

Another class of operations that are implied by this syntax are range
restrictions (range in the mathematical sense).

A note about security

The feature provided by \#1 opens servers to working with non-local
data. This is a known vulnerability for several of the servers in
question because they use libraries to read data files that have known
problems. Avoid this.

A note about syntax

There are a number of different syntax choices that could be made to
represent this. Weighing on this is the decision by the OPULS/DAP4 team
to drop the non-standard syntax it uses for constraints. In DAP4, we
will use much more of what the URL query-string can provide. One way to
map this syntax onto that is to adopt:

<http://machine/path>.<response>\[?\[other=#1\] & \[expr=#2\] &
\[limits=#3\]\]

where the \[\]s indicate optional elements and the (somewhat broken)
syntax indicates that other, expr and limits are all optional (and other
is still a really bad idea ;-). This syntax could (would?) be augmented
by the DAP4 constraint expression syntax that will use additional
identifiers concatenated using the standard syntax element &. This will
provide a way around issues like non-standard use of {} or & and will
create an extension mechanism that that's easy to document. The syntax
is extensible because, within the scope of the syntax/specification,
other groups can add new keys. Upstream HTTP software 'knows' that
things in the query-string follow a certain syntax that should otherwise
be ignored and servers can be written to either ignore or return an
error when presented with keys they do not recognize.

Suggestion

I have suggestions about a syntax from the perspective of DAP4:

Drop the explicit references to non-local data. As powerful an idea as
that is, it's too hard to secure and will leave the resulting effort
open to the criticism that it contains a 'structural' flaw. Combine the
expr (#2) and limits (#3) into a single expr key where each key combines
the notion of a projection that describes some value and some set of
filters that may apply to either domain, range or both. Allow several
functions to be run and return distinct values that will be 'packaged'
into a single result-set that is returned. This could be accomplished by
listing the key=param clauses one after another. Include in the name of
the key a notion of the kind of expression it accepts. For example,
<http://>... .dods?ferret_expr=<expr>. Then looking to see if a
particular server accepts ferret_expr tells you quite a bit about what
you can do. It is not hard to move from this notion of using GET to POST
where the request document body contains these same key-param pairs
(separated by newlines, e.g.)

## Deliverables

A new version of Hyrax on the trunk such that a special branch can be
formed for the MIIC project to use. Once the feature is stable, make a
Hyrax 1.9 branch which should include this feature.

### OLFS

To configure the OLFS so that it will accept requests using POST, you
first need to set the *olfs.xml* file in the *content* directory. Add
the following to the end of the configuration file, after the closing of
*<HttpGetHandlers>*:

``` xml
<HttpPostHandlers>
    <Handler className="opendap.bes.dapResponders.DapDispatcher" >
        <AllowDirectDataSourceAccess />
        <MaxPostBodyLength>500</MaxPostBodyLength>
        <!-- UseDAP2ResourceUrlResponse / -->
     </Handler>
</HttpPostHandlers>
```

When a client sends a request using POST, the format of the request
document body is a function of the value of the request's *Content-Type*
header. If *Content-Type* is *application/x-www-form-urlencoded*, then
the request body should start with the literal *ce=* (or *dap:ce=*) and
then contain the constraint expression. If *Content-Type* is not set, do
not include the *ce=* prefix. In both cases the syntax of the CE is
unchanged.

**Important**: Make sure you configure the POST handler and the DAP
Dispatch handler with the same optional values.

**Note**: If you are using *curl* to test this (or as a client), it will
set *Content-Type* for you (to *application/x-www-form-urlencoded*) so
you must use the *ce=* literal.

### Constraints

There are two ways to make arrays that can be passed into DAP2 server
functions. The first way, and the slower of the two, is to use a
function called *make_array()* as an argument to another function. The
faster of the two ways to pass an array of constant values into a server
function is to use the new *constant array* special form.

#### The make_array() function

The *make_array()* function takes three or more arguments and returns a
DAP2 Array with the values passed to the function.

make_array(<type>, <shape>, <values>, ...): <type> is any of the DAP2 numeric types (Byte, Int16, UInt16, Int32, UInt32, Float32, Float64); <shape> is a string that indicates the size and number of the array's dimensions. Following those two arguments are N arguments that are the values of the array. The number of values must equal the product of the dimension sizes.

Example: make_array(Byte,"\[4\]\[4\]",2,3,4,5,2,3,4,5,2,3,4,5,2,3,4,5)
will return a DAP2 four by four Array of Bytes with the values 2, 3, ...
. The Array will be named *g<int>* where <int> is 1, 2, ..., such that
the name does not conflict with any existing variable in the dataset.
Use *bind_name()* to change the name.

This function can build an array with 1024 X 1024 Int32 elements in
about 4 seconds.

#### The array constant special forms

These special forms can build vectors with specific values and return
them as DAP2 Arrays. The Array variables can be named using the
*bind_name()* function and have their shape set using *bind_shape()*.

\$<type>(<size hint>: <values>, ...): The *\$<type>* (*\$Byte*, ...) literal starts the special form. The first argument <size_hint> provides a way to preallocate the memory needed to hold the vector of values. Following that, the values are listed. Unlike *make_array()*, it is not necessary to provide the exact size of the vector; the size hint is just that, a hint. If a size hint of zero is supplied, it will be ignored. Any of the DAP2 numeric types can be used with this special form. This is called a 'special form' because it invokes a custom parser that can process values very efficiently.

Example: \$Byte(16:2,3,4,5,2,3,4,5,2,3,4,5,2,3,4,5) will return a one
dimensional (i.e., a vector) Array of Bytes with values 2, 3, ... . The
vector is named *g<int>* just like the array returned by make_array().
The vector can be turned in to a N-dimensional Array using
*bind_shape()* using
''bind_shape("\[4\]\[4\]",\$Byte(16:2,3,4,5,2,3,4,5,2,3,4,5,2,3,4,5)).

The special forms can make a 1,047,572 element vector on Int32 in 0.4
seconds.

#### The bind_name() and bind_shape() functions

These functions take a BaseType\* object and bind a name or shape to it
(in the latter case the BaseType\* must be an Array\*). They are
intended to be used with make_array() and the \$type special forms, but
they can be used with any variable in a dataset.

bind_name(<name>,<variable>): The <name> must not exist in the dataset; <variable> may be the name of a variable in the dataset (so this function can rename an existing variable) or it can be a variable returned by another function or special form.
bind_shape(<shape expression>,<variable>): The <shape expression> is a string that gives the number and size of the array's dimensions; the <variable> may be the name of a variable in the dataset (so this function can rename an existing variable) or it can be a variable returned by another function or special form.

### Performance measurements

Time to make 1,000,000 (actually 1,048,576) element Int32 array using
the special form, where the argument vector<int> was preset to 1,048,576
elements. Times are for 50 repeats.

Summary: Using the special for \$Int32(size_hint, values...) is about 10
times faster for a 1 million element vector than
make_array(Int32,\[1048576\],values...). As part of the performance
testing, the scanner and parser were run under a sampling runtime
analyzer ('Instruments' on OS/X) and the code was optimized so that long
sequences of numbers would scan and parse more efficiently. This
benefited both the make_array() function and \$type() special form.

#### Raw timing data

In all cases, a 1,048,576 element vector of Int32 was built 50 times.
The values were serialized and written to /dev/null using the command
*time besstandalone -c bes.conf -i bescmd/fast_array_test_3.dods.bescmd
-r 50 \> /dev/null* where the *.bescmd* file lists a massive constraint
expression (a million values). The same values were used.

These values are using the ce_expr parser & scanner before it was
optimized.

|                   | Time in seconds |
|-------------------|-----------------|
| What              | Real            |
| \$type, with hint | 37.975          |
| \$type, no hint   | 39.388          |
| make_array()      | 212.697         |

Runtimes for make_array() and \$type, before scanner/parser optimization

The scanner was improved based on info from 'instruments', an OS/X
profiling tool. Copying values and applying www2id escaping was moved
from the scanner, where it was applied it to every token that matched
SCAN_WORD, to the parser, where it was used only for non-numeric tokens.
This performance tweak makes a big difference in this case since there
are a million SCAN_WORD tokens that are not symbols.

|                   | Time in seconds |
|-------------------|-----------------|
| What              | Real (s)        |
| \$type, with hint | 19.844          |
| \$type, with hint | 19.817          |
| \$type, no hint   | 19.912          |
| \$type, no hint   | 19.988          |
| make_array()      | 195.332         |
| make_array()      | 197.900         |

Runtimes for make_array() and \$type, scanner/parser optimized, two
trials

### Testing

**Note**: To test the \#*type* special form using a browser, make sure
to escape the *\#* character. That is, type *%23Byte(...* instead of
*\#Byte(...*. The *\#* character is reserved by
[RFC3986](http://tools.ietf.org/html/rfc3986).

One of the issues with testing POST responses is having a client that
will use the HTTP POST verb. I'll show how to use *curl*.

There are two ways to POST with curl: Using data from the command line
and use data from a file.

To POST using data from the command line, use the *--data* switch.
Here's an example:

``` sh
curl --data "ce=#Byte(4:2,3,4,5)" http://localhost:8080/opendap/hyrax/data/nc/fnoc1.nc.ascii

Dataset: fnoc1.nc
g6, 2, 3, 4, 5
```

To POST using daa from a file, first store the data in a file:

``` sh
echo "ce=#Byte(4:2,3,4,5)" > POST.data.txt
```

and then use the -X and -d switches:

``` sh
curl -X POST -d @POST.data.txt http://localhost:8080/opendap/hyrax/data/nc/fnoc1.nc.ascii

Dataset: fnoc1.nc
g5, 2, 3, 4, 5
```

## Period of use

26 June 2013 onward