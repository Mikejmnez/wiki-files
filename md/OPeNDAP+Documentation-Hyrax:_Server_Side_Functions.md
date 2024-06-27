## Overview

Before you embark upon writing a server side function for DAP2 you
should read through this [overview of DAP2 constraint expressions and
their use of functions](DAP2:_Constraint_Expressions "wikilink").

<font color="red">Here's another source for information on Server Side
FUnctions and Constraint Expressions:
<http://docs.opendap.org/index.php/UserGuideOPeNDAPMessages#Constraint_Expressions>
You might want to take some of your new text and fold it into this and
then reference the User Guide only - I'm just angling here for any way
to improve the User Guide ;-) jhrg 2/11/13</font>

## Types of Server Side Functions

> Here we document all three types of Server Side Functions that DAP2
> supports. In *practice* only the first kind of function is very widely
> used. These functions take a set of variables from the current dataset
> and return a new value wrapped in a DAP2 variable. The other kinds of
> functions are used to control constraint expression evaluation and to
> add new variables to the dataset's DDS, respectively. We have used
> these to build queryable interfaces for inventories.

BaseType Functions
<font size="2">`void(*btp_func)(int argc, libdap::BaseType *argv[], libdap::DDS &dds, libdap::BaseType **btpp)`</font>

A BaseType function takes four arguments: an integer (argc), a vector of
BaseType \*s (argv), the DDS for the dataset for which these function is
being evaluated (analogous to the ENVP in UNIX) and a pointer for the
function's return value. ARGC is the length of ARGV.

<!-- -->

Boolean Functions
<font size="2">`void(*bool_func)(int argc, libdap::BaseType *argv[], libdap::DDS &dds, bool *result)`</font>

A boolean function takes four arguments, an integer (argc), a vector of
BaseType \*s (argv), the DDS for the dataset for which these function is
being evaluated (analogous to the ENVP in UNIX) and a pointer for the
function's return value. Unlike the previous type function, the result
is a boolean type, and will be used by the constraint expression
evaluator to determine the truth value for the expression. ARGC is the
length of ARGV.

<!-- -->

Projection Functions
<font size="2">`void(*proj_func)(int argc, libdap::BaseType *argv[], libdap::DDS &dds, libdap::ConstraintEvaluator &ce)`</font>

A projection function is a function that appears in the projection part
of the CE and is executed for its side-effect. Usually adds a new
variable to the DDS. These are run *during the parse* so their
side-effects can be used by subsequent parts of the CE.

## Writing Server Side Functions

### Example: HelloWorld

What we'll do

- Write a C++ function which uses one of the three server function type
  signatures, btp_func (BaseTypePointer_Function)
- Write a very simple subclass of the
  <font size="2">`libdap::Function`</font> class and install the your
  function into the class using the
  <font size="2">`libdap::Function.setFunction()`</font> method.
- Write a very simple subclass of the
  <font size="2">`BESAbstractModule`</font> and in the
  <font size="2">`initialize()`</font> method, add an instance of your
  Function class to the
  <font size="2">`libdap::ServerFunctionsList`</font>
- Build and install the module.

Get the example_ssFunction project and follow along:


<font size="2">`svn co `[`https://scm.opendap.org/svn/trunk/example_ssFunction`](https://scm.opendap.org/svn/trunk/example_ssFunction)</font>

#### The `helloWorld()` function

Here is the <font size="2">`example_ssf::helloWorld()`</font> function
from [the example_ssFunction
project.](https://scm.opendap.org/svn/trunk/example_ssFunction/) The
<font size="2">`example_ssf::helloWorld()`</font> function is defined in
the file
[ExampleServerSideFunctions.cc](https://scm.opendap.org/trac/browser/trunk/example_ssFunction/ExampleServerSideFunctions.cc).

<font size="2">

``` cpp
void helloWorld(  int argc,   libdap::BaseType *argv[],  libdap::DDS &dds, libdap::BaseType **btpp)
{
    Str *dapResult = new Str("helloWorldFunction_result");
    dapResult->set_value("Howdy Stranger...");
    *btpp = dapResult;
}
```

</font> We can see from the method's type signature that it is a
BaseType function. The <font size="2">`helloWorld()`</font> function
creates a DAP String object, set it's value and return it via the return
value parameter **btpp**.

#### An instance of libdap::ServerFunction

Now we need a <font size="2">`libdap::ServerFunction`</font> to provide
an API by which the server can learn things about our new function.
Since <font size="2">`libdap::ServerFunction`</font> is not an abstract
we don't need to override any methods to make the child class work, all
we do is give the new instance state that's specific to our new
function.

<font size="2">

``` cpp

libdap:ServerFunction *helloWorld =
    new libdap::ServerFunction(
        "helloWorld", // The name of the function as it will appear in a constraint expression
        "1.0",        // The version of the function
        "Returns a DAP String whose value is 'Hello World'", // A brief description of the function
        "helloWorld()", // A usage/syntax statement
        "http://docs.opendap.org/index.php/Hyrax:_Server_Side_Functions", // A URL that points two a web page describing the function
        "http://docs.opendap.org/index.php/Hyrax:_Server_Side_Functions", // A URI that defines the role of the function
        example_ssf::helloWorld // A pointer to the helloWorld() function
    );
```

</font>

Now we have

- a BaseType function, and
- a libdap::ServerFunction class instance through whose API we can learn
  about the<font size="2">`example_ssf::helloWorld`</font> function.

Now we just need to hook it up to the BES and Hyrax...

#### A child class of BESAbstractModule

In order to get our new function into Hyrax we need to have the BES load
it at startup. This is done by writing a very simple BES module. For
this example our module class is called
<font size="2">`ExampleServerSideFunctions`</font> which is declared in
the header file
[ExampleServerSideFunctions.h](https://scm.opendap.org/trac/browser/trunk/example_ssFunction/ExampleServerSideFunctions.h):

<font size="2">

``` cpp
class ExampleServerSideFunctions: public BESAbstractModule {
public:
    ExampleServerSideFunctions() {}
    virtual ~ExampleServerSideFunctions() {}
    virtual void initialize(const string &modname);
    virtual void terminate(const string &modname);
    virtual void dump(ostream &strm) const;
};
```

</font>

and implemented in
[ExampleServerSideFunctions.cc](https://scm.opendap.org/trac/browser/trunk/example_ssFunction/ExampleServerSideFunctions.cc):

<font size="2">

``` cpp
void ExampleServerSideFunctions::initialize(const string &modname) {
    BESDEBUG( "ExampleServerSideFunctions", "Initializing ExampleServerSideFunctions:" << endl );

    // Here we wrap the helloWorld() function in an instance of the libdap::ServerFunction class
    libdap:ServerFunction *helloWorld =
        new libdap::ServerFunction(
            "helloWorld", // The name of the function as it will appear in a constraint expression
            "1.0",        // The version of the function
            "Returns a DAP String whose value is 'Hello World'", // A brief description of the function
            "helloWorld()", // A usage/syntax statement
            "http://docs.opendap.org/index.php/Hyrax:_Server_Side_Functions", // A URL that points two a web page describing the function
            "http://services.opendap.org/dap4/server-side-function/helloWorld", // A URI that defines the role of the function
            example_ssf::helloWorld // A pointer to the helloWorld() function
        );

    // Here we add our new instance of libdap::ServerFunction to the libdap::ServerFunctionsList.
    libdap::ServerFunctionsList::TheList()->add_function(helloWorld);

    BESDEBUG( "ExampleServerSideFunctions", "Done initializing ExampleServerSideFunctions" << endl );
}

void ExampleServerSideFunctions::terminate(const string &modname) {
    BESDEBUG( "ExampleServerSideFunctions", "Removing ExampleServerSideFunctions module (this does nothing)." << endl );
}

extern "C" {
    BESAbstractModule *maker() {
        return new ExampleServerSideFunctions;
    }
}
```

</font> Note carefully the method
<font size="2">`ExampleServerSideFunctions.initialize()` which contains
the call to the
<font size="2">`libdap:ServerFunctionsList.addFunction()`</font> method.
It is this call that causes a HelloWorld class instance and thus the
helloWorld() function to be registered in BES.

And that's pretty much all there is to the C/C++ programming. What
remains is to build a little autotools project for your function code
and set it up to install into the BES when *make install* is run.