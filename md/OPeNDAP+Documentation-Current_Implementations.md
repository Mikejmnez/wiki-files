## The GrADS Data Server (GDS)

#### Institution

The [Center for Ocean-Land-Atmosphere
Studies](http://www.iges.org/cola.html) of the [Institute for Global
Environment and Society](http://www.iges.org/aboutiges.html)

#### Documentation

[Overview](http://www.iges.org/grads/gds/index.html) and details about
[server-side
processing](http://www.iges.org/grads/gds/doc/user.html#2b).

#### Implementation Details

A Java Servlet implemented on top of the bespoke [Anagram
Framework](http://grads.iges.org/anagram/) using the Java Runtime
environment to execute the [GrADS](http://www.iges.org/grads/grads.html)
application to perform the data access and analysis and using the [Java
OPeNDAP library](http://www.opendap.org/swJava1.1/) for DAP
communication.

#### Functions

Any valid [GrADS
expression](http://www.iges.org/grads/gadoc/expressions.html).

#### Syntax

<http://machine_name:9090/dods/_expr_> followed immediately by three
sets of curly braces containing arguments, as follows:

{dataset1,dataset2,...}{*expression*}{x1:x2,y1:y2,z1:z2,t1:t2}

where the first set of braces contains a list of data sets to be used in
the analysis, *expression* is any valid GrADS expression operating on
the variables in the scope of the referenced data sets and the thirds
set of braces is the boundaries for the evaluation of the expression in
world coordinates.

## The Ferret-Thredds Data Server (F-TDS)

#### Institution

#### Documentation

#### Implementation Details

#### Functions

#### Syntax

## Ingrid

#### Institution

The [Data Library](http://iridl.ldeo.columbia.edu/) of the [The
International Research Institute for Climate and
Society](http://portal.iri.columbia.edu/)

#### Documentation

[Overview](http://iridl.ldeo.columbia.edu/dochelp/Documentation/)

- [Function
  Menu](http://iridl.ldeo.columbia.edu/dochelp/Documentation/funcmenu.html)
- [Function
  Ontology](http://iridl.ldeo.columbia.edu/ontologies/functions.owl)
- [Function Ontology with Ingrid
  Functions](http://iridl.ldeo.columbia.edu/ontologies/functions_ingrid.owl)

<!-- -->

- [Function Ontology
  Browse](http://iridl.ldeo.columbia.edu/ontologies/browse.pl?node=http%3A%2F%2Firidl.ldeo.columbia.edu%2Fontologies%2Ffunctions.owl&repository=dl-owl)

#### Implementation Details

Mix of FORTRAN/C implementing a delayed-execution data-flow
architecture. Supports a number of data formats/exchange protocols.
Local code not under wide distribution.

#### Functions

Any Ingrid expression, which is RPN grammer on top of a large library of
words (i.e. functions). There are many functions which operate on
variables/datasets of which currently 166 are documented [detailed
here](http://iridl.ldeo.columbia.edu/dochelp/Documentation/funcmenu.html).
This documentation is generated from a semantic framework which is
intended to help map interoperable functions.

#### Syntax

A URL-based (not in the query string) RPN notation.

For example:

<http://iridl.ldeo.columbia.edu/expert/SOURCES/.NOAA/.NCEP/.CPC/.GMSM/.w/5/add/>

SOURCES .NOAA .NCEP .CPC .GMSM .w (the OPeDNAP data variable)

5 add (the constant 5; the operation desired)

[<http://iridl.ldeo.columbia.edu/expert/SOURCES/.NOAA/.NCDC/.ERSST/.version2/.SST/X/(170W)(120W)RANGE/Y/(5S)(5N)RANGE%5BX/Y%5Daverage/>](http://iridl.ldeo.columbia.edu/expert/SOURCES/.NOAA/.NCDC/.ERSST/.version2/.SST/X/%28170W%29%28120W%29RANGE/Y/%285S%29%285N%29RANGE%5BX/Y%5Daverage/)
(The actual URL is usually encoded. See the link contents.)

SOURCES .NOAA .NCDC .ERSST .version2 .SST (The OPeNDAP data variable)

X (170W) (120W) RANGE (A range in X of the data variable's underlying
grid in world coordinates)

Y (5S) (5N) RANGE (A range in Y of the data variable's underlying grid
in world coordinates)

\[X Y\]average (the dimensions to be operated on; the operation)

In this case, the OPeNDAP variable is three dimensional (X Y T), so
after averaging we are left with a one dimensional variable (depending
on T).

If we had alternatively averaged only over X

\[X\] average

we would be left with a two-dimensional variable (depending on Y and T).

An OPeNDAP service is created at any point by appending dods, e.g.
dods.das,dods.dds,dods.dods -- this is, of course, index-based
subsetting. Users do not normally do index-based subsetting explicitly,
though there are methods within the independent variables (first,
second, secondtolast, last, valuetoindex, indextovalue) that would make
it possible to do so.

## pyDap

#### Institution

Independent implementation by Roberto De Almeida, initially developed at
the [Oceanographic Institute](http://www.io.usp.br) of the [University
of São Paulo](http://www.usp.br), and currently at the [Center of
Weather Forecast and Climate Studies](http://www.cptec.inpe.br).

#### Documentation

Server-side functions are currently being implemented for the next major
release of pydap (2.3), still under development. There's an
[overview](http://groups.google.com/group/pydap) of the planned
implementation.

#### Implementation Details

A WSGI stack with a pydap-aware middleware that handles server-side
functions. The middleware converts OPeNDAP requests with server calls to
regular data requests and pass them to the pydap server. The data
returned by the server are used by the middleware to complete the
function calls, with the result being passed back to the client.

#### Functions

geogrid and fill_value. Supports an auto-discovery mechanism for
third-party functions installed separately.

#### Syntax

Follows the DAP 2 spec:
<http://example.com/dataset?function(param_1,param_2>,...,param_n).

## SWAMP

#### Institution

#### Documentation

#### Implementation Details

#### Functions

#### Syntax