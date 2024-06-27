## Overview

We have implemented a simple timer class "BESStopWatch" that provides
log based time marking along with guaranteed stopping vis-a-vis the
timer passing out of scope and its destructor method being called.

## Usage

This class is implemented so that the timer is stopped and the log
updated when the destructor is called. This allows the class to be
declared with local scope (on the stack) and we are then assured that
the timer's destructor will be called when the method exits - for
whatever reason. If the timer is never started then no timing
information will be written to the log.

A typical usage would look like this:

``` cpp
#include <BESDebug.h>
#include "BESStopWatch.h"

bool ClassName::methodName(BESDataHandlerInterface &dhi){
    BESStopWatch sw;
    if (BESISDEBUG( TIMING_LOG ))
        sw.start("ClassName::methodName", dhi.data[REQUEST_ID]);

    // Do all the stuff you're trying to time…

    return ret;
}
```

**BESStopWatch** has two **start()** methods. One takes as it's
parameter the name of the thing that is being timed - typically this is
the *class::method* name of the calling method. The other takes the name
of the thing that is being timed AND the request ID string sent from the
client (the OLFS in this case). This allows the relevant components of
the client and server logs to be compared. The client request ID can be
retrieved from an instance of BESDataHandlerInterface. For parts of the
call tree that do not have access to the BESDataHandlerInterface object
the single parameter **start()** method is recommended.

## Invoking

Start the BES like this:


`besctl start -d "cerr,timing"`

Or, to get better formatted output you can use the shell script
*timingIndent* located in the *timing* directory of the BES project:


`besctl stop; besctl start -d "bes.log,timing"; tail -f bes.log | ./timing/timingIndent`