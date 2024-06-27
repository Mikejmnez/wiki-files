## Proposal for DAP4 Server-Side Functions

**Author**: Dennis Heimbigner
**Organization**: Unidata/UCAR
**Initial Draft**: 1/1/16 **Revised**: 1/16/16

**Change List**

<table border=1 width="85%">
<tr>
<td width="25%">

1/1/16:

</td>
<td>

Initial Draft

</td>
</tr>
<tr>
<td width="25%">

1/16/16

</td>
<td>

Converted to totally functional form using an eval() function.

</td>
</tr>
<td>

Added comma as escaped character for function args.

</td>
</tr>
</table>

### Introduction

The server-side function (SSF) problem divides into two parts.

- Type 1: There are limited set of functions that do "simple"
  computations on the server with respect to one or more datasets. These
  computations include (and sometimes extend) the constraint
  computations associated with (e.g. DAP4) queries.

<!-- -->

- Type 2: There are much larger, longer-duration, computations that do
  significant computing over one or more datasets.

This proposal addresses Type 1 functions for use within an extended DAP4
constraint language. I will address my approach to Type 2 in a
subsequent document.

### General Syntax

The key element of this proposal is to use single assignment syntax and
semantics.

- A query consists of a sequence of assignment statements optionally
  separated by blanks.

<!-- -->

- The basic assignment statement is of the form:
      name=f(a1,...,an)

Note that using this form, we can jam statements together without (I
think) any parsing ambiguity. I considered using a semicolon separator,
but we already overload that character. Note also that first level
parsing becomes trivial; if it were not for the nested parentheses issue
(see below) it would (I think) be regular.

<font color="red">Interesting. This idea is much more like a series of
statements than the DAP2 (original) notion of a single function per
query/access. This is effectively what we've started doing - allowing
for a sequence of 'statements.' This idea takes that notion farther by
binding names to the results, which is interesting but also means that
the lookup table (environment) must be read/write. Not a huge deal, but
something to consider.</font>

<font color="blue">Not sure what you mean by "...lookup table
(environment) must be read/write..." For a given query, the number of
names on the left hand side is fixed for the query. Since this is single
assignent, then once assigned, the value associated with a name is
fixed. dmh 1/15/16</font>

<font color="red">I'm talking in terms of building an interpreter - the
new symbol has to be added to some sort of a lookup table because (I'm
assuming here) that we want to be able to use them in subsequent calls
in the same line. Like this: name1=function1(var1, var2);
name2=function2(var1, var2, name1). jhrg 1/19/16</font>

I considered the possibility of allowing a function to return multiple
values, like this

    name,...name=f(...)

but I think that this makes the construction of meta-data (see below)
harder. The price one pays is that one might have to convert the above
to something like this.

    name1=f(1,...)
    ...
    namen=f(n,...)

This implies that *f()* must be called repeatedly. This may be mitigated
by having f() cache its results or by having *f()* return a structure.

\[N.B. In light of jhg comment, using a return structure seems to be the
way to go.\]

<font color="red">We are using the 'structure return' approach now and
it works fairly well. There are, of course, some wrinkles, but on the
whole it's a simple solution that works well with the existing software.
jhrg 1/5/16</font>

<font color="blue">Good to know. I will adopt. dmh 1/15/16</font>

### Single Assignment Rule

Any "assignment" is unique in that no other statement may assign to this
same variable; this is why it is called "single assignment".

It is convenient for a number of reasons:

1.  It is (IMO) easier to read and write
2.  It unwinds nested expressions, hence simplifying the syntax.

### Function Syntax

A function call is of the usual form

    f(a1,a2,...,an)

It is intended that function namespaces be supported, so the function
name, f, may actually be of the form *x.y.z.f*. The assumption is that
the server maintains a namespace tree with functions as leaves. Certain
function names such as *eval* and *return* are reserved words. Note that
this not much of an issue since any one introducing their own *eva*',
for example, would presumably put it into some defined namespace (e.g.
*ns.eval*).

The arguments (a1,...,an) present a problem. It is unlikely that we can
syntactically prescribe the form of arguments because they are specific
to the function. For those familiar with lisp, they need to act like
FEXPRs.

Because of the need to construct meta-data (see below), it must be
possible to detect which arguments refer to previously defined
variables. The approach taken here is to assume that the arguments to an
expression are separated by commas. Thus, the top-level query processor
can parse the arguments to a function and detect which are the names of
previously assigned variables.

In addition, there must be some way to pass other kinds of arbitrary
arguments as unevaluated strings to the function. For other arguments,
including simple names that might be mistaken for variables, we need
some kind of escape process. I propose that we use parentheses plus,
where necessary, the backslash ('\\) as the escaping mechanism. This
means that any argument that is surrounded by parentheses is passed
unchanged as a string argument to the function. Note that if the
argument itself contains balanced parentheses, these will be parsed as
matching pairs and passed along. The gotcha is that an unbalanced
parenthesis must be escaped with a backslash.

Similarly, commas and backslashes will need to be escaped so that the
list of function arguments can be properly parsed.
<font color="blue">\[New\]</font>

<font color="red">We're doing this. Again, it's not so bad. (sorry about
the red, but it makes these comments easier to see)</font>
<font color="blue">Are you using backslash escapes? dmh 1/15/16</font>
<font color="red">We use double quotes jhrg 1/19/16</font>

### Evaluating DAP4 Basic Constraints

<font color="blue">\[New section\]</font>
It is convenient to allow for the use of DAP4 basic constraints. To this
end, we support an *eval* function that takes a single DAP4 basic
constraint, evaluates it, and returns it's value as the value of the
*eval* function.

### Returning Variables

At the end of our query, it will be necessary to specify the variables
that will be returned to the client that made they original query. The
simplest approach is to have a "return" statement of this form.

    return(d1,d2,...dn)

where the di are the names to which expressions were previously
assigned. One potential complication is with respect to DAP4 groups. It
may be desirable to insert any of the resulting variables in a specific
DAP4 group. To this end, I propose to all the di to actually take this
form: *g1.g2...gn.di* to indicate the group position of each variable.

### Meta-data for Queries

For DAP4, every query is associated with a meta-data description that
describes the structure of the returned data.

This is the really hard part of this proposal, and everything else must
be designed to support meta-data construction.

Note that meta-data construction will be needed even if the expression
is not evaluated. This is, of course, because the client can ask for the
*.dmr* and that will return only the meta-data.

<font color="red">We've tried this and it's OK when it works, but some
functions' results are needed to determine the metadata. We've adopted
the rule that it's easier to just have functions do their thing and
cache the results - return only the DMR when that's the request but
cache the full data response.</font> <font color="blue">The costs are of
course that the full computation may be enormous. dmh 1/15/16</font>

As a minor point, note that the result(s) of a query are defined by the
return statement. This means that we only need to obtain the meta-data
for the expressions that defined the variables in the return statement.

For DAP4 basic constraints, we already know how to construct the
meta-data. For a function evaluation, we have no idea because it could
be largely arbitrary.

The approach taken here is to require every function to be able to
describe the meta-data that will result from its execution with specific
arguments. This means that when the client requests the *dmr*, the query
must be evaluated as the meta-data level to obtain the final *dmr*. If,
later, the client asks for the *.dap* (the data), then the evaluation
must produce data that conforms to the *dmr*.

The *dmr* construction process is likely to look like this.

1.  Meta-evaluate each statement in the query from left to right.
2.  If the statement is "name=(<DAP4 expression>)" then use the existing
    rules to meta-assign the DAP4 expression meta-data to the left side
    variable.
3.  Consider a function "f(a1,...,an)", where some of the ai are
    references to previously defined variables. In this case, we invoke
    the functions's meta-evaluator with its variable references replaced
    with the corresponding meta-data for that variable.
4.  The final *return* statement returns the concatenation of the
    meta-data of the specified variables.

There are some issues that need addressing.

1.  **Structures**: A function might return a structure, which is ok,
    but it might return a structure with different numbers of fields
    depending on its actual inputs. This is not ok and I propose to
    prohibit it.
2.  **Selections over dimensioned arrays**: We discussed this a long
    time ago and decided to leave it out of the basic DAP4 constraint
    language. However here it is possible for a function to, for
    example, take an array as input and return another array as output,
    where the output is defined by the values of the input array.



The problem is that the actual dimensions might differ depending on the
content of the input array. So, if the client asks for the *dmr* only,
what dimension is returned? I am considering the following alternatives:

1.  Encapsulate the output array as a sequence or (sometimes) a set of
    nested sequences \[N.B. James, this is one reason I am considering
    the VLEN concept to replace sequences\].
2.  Introduce the netCDF concept of UNLIMITED dimensions to indicate
    that the actual size is unknown, but it is some fixed value.

I have not decided which way to go on this. I lean towards the Sequence
solution.

<font color="red">We've written a function that builds a table from a
set of arrays (that meet other constraints) and use Sequence as the
return type internally, transforming the result to whatever elsewhere as
needed. </font> <font color="blue">Can you elaborate on this? dmh
1/15/16</font>

#### Attributes

I propose to allow functions to annotate their meta-data with whatever
attributes they desire.

### GrADS Examples

Mostly out of curiosity, I attempted to convert some [GrADS '_expr_'
examples](http://www.iges.org/grads/gds/doc/user.html) to the form
proposed here. I failed because GrADS uses a full-blown scripting
language, so it was not possible to emulate the given examples.

### Additional Open Issues

1.  Should this proposal be folded into dap.ce (i.e. the existing basic
    constraint language) or as a separate constraint system dap.fcn,
    say.

<font color="red">We've done the latter</font>