# Linking Your Program

To link a user program to the OPeNDAP client-library version of a data
access API library, you need only substitute the name of the
reimplemented API library for the original, and add the OPeNDAP Data
Access Protocol library (<font color='green'>libdap++</font>) to the
link list.

Because the DAP library and the API have circular dependencies they must
each be included on the linker command line twice. An example of this
can be seen in the OPeNDAP-NetCDF Makefile; the value of *LIBS* is
passed to the linker <font color='green'>ld</font>. Note that the API
library is listed first.

    LIBS = -lnc-dods -ldap++ -lnc-dods -ldap++

You should have users link their programs using
<font color='green'>gcc</font> or <font color='green'>g++</font> since
the libraries are all built using those tools. In particular,
<font color='green'>g++</font> includes libstdc++ (and libg++) by
default when it builds an executable program from object modules. If you
use <font color='green'>gcc</font> instead of
<font color='green'>g++</font> when you link, be sure to include these
libraries as well after all the libraries listed above. If you don't use
<font color='green'>gcc</font>, but instead use the linker directly
(i.e, you call <font color='green'>ld</font> yourself) you are on your
own - you can use <font color='green'>gcc -V</font> to determine what
flags and additional libraries it uses that are specific to your system
and then experiment with those. We cannot tell you how to proceed since
each UNIX variant requires different flags and libraries.