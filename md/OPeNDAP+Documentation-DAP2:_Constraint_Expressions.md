## Overview

- The DAP2 interface is via the URL.
- The URL includes the
  - Name of the data set (/data/nc/fnoc1.nc)
  - Name of the desired response (DAS, …)
  - Optional Constraint Expression
- Constraint expression chooses which parts of the dataset (granule) are
  returned
  - If the dataset has several variables, chooses among those
  - If the variables are arrays, subsets those
  - If the are Structures, chooses the fields
  - If there are Sequences, selects values
  - Also runs functions if they are available

### Constraint Expression Syntax

- URL: <http://host/dataset>.<ext>?<ce>
- If <ext> is ‘dds’ or ‘dods’ the <ce> is used to contrain the response
- <ce> is a <projection>\[&<selection>\]\*
- <projection>

  \- is a comma separated list of zero or more:

  - Variables to be returned.
  - Array hyperslabs
  - Structure fields
  - Function calls

### CE Projection Examples

#### Example 1 - Choosing Variables and subsetting arrays

- Using the <http://localhost:8080/data/nc/fnoc1.nc> dataset:


\- ?u,v Chooses the variables u and v

\- ?u\[0\]\[0:5\]\[0:5\] Chooses u and subsamples the array returning a
6 by 6 hyperslab

- Try these in a browser - you do not need to use the virtual machine’s
  server; you can substitute test.opendap.org for localhost


\-
e.g.,http://test.opendap.org/opendap/data/nc/fnoc1.nc.ascii?u\[0\]\[0:5\]\[0:5\]

\- I used ‘.ascii’ instead of ‘.dods’ so the result is easy to see in a
browser

#### Example 2 - Retrieving Map variable data from a Grid

Selecting a top level Grid variable:
<http://test.opendap.org/opendap/data/nc/coads_climatology.nc.dds?SST>

`Dataset {`
`   Grid {`
`     Array:`
`       Float32 SST[TIME = 12][COADSY = 90][COADSX = 180];`
`     Maps:`
`       Float64 TIME[TIME = 12];`
`       Float64 COADSY[COADSY = 90];`
`       Float64 COADSX[COADSX = 180];`
`   } SST;`
`} coads_climatology.nc;`

Choosing a Map variable:
<http://test.opendap.org/opendap/data/nc/coads_climatology.nc.dds?SST.TIME>

`Dataset {`
`   Structure {`
`       Float64 TIME[TIME = 12];`
`   } SST;`
`} coads_climatology.nc;`

CEs for data
Replace the ‘.dds’ in the previous URLs with .ascii or .dods (use ascii
with a web browser or dods with an application that understands binary
data)

Look at the results

Try various combinations

- *Writing Server Functions: short version*
- *Programming with libdap*
- *A Real Server Function*
- *Write HelloWorld()*
- *Advanced Topics*

### CE Selection Examples

Selections are used to choose values from relational types

Try it:
<http://localhost:8080/opendap/data/ff/avhrr.dat.asc?day>

<http://localhost:8080/opendap/data/ff/avhrr.dat.asc?day&day=19>

<http://localhost:8080/opendap/data/ff/avhrr.dat.asc?&day=19>

The first constraint is a projection that chooses just the variable
‘day’ while the second constraint includes a selection clause which
chooses only those entries where the value of day is 19. The third
constraint returns all the fields but only those rows where day is 19

### CE Functions

- Constrain Expressions can also invoke functions.
- The functions can compute and return values.
- They can also return a Boolean value for use in the selection part of
  the constraint expression.
- Functions are packaged in BES Modules and loaded by the BES at start
  up.
- The BES library (which is used by all of our handlers) includes a base
  set of functions

#### Function Syntax

The <ce> may contain one or more function calls
\- <function call>,<function call>

<!-- -->

Functions can be composed.
\- <function> “(“ <zero or more args> “)”

#### Function Documentation

- Use ?version() to get a listing of installed functions.
- Use ?<function>() to get help information specific to a particular
  function
- Go to docs.opendap.org\*

#### Simple Example: The version() function

Example of a simple function call:


<http://test.opendap.org/opendap/data/nc/fnoc1.nc.ascii?version>()

\- Run this example in a browser or command shell using getdap

\- This is a very simple function and it takes no arguments

\- It’s intent is both as an example and to provide information about
the functions leaded into the server.

getdap:

`jimg : ~/src/hyrax_1.8_release $ getdap "`[`http://test.opendap.org/opendap/data/nc/fnoc1.nc.ascii?version`](http://test.opendap.org/opendap/data/nc/fnoc1.nc.ascii?version)`()"`
`Dataset: function_result_fnoc1.nc`
`version, "`

<?xml version=\"1.0\" encoding=\"UTF-8\"?>

`…`

#### More Useful Functions

grid()
sample a Grid variable based on the values of its map vectors

geogrid()

just like grid(), but assume that the Grid holds geospatial data

linear_scale()
Scale data using y=mx+b

#### geogrid() function example

Use the geogrid() function to select values from the COADS Climatology
data set: ?geogrid(SST,-5,-80,-40,-50)

<http://test.opendap.org/opendap/data/nc/coads_climatology.nc.asc?geogrid(SST,-5,-80,-40,-50>)

Comments
\- Note that the lat/lon values from the call match those of the
response

\- The arguments are a little odd but help is available by calling the
function with no arguments

\- This function’s logic is pretty crude, but the same interface can be
used in front of more sophisticated processing (i.e., GIS)

\- Calling the function with no arguments returns version information
and the URL of the function’s documentation.

\- Note also that this made no choice about the time information in the
SST variable…

##### Selecting Time


?geogrid(SST,40,-80,5,-50,\\2000\<TIME\\)

<!-- -->

Try it - be careful with the quotes:
http://test.opendap.org/opendap/data/nc/coads_climatology.nc.asc?geogrid(SST,-5,-80,-40,-50,"2000\<TIME")

The general rule for geogrid() is that the first five arguments are
required (they specify the Grid variable and the TLBR lat/lon box) and
then subsequent arguments are relational expressions which include
constants and variables which are the names of the Grid’s map vectors.
The result is the intersection of the zero or more relational
expressions