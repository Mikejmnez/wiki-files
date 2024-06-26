## Kinds of files the handler will serve

This handler will serve Comma-Separated Values type data. Form many
kinds of files, only very modifications to the data files are needed. If
you have very complex ASCII data (e.g., data with headers), take a look
at the FreeForm handler, too.

### Data file configuration

Given a simple CSV data file, such as would be written out by Excel, add
a single line at the start that provides a name and OpenDAP datatype for
each column. Just as the data values in a given row are separated by a
comma, so are the column names and types. Here is a small example data
file with the added *name<type>* configuration row.


"Station<String>","latitude<Float32>","longitude<Float32>","temperature_K<Float32>","Notes<String>"

"CMWM",-34.7,23.7,264.3,

"BWWJ",-34.2,21.5,262.1,"Foo"

"CWQK",-32.7,22.3,268.4,

"CRLM",-33.8,22.1,270.2,"Blah"

"FOOB",-32.9,23.4,269.69,"FOOBAR"

##### Supported OpenDAP datatypes

The CSV handler supports the following DAP2 simple types: Int16, Int32,
Float32, Float64, String.

##### Dataset representation

The CSV handler will return represent the columns in the dataset as
arrays with the named dimension *record*. For example, the sample data
shown above will be represented in DAP2 by this handler as:

``` c
Dataset {
    String Station[record = 5];
    Float32 latitude[record = 5];
    Float32 longitude[record = 5];
    Float32 temperature_K[record = 5];
    String Notes[record = 5];
} temperature.csv;
```

This is in contrast to the FreeForm handler that would represent these
data as a Sequence with five columns.

For each column, the corresponding Array in the OpenDAP dataset has one
attribute named *type* with a string value of *Int16*, ..., *String*.
However, see below for information on how to add custom attributes to a
dataset.

### Known problems

There are no known problems.

## Configuration parameters

### Configuring the handler

This handler has no specific configuration parameters.

### Configuring Datasets

There are two ways to add custom attributes to a dataset. First, you can
use an *ancillary attribute* file in the same directory as the dataset.
Alternatively, you can use NcML to add new attributes, variables, etc.
See [the NcML Handler
documentation](BES_-_Modules_-_NcML_Module "wikilink") for more
information on that option. Here we will describe how to set up an
ancillary attribute file.

##### Simple attribute definitions

For any OpenDAP dataset, it is possible to write an ancillary attributes
file like the following. If that file has the name *dataset*.`das` then
whenever Hyrax reads *dataset*, it will also read those attributes, and
return them when asked.

``` c
Attributes {
   Station {
      String bouy_type "flashing";
      Byte Age 53;
   }
   Global {
       String DateCompiled "11/17/98";
       String Conventions "CF-1.0", "CF-1.6";
   }
}
```

The format of this file is very simple: Each variable in the dataset may
have a collection of attributes, each of which consists of a type, a
name and one or more values. In the above example, the variable
*Station* will have the additional attributes *bouy_type* and *Age* with
the respective types and values. Note that datasets may also define
global attributes - information about the dataset as a whole - by adding
a section with a name that doesn't match the name of any variable in the
dataset. In this example, I used *Global* for this (because it's
obvious) but I could have used *foo*. Also note the attribute
*Conventions* has two values, *CF-1.0* and 'CF-1.6''.