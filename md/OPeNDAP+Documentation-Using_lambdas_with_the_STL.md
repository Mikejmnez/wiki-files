# Using lambdas with the STL

Let's say you want to find the first instance of the variable named
'name' in one of our containers. The thing in the container is not a
string, but a complex object with lots of fields, one of which happens
to be a string that holds the name of the object. Here's the old,
pre-c++-11 way using a function that returns a boolean and some adapters
like bind2nd() and ptr_fun().

Change code like:

     // Note that in order for this to work the second argument must not be a reference.
     // jhrg 8/20/13
     static bool
     name_eq(D4Group *g, const string name)
     {
        return g->name() == name;
     }

     groupsIter g = find_if(grp_begin(), grp_end(), bind2nd(ptr_fun(name_eq), grp_name));

to:

     auto g = find_if(grp_begin(), grp_end(), [name](const D4Group *g) { return g->name() == name; });
                                               ^     ^                                       ^
                                               1     2                                       3

Where *\[name\](const D4Group \*g) { return g-\>name() == name; }* is a
C++ Lambda function (an anonymous function). This lambda function use
uses a 'capture.' The square braces at \#1 name the captured variable.
Its value is taken from the current environment when the lambda is
instantiated at runtime and used at \#3 in the function. At \#2 the
argument to the lambda function is declared as a *const pointer* so the
compiler knows the function won't be modifying the object. C++ STL
functions like *find_if()* take predicates (which this lambda function
is) and that can streamline code quite a bit.

The return value from find_if(...) is the iterator that references the
first instance in the D4Group with a name that matches *name*.

Note that there a many algorithms in the STL that can perform operations
like searching on all the elements of a container given the beginning
and ending iterators.