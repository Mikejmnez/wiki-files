# Data Analysis with OPeNDAP

The OPeNDAP software is not only a data transport mechanism. Using
OPeNDAP, you can subsample the data you are looking at. That is, you can
request an entire data file, or just a small piece of it.

## Selecting Data: Using Constraint Expressions

The URL such as this one:

    http://dods.gso.uri.edu/cgi-bin/nph-nc/data/buoys.nc

refers to the entire dataset contained in the buoys.nc file. A user may,
however, choose to sample the dataset simply by modifying the submitted
URL. The constraint expression attached to the URL directs that the data
set specified by the first part of the URL be sampled to select only the
data of interest from a dataset even for programs that do not have a
built-in way to accomplish such selections. This can vastly reduce the
amount of data a program needs to process, and reduce the network load
of transmitting that data to the client.

### Constraint Expression Syntax

A constraint expression is appended to the target URL following a
question mark, as in the following examples:

     http://oceans.univ.edu/cgi-bin/nc/expl/buoys.nc?temp

     http://oceans.univ.edu/cgi-bin/nc/expl/buoys.nc?temp[1,100,5]

     http://oceans.univ.edu/cgi-bin/nc/expl/buoys.nc?u&lat>15.0

     http://oceans.univ.edu/cgi-bin/nc/expl/buoys.nc?cast.02<15.0

     http://oceans.univ.edu/cgi-bin/nc/expl/buoys.nc
                                       ?station&station.temp<15.0

A constraint expression consists of two parts: a projection and a
selection, separated by an ampersand (&). Either part may contain
several sub-expressions. Either part may be present, or both.

                   proj_1,proj_2,...,proj_n&sel_1&sel_2&...&sel_m

A projection is simply a comma-separated list of the variables that are
to be returned to the client. If an array is to be subsampled, the
projection specifies the manner in which the sampling is to be done. If
the selection is omitted, all the variables in the projection list are
returned. If the projection is omitted, the entire dataset is returned,
subject to the evaluation of the selection expression. The projection
can also include functional expressions of the form:

             function(arg_1,arg_2,...,arg_n)

where the arguments are variables from the dataset, scalar values, or
other functions.

    A simple selection expression is a boolean expression of the form
    variable operator variable
    or
    variable operator value
    or
    function(arg_1,arg_2,...,arg_n)

Where

> operator
>
> `   can be one of the relational operators listed in table 4.1.2 on here; `
>
> variable
>
> `   can be any variable recorded in the dataset; `
>
> value
>
> `   can be any scalar, string, function, or list of numbers (Lists are denoted by comma-separated items enclosed in curly braces ,for example, {3,11,4.5}.); and `
>
> function

`   is a function defined by the server to operate on variables or values, and to return a boolean value (See `[`Section 4.1.3`](Wiki_Testing/OPeNDAPUserGuide4 "wikilink")`). `

Each selection clause begins with an ampersand (&) representing the
"AND" boolean operation10.

> NOTE: The & is actually a prefix operator, not an infix operator. That
> is, it must appear at the beginning of each selection clause, no
> matter what. This means that a constraint expression that contains no
> projection clause must still have an & in front of the first selection
> clause.

There is no limit on the number of selection clauses that can be
combined to create a compound constraint expression. Data that produces
a true (non-zero) value for the entire selection expression will be
included in the data returned to the client by the server. If only a
part of some data structure, such as a Sequence, satisfies the selection
criteria, then only that part will be returned.

> NOTE: Due to the differences in data model paradigms, selection is not
> implemented for the OPeNDAP array data types, such as Grid or Array.
> However, many OPeNDAP servers implement selection functions you can
> use for the same effect. You can query the server for the functions it
> implements with the usage service outlined in
> \[Wiki_Testing/OPeNDAPUserGuide4\|Section 4.1.3\]\].

Simple Constraint Expression Examples

Consider the data descriptor in figure 4.1.1. The figure is an example
of the Data Descriptor Structure , one of the messages returned by an
OPeNDAP server in response to a query about some dataset. The full
syntax description for this structure is given in Section 6.4. For the
moment, it is only important that it is the description of a dataset
containing station data including temperature, oxygen, and salinity.
Each station also contains 20 oxygen data points, taken at 20 fixed
depths, used for calibration of the data.

The following URL will return only the pressure and temperature pairs of
this dataset. (Note that the constraint expression parser removes all
spaces, tabs, and newline characters before the expression is parsed.)
There is only a projection clause, without a selection, in this
constraint expression11.

    Dataset {
       Sequence{
          Int32 day;
          Int32 month;
          Int32 year;
          Float64 lat;
          Float64 lon;
          Float64 O2cal[20];
          Sequence{
             Float64 press;
             Float64 temp;
             Float64 O2;
             Float64 salt;
          } cast;
          String comments;
       } station;
    } arabian-sea;

Sample Data Descriptor

    http://oceans.edu/cgi/nph-jg/exp1O2/cruise?station.cast.press,
                                               station.cast.temp

Incidentally, we have assumed that the dataset was stored in the JGOFS
format12 on the remote host oceans.edu, in a file called explO2/cruise.
For the sake of brevity, from here on we will omit the first part of the
URL, to concentrate on the constraint expression alone.

If we only want to see pressure and temperature pairs below 500 meters
deep, we can modify the constraint expression by adding a selection
clause.

    ?station.cast.press,station.cast.temp&station.cast.press>500.0

In order to retrieve all of each cast that has any temperature reading
greater than 22 degrees, use the following:

    ?station.cast&station.cast.temp>22.0

Simple constraint expressions may be combined into compound expressions
with logical AND (& ). To retrieve all stations west of 60 degrees West
and north of the equator:

    ?station&station.lat>0.0&station.lon<-60.0

As was mentioned, the logical OR can be implemented using a list of
scalars. The following expression will select only stations taken north
of the equator in April, May, June, or July.

    ?station&station.lat>0.0&station.month={4,5,6,7}

If our dataset contained a field called monsoon-month, indicating the
month in which monsoons happened that year, we could modify the last
example search to include those months as follows:

    ?station&station.lat>O.O
            &station.month={4,5,6,7,station.monsoon-month}

In other words, a list can contain both values and other variables. If
monsoon-month was itself a list of months, a search could be written as:

    ?station&station.lat>0.0&station.month=station.monsoon-month

For arrays and grids, there is a special way to select data within the
projection clause. Suppose we want to see only the first five oxygen
calibration points for each station. The constraint expression for this
would be:

    ?station.02cal[0:4]

By specifying a stride value, we can also select a hyperslab of the
oxygen calibration array:

    ?station.02cal[0:5:19]

This expression will return every fifth member of the 02cal array. In
other words, the result will be a four-element array containing only the
first, sixth, eleventh, and sixteenth members of the 02cal array. Each
dimension of a multi-dimensional arrays may be subsampled in an
analogous way. The return value is an array of the same number of
dimensions as the sampled array, with each dimension size equal to the
number of elements selected from it.

### Operators, Special Functions, and Data Types

The data types accessible through the OPeNDAP software are listed and
described in Section 6.3. It is advisable to be familiar with these
types before trying to construct complex constraint expressions.

The constraint expression syntax defines a number of operators for each
data type. These operators are listed in table 4.1.2

Except for the \* operation defined on the URL data type, all the
operators defined for the scalar base types are boolean operators whose
result depends on the specified comparison between its arguments. Refer
to Section 4.1.4 for a description of the URL data type and its
operator.

The ~= operator returns true when the character string on the left of
the operator matches the regular expression on the right. See Section
4.1.5 for a discussion of regular expressions.

The Structure, Sequence, and Grid data types are each composed of a
collection of simpler data types. The . and operators allow a user to
refer to the subsidiary variables within these compound types. For
example, station.year indicates the value of the year member of the
station sequence.

The array operator \[\] is used to subsample the given array. See here
for an explanation and example of its use.

<center>

Constraint Expression Operators.

| Class                        | Operators                                    |
|------------------------------|----------------------------------------------|
| Simple Types                 |                                              |
| Byte, Int32, UInt32, Float64 | \< \> = != \<= \>=                           |
| String                       | = != ~=                                      |
| URL                          | \*                                           |
| Compound Types               |                                              |
| Array                        | \[start:stop\] \[start:stride:stop\]         |
| List                         | length(list), nth(list,n), member(list,elem) |
| Structure                    | \*                                           |
| Sequence                     | \*                                           |
| Grid                         | \[start:stop\] \[start:stride:stop\] .       |

</center>

There are three special functions defined to operate on the List data
type. The length() function returns the number of elements in the given
list, the nth() function returns the list element indicated by the input
index, and the member() function, which returns true if the given value
equals any member of the list. Note that the behavior of the nth()
function is undefined for indices beyond the range of the list.

### Using Functions in a Constraint Expression

An OPeNDAP data server may define its own set of functions that may be
used in a constraint expression. For example, the data server containing
the example data from figure 4.1.1 might define a sigma1() function to
return the density of the water at the given temperature, salinity and
pressure. A query like the following would return all the stations
containing water samples whose density exceeded 1.0275g/cm3.

    ?station.cast&sigma1(station.cast.temp,
                         station.cast.salt,
                         station.cast.press)>27.5

Functions like this one are not a standard part of the OPeNDAP
architecture, and may vary from one server to another. A user may query
a server for a list of such functions by sending a URL ending with
".info". For example, you can query the data server installed on the
OPeNDAP home site with the following URL:

      http://dods.gso.uri.edu/cgi-bin/nph-nc/fnoc1.nc.info

The data returned will be an HTML message, readable with a standard web
browser, containing documentation of the server running on the given
site, and the data named in the URL. In this case, you will learn that
the specified server defines two functions that can be used in a
constraint expression:

geolocate(variable, lat1, lat2, lon1, lon2)

Returns the elements of variable that fall within the box created by
(lat1,lon1) and (lat2,lon2).

time(variable, start_time, stop_time)

Returns the elements of variable that fall within the time interval
start_time and stop_time.

### Using URLs in a Constraint Expression

The OPeNDAP data access protocol defines a special data type to handle
distributed data: URL. This is a scalar data type, much like the String
type, intended to hold one OPeNDAP URL. It generally points at some
remote dataset or data value. Using this data type, a constraint
expression may make the data returned from one OPeNDAP data server
dependent on data held at an entirely different site.

In order to accommodate this data type, OPeNDAP defines a special
"dereference" operator \*. Similar to its function with pointers in C,
applying this operator to a URL returns the data specified by that URL.
The URL data type itself contains only a character string. It must be
dereferenced to produce a reference to the data named by the URL.

Examples

The following example will return all the stations containing oxygen
values greater than fifteen:

    ?station&station.cast.O2>15.0

Similarly, the following constraint expression will yield all the
stations in the dataset whose value is greater than that of the oxygen
value indicated by the URL:

    ?station&station.cast.O2>*''http://ocean.edu/etc/nc/data?O2MAX''

Finally, suppose that the dataset itself contained a variable of type
URL, and that this URL contained the address of oxygen data stored at
some other site. The data descriptor for the dataset might look like the
following:

    Dataset {
       Sequence{
           .
           .
           .
          URL O2cal;
           .
           .
           .
       } station;
    } arabian-sea;

We can now write the previous constraint as:

    ?station&station.cast.O2>*O2cal

URLs stored in remote datasets may also be used in the projection clause
of the constraint expression. Imagine a dataset that consists only of a
list of URLs for each square degree of latitude and longitude. A user
could query this dataset for the actual list of URLs, or, by using the
\* operator, could construct a constraint expression that would return
the actual data indicated by the URLs in the target dataset.

### Pattern Matching with Constraint Expressions

There are three operators defined to compare one String data type to
another. The = operator returns TRUE if its two input character strings
are identical, and the != operator returns TRUE if the Strings do not
match. A third operator, ~= is provided that returns TRUE if the String
to the left of the operator matches the regular expression in the String
on the right.

A regular expression is simply a character string containing wildcard
characters that allow it to match patterns within a longer string. For
example, the following constraint expression might return all the
stations on the sample cruise at which a shark was sighted:

    ?station&station.comment~=``.*shark.*''

Most characters in a regular expression match themselves. That is, an
"f" in a regular expression matches an "f" in the target string. There
are several special characters, however, that provide more sophisticated
pattern-matching capabilities.

> .
>
> The period matches any single character except a newline.
>
> \* + ?
>
> These are postfix operators, which indicate to try to match the
> preceding regular expression repetitively (as many times as possible).
> Thus, o\* matches any number of o's. The operators differ in that o\*
> also matches zero o's, o+ matches only a series of one or more o's,
> and o? matches only zero or one o.
>
> \`\[ ... \]'
>
> Define a "character set," which begins with \[ and is terminated by
> \]. In the simplest case, the characters between the two brackets are
> what this set can match. The expression \[Ss\] matches either an upper
> or lower case s. Brackets can also contain character ranges, so
> \[0-9\] matches all the numerals. If the first character within the
> brackets is a caret ( ), the expression will only match characters
> that do not appear in the brackets. For example, \[ 0-9\]\* only
> matches character strings that contain no numerals.
>
> \$
>
> These are special characters that match the empty string at the
> beginning or end of a line.
>
> \\
>
> These two characters define a logical OR between the largest possible
> expression on either side of the operator. So, for example, the string
> Endeavor\\Oceanus matches either Endeavor or Oceanus. The scope of the
> OR can be contained with the grouping operators, $and$.
>
> $$
>
> These are used to group a series of characters into an expression, or
> for the OR function. So, for example, $abc$\* matches zero or more
> repetitions of the string abc2.

There are several more special characters and several other features of
the characters described here, but they are beyond the scope of this
guide. The OPeNDAP regular expression syntax is the same as that used in
the Emacs editor. See the documentation for Emacs \[1\] for a complete
description of all the pattern- matching capabilities of regular
expressions.

Examples

In the above example, a user might wonder whether the shark comments had
been spelled with upper or lower case letters. The following constraint
expression will return any station that mentions a shark in upper or
lower case.

    ?station&station.comment~=``.*\(SHARK\|shark\).*''

Of course, this would miss Shark and sHark and so on. The constraint
could be written this way to catch all odd permutations of upper and
lower case:

    ?station&station.comment~=``.*[Ss][Hh][Aa][Rr][Kk].*''

### Optimizing the Query

Using the tools provided by OPeNDAP, a user can build quite elaborate
and sophisticated constraint expressions that will return precisely the
data he or she wishes to examine. However, as the complexity of the
constraint expression increases, so does the time necessary to process
that expression. There are some techniques a user may user to optimize
the evaluation of a constraint that will ease the load on the server,
and provide faster replies to OPeNDAP dataset queries.

The OPeNDAP constraint expression evaluator uses a "lazy evaluation"
algorithm. This means that the sub-clauses of the selection clause are
evaluated in order, and parsing halts when any sub-clause returns FALSE.
Consider a constraint expression that looks like this:

    ?station&station.cast.O2>15.0&station.cast.temp>22.0

If the server encounters a station with no oxygen values over 15.0, it
does not bother to look at the temperature records at all. The first
sub- clause evaluates FALSE, so the second clause is never even parsed.

A careful user may use this feature to his or her advantage. In the
above example, the order of the clauses does not really matter; there
are the same number of temperature and oxygen measurements at each
station. However, consider the following expression:

    ?station&station.cast.O2>15.0&station.month={3,4,5}

For each station there is only one month value, while there are many
oxygen values. Passing a constraint expression like this one will force
the server to sort through all the oxygen data for each station (which
could be in the thousands of points), only to throw the data away when
it finds that the month requested does not match the month value stored
in the station data. This would be far better done with the clauses
reversed:

    ?station&station.month={3,4,5}&station.cast.O2>15.0

This expression will evaluate much more quickly because unwanted
stations may be quickly discarded by the first sub-clause of the
selection. The server will only examine each oxygen value in the station
if it already knows that the station might be worth keeping.

This sort of optimization becomes even more important when one of the
clauses contains a URL. In general, any selection sub-clause containing
a URL should be left to the end of the selection. This way, the OPeNDAP
server will only be forced to go to the network for data if absolutely
necessary to evaluate the constraint expression.

## A Word About Data Translation

Once a researcher is freed from the confines of using only local data,
he or she will soon discover that there is a wealth of data available on
the Internet, and nearly all of it is stored in formats incompatible
with her own. Worse, the data formats are often mutually incompatible,
rendering the confusion complete. OPeNDAP provides a solution applicable
to a great many such problems.

When an OPeNDAP server retrieves data from some distant machine, that
data may be in any of several file formats supported by OPeNDAP. The
server translates the data, however, into an intermediate format for
transmission. Upon receipt of the messages containing data, the OPeNDAP
client software unpacks the data into the form expected by the calling
client program and returns it to that program. Because all data must be
translated into the same intermediate format, OPeNDAP becomes a powerful
format translator for datasets. In effect, this means that a program
designed to read and display JGOFS data can look at the OPeNDAP data
catalog and see everything as JGOFS datasets. A netCDF program can look
at those same datasets, from that same catalog, and think they are all
in netCDF format. This system of translation allows a researcher to
ignore the question of formats and concentrate on the data alone.

Of course, there are some translations that cannot be done
transparently, if they can be done at all. Consider a two-dimensional
array of satellite sea-surface temperature measurements. Assume the data
is stored in netCDF format on some machine called satt.uri.edu. The data
might be uniquely specified by some URL, say
<http://satt.uri.edu/sst/010694.nc>. However, were a user to feed that
URL to a JGOFS-originated OPeNDAP client designed to draw property vs.
depth graphs of station data, no translation facility would be able to
map the original data into a form accommodated by the client program.

The issues of data models and data translation are important ones to the
data provider. These issues are discussed in detail in [Section
6.1.2](Wiki_Testing/OPeNDAPUserGuide6 "wikilink").