# Guidelines for including headers

In our code that uses *libdap*, we should make sure to prefix the libdap
headers that are included with

    libdap/

like this:

`   #include `<libdap/BaseType>

This is because some of the names used by the libdap headers are also
used by C++ or the STL or are close enough to those names to cause
problems with various software development tools.

It's best to use double quotes for headers from within a project and
angle brackets for those from outside the project. So, use angle
brackets with libdap headers in the BES code; use double quotes for BES
headers in the BES code.

Group headers like this:

- First include "config.h"
- Then system headers
- Then C/C++ library headers
- Then libraries (like libdap)
- Then BES headers

Never include

    config.h

in a header that will be installed (which is essentially every header).

Minimise headers included in other headers.

Never put

    using namespace xxx

in a header - it's a great way to cut down on the wordiness of an
implementation (.cc) but in a header it will introduce that *using*
statement to all of the code that directly or indirectly includes the
header.