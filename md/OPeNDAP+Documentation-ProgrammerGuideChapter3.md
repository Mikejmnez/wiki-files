# Using the OPeNDAP Library Classes

In order to be used by real client-libraries and data servers, many of
the classes in the OPeNDAP toolkit must be subclassed. For example, the
Type Hierarchy classes, which represent the data types in the OPeNDAP
data model, are all abstract classes. In order to use them in a program
they must be subclassed. The Connect class also must be subclassed if it
to hold additional information about the connection.

## Sub-classing the Type Hierarchy

In order to link a program with the OPeNDAP Toolkit library, the DAP's
abstract classes must be subclassed and those subclasses must ensure
that all of the member functions of those classes have valid
definitions. This is necessary because of C++'s rules governing abstract
classes. The next three sections cover sub-classing the simple, vector
and compound classes, respectively. In addition, a sample set of classes
(called the <font color='green'>Test</font> classes because they are
used by the DAP tests) is included with the DAP distribution. You can
read the source code for those classes to find out how they were
created.

Each of the sub-classed types must supply:

- A constructor that takes a String argument and returns an object with
  that name.
- A virtual destructor.
- A copy function called <font color='green'>ptr_duplicate</font>.
- The <font color='green'>read</font> function, to read data from a disk
  and load it into the type class object.

The <font color='green'>ptr_duplicate</font> member function returns a
pointer to a new instance of the type of object from which it was
invoked. This member function exists so that objects that are referenced
through pointers to BaseType can correctly copy themselves. (If you were
to use the operator <font color='green'>new</font> to copy an object
referenced through BaseType , you would get a BaseType, not a new
instance of the type of the referenced object.) Note that
<font color='green'>ptr_duplicate</font> is a virtual function so an
object which is a descendent of BaseType will get the most specific
definition of that function.

The <font color='green'>read</font> function is much more complicated to
write than <font color='green'>ptr_duplicate</font>, and the difficulty
varies depending on the data type to be read. However, this function is
only used on the server side of the system and not by the client. That
is, it can be implemented with a null function body if all you are
building is a client library. The <font color='green'>read</font>
function takes two arguments, the dataset name, and an error flag. It
must read from that dataset the values specified by the current
constraint expression. The error flag is passed by reference so that
read can set its value and callers can test it. The class BaseType
contains a number of member functions to facilitate writing a read
function. The return value of <font color='green'>read</font> is TRUE is
there is more data to be read (by additional calls to
<font color='green'>read</font>) or FALSE otherwise.

### Sub-classing the Simple Types

Creating a <font color='green'>read</font> function for the simple type
classes is a fairly straightforward operation. Simply read the function
using the data access API's standard protocol, and use the type class's
<font color='green'>val2buf</font> function to load the data into the
type object.

The following function is taken from the OPeNDAP implementation of the
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html)
data access library. (See the file
<font color='green'>src/nc-dods/NCByte.cc</font>.)


    NCByte::read(const String &dataset, int &error)
    {
       int varid;                  /* variable Id */
       nc_type datatype;           /* variable data type */
       long cor[MAX_NC_DIMS];      /* corner coordinates */
       int num_dim;                /* number of dim. in variable */
       long nels = -1;             /* number of elements in buffer */
       int id;

       if (read_p()) // already done
         return true;

       int ncid = lncopen(dataset, NC_NOWRITE); /* netCDF id */

       if (ncid == -1) {
         cerr << "ncopen failed on " << dataset<< endl;
         return false;
       }

       varid = lncvarid( ncid, name());
       (void)lncvarinq( ncid, varid, (char *)0, &datatype,
                        &num_dim, (int *)0, (int *)0);

       if(nels == -1){
         for (id = 0; id < num_dim; id++)
           cor[id] = 0;
       }

       if (datatype == NC_BYTE){
         dods_byte Dbyte;

         (void) lncvarget1 (ncid, varid, cor, &Dbyte);
         set_read_p(true);

         val2buf( &Dbyte );

         (void) lncclose(ncid);
         return true;
       }
       return false;
    }


The following points are worth consideration about the above example.

line 10 : Check to see if this variable has already been read.

<!-- -->

line 13 : The <font color='green'>lncopen()</font> function call is simply the <font color='green'>ncopen()</font> function from the [<cite>NetCDF</cite>](http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html) library. Also, the <font color='green'>lncvarid</font> function is renamed <font color='green'>ncvarid</font> and so on. The names have been changed to avoid link-time problems. (Remember that the whole point of this exercise is to create a new <font color='green'>ncopen()</font> function and its friends.

<!-- -->

line 33 : This sets the flag tested with the <font color='green'>read_p</font> function.

<!-- -->

line 35 : This command transfers values from the dataset's variable to the OPeNDAP instance. This is the point where the read actually happens.

Note the use of <font color='green'>dods_byte</font> in the code
example. The OPeNDAP configuration process creates definitions for the
simple data types like this one. They are stored in
<font color='green'>config_dap.h</font>.

The <font color='green'>read</font> member function is used by the
constraint expression evaluator to extract data from a dataset during
evaluation of the constraint expression. This is particularly important
to remember because the <font color='green'>read</font> member function
for a simple data type will be called when reading an aggregate type
such as Structure.

### Sub-classing the Vector Types

The vector data types require the same abstract member functions be
defined as the simple types. The definition for
<font color='green'>ptr_duplicate</font> is also the same for vector as
for simple types. However, the <font color='green'>read</font> member
function for the vector types (classes Array and List) is more
complicated than for the simple types because vectors of values are
represented in two ways in OPeNDAP, depending on the type of variable in
the vector. Arrays are stored as C would store them for the simple types
such as Byte, Int32 and Float64. However, compound types are stored as
arrays of the OPeNDAP \Cpp objects (with the exception of arrays
themselves, but more on that later).

When reading an array of Byte values, the
<font color='green'>val2buf</font> member function should be passed a
pointer to values stored in a contiguous piece of memory. For example,
when <font color='green'>read</font> is called to read a variable
<font color='green'>byte-array</font>, it must determine how much memory
to allocate to hold that much information, use
<font color='green'>new</font> to allocate an adequate amount of memory,
use the dataset's API calls to read
<font color='green'>byte-array</font> into the newly allocated memory
and then pass that memory to <font color='green'>val2buf</font>. This
same procedure can be followed for all the simple types.

However, when reading an array of Structure, for example, the values
must be stored in the OPeNDAP Array object one at a time using the Array
member function <font color='green'>set_vec</font>. An Array object
containing a 3x4 array of Structure objects will actually point to 12
different instances of that class. An Array object containing Byte
objects contains only one instance of the Byte class, as a template for
the array elements.

Arrays in OPeNDAP are unlike arrays in C in that an array object may
have more than one dimension. In terms of the way a value is stored,
however, an Array is a single dimensional object. When an Array is
declared as having two or more dimensions, those are mapped onto a
single vector. To access the element A_ij of array *A* , you must know
the size of the first dimension, *I* , and use the expression i \* I + j
to compute the offset into the vector.

Since the class List is a single dimension array without *declared* size
(the size of each value of the List object is stored in the object) the
rules for Array's read member function apply.

### Sub-classing the Compound Types

The <font color='green'>read</font> member function of the compound data
types simply iterates over the contained variables calling their
<font color='green'>read</font> member functions. In the future, this
definition will move into the supplied classes (That is,
<font color='green'>read</font> will no longer be a abstract member
function for the compound types.).

The two constructor types Sequence and Function are different from all
the other types in the DAP in that they have *state* . That is, the
value of a sequence depends on how many values have been read
previously. This is very different from an array where the i^{th}
element has the same value regardless of what has happened before. When
you write implementations for <font color='green'>read</font> in the
Sequence and Function classes, you must be sure to write those member
functions so that they can be called repeatedly and that each call to
read returns the next value of the corresponding data Sequence or
Function.

This is true because the constraint expression evaluator must be able to
apply certain constraints to values of individual sequence elements and
is actually implemented in the DDS class by first calling the read
member function, evaluating the constraint expression based on the
values and, if they constraint expression is satisfied, calling the
serialize member function. See the member function
<font color='green'>DDS::send</font> (See [Section
4.3.1](ProgrammerGuideChapter4 "wikilink") for more information on
evaluation of constraint expressions).

If, for some reason, it is not possible to write
<font color='green'>read</font> so that it gets called once for each
sequence value [(6)](ProgrammerGuideFootNotes "wikilink"), then you must
re-implement <font color='green'>DDS::send</font> so that its functions
are performed. For example, you could implement
<font color='green'>Sequence::read</font> so that the entire sequence is
read in and overload <font color='green'>Sequence::serialize</font> and
<font color='green'>Sequence::deserialize</font> so that the next set of
values are sent/received. You would then build a send that called the
<font color='green'>Sequence::read</font> member function once and
extracted each successive value, evaluated the constraint expression
using on that value and used the result of that evaluation to determine
whether to send the value or not.

## Sub-classing the Connect Class

The OPeNDAP API defines a connection-less protocol in which a server
keeps no information for a client in between data requests. This is in
contrast to most data access APIs, which maintain state information
about the files or datasets that a user program currently has open. To
simulate an API's connection, the client library for that API must
create a *virtual connection* using information about the data set it
has read from the server. That is, the client library must maintain the
*illusion* of a connection (state) for each open data object (typically
a file) even though no such connection actually exists.

OPeNDAP provides the Connect class to make this a simple process. This
class contains a variety of information about a dataset and its location
on the internet, including the dataset's URL. Most of the information
necessary to fake a connection is contained here.

Data access APIs differ widely however, and it is usually necessary to
add some information to the Connect class to make a workable virtual
connection. This information can be stored in the subclassed Connect.
The API's "open" call must be completely recoded so that the DAS and DDS
objects, as well as any other information, are requested from the
dataset server and stored locally in whatever form is most convenient.

Figure NCConnect shows the sub-class of Connect used to store
information extracted from the DAS and DDS objects (for the
\[<http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html><cite>NetCDF</cite>\]
library), and used to simulate the storage of information in the
original API.[(7)](ProgrammerGuideFootNotes "wikilink")

    class NCConnect: public Connect {
    private:
        int _ncid;
        int _nvars;
        int _ndims;
        String _dim_name[MAX_NC_DIMS];
        int _dim_size[MAX_NC_DIMS];
        int _das_loc[MAX_NC_VARS];
        void init_list(int i);
        void parse_array_dims();
        void parse_grid_dims();

    public:
        NCConnect(const String &name, const int mode);
        ~NCConnect();

        int &ncid();
        int ndims();
        int nvars();
        int dim_size(const int dimid);
        const String &dim_name(const int dimid);
        int das_loc(const int varid);
        void parse_das_loc();
        void parse_dims();
    };

{The subclass of Connect used with the NetCDF client library.}

The private data added to the Connect object with this class is as
follows:

<font color='green'>_ncid</font> : This is only used for access to local files. It is the \[<http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html><cite>NetCDF</cite>\] file descriptor, or file ID.

<!-- -->

<font color='green'>_nvars</font> : The number of variables in data file.

<!-- -->

<font color='green'>_ndims</font> : The number of dimensions found. Note that not all the variables in a \[<http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html><cite>NetCDF</cite>\] file have the same number or set of dimensions. This number is the list of all the different ones.

<!-- -->

<font color='green'>_dim_name\[MAX_NC_DIMS\]</font> : The names of the dimensions found in the data file.

<!-- -->

<font color='green'>_dim_size\[MAX_NC_DIMS\]</font> : An array containing the size

` of each dimension found.`

<font color='green'>_das_loc\[MAX_NC_VARS\]</font> : An index into a table of

` attributes.`

This additional data allows the client library to emulate most of the
\[<http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html><cite>NetCDF</cite>\]
query functions properly and *locally* .

Now look at the recoded open call of the
\[<http://www.unidata.ucar.edu/software/netcdf/guide.txn_toc.html><cite>NetCDF</cite>\]
library in figure 3.2. The new function has exactly the same type as the
original implementation; it takes the same number and type of arguments
and returns the same type. The first operation performed by the new open
call is to create a Connect object (actually an NCConnect object) using
the arguments passed to the open call.

After the open call creates the new NCConnect object, it makes sure that
the user is not trying to open the remote dataset for writing and, if
they are not, reads the DAS and DDS from the dataset. Once read, the DAS
and DDS objects are parsed using NCConnect's member functions
<font color='green'>parse_das_loc</font> and
<font color='green'>parse_dims</font>. These two member functions, along
with the additional state variables in NCConnect, effectively create the
virtual connection. Subsequent calls to the client library for
information about the variables (e.g., their size, names, etc.) will be
answered using information stored in the symbol table in the NCConnect
object.

    int
    ncopen(const char *path, int cmode)
    {
        int id;
        NCConnect *c = new NCConnect(path);

        if(cmode != NC_NOWRITE) {
            delete c;
            return(-1);
        }

        c->request_das();
        c->request_dds();

        c->parse_das_loc();
        c->parse_dims();

        return(conns.add_connect(c)) ;
    }

{The recoded open call of NetCDF.}

The code above checks to see whether the user is trying to open a
connection for writing. Since OPeNDAP is a read-only protocol, this
attempt must fail.