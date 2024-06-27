# Copyrights for OPeNDAP software

Unless otherwise specified, all software written for OPeNDAP is **Open
Source** and should be copyrighted using the GNU [Free Software
Foundation](http://www.fsf.org/)'s [Lesser General Public
License](http://www.fsf.org/licensing/licenses/lgpl.html).

## What to do

First, you should prefix all of your source code with a header like the
following:

``` c
/////////////////////////////////////////////////////////////////////////////
// This file is part of the "Server4" project, a Java implementation of the
// OPeNDAP Data Access Protocol.
//
// Copyright (c) 2005 OPeNDAP, Inc.
// Author: Nathan David Potter  <nathan's email here>
//
// This library is free software; you can redistribute it and/or
// modify it under the terms of the GNU Lesser General Public
// License as published by the Free Software Foundation; either
// version 2.1 of the License, or (at your option) any later version.
//
// This library is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
// Lesser General Public License for more details.
//
// You should have received a copy of the GNU Lesser General Public
// License along with this library; if not, write to the Free Software
// Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA
//
// You can contact OPeNDAP, Inc. at PO Box 112, Saunderstown, RI. 02874-0112.
/////////////////////////////////////////////////////////////////////////////
```

Make sure to change that header to fit the software you're writing. For
example, not all of the code we produce is a library, so change
'library' to 'software,' 'program,' 'server,' ..., whatever is
appropriate. Put this at the start of all .c, .cc, .h, .java, et c.
files.

Second, in the main directory of the software's distribution, include a
copy of the license in a file named ![COPYING](COPYING.txt "COPYING").
In the README file, which should also appear in the distribution's main
directory, explain that the license is in the file named 'COPYING'.

## What about books and the like?

There is a GNU open source license specifically tailored to
documentation. This is not needed for files like README, INSTALL, but it
is appropriate for larger documents such as our User's Guide. See the
[FSF](http://www.fsf.org/) Web site for details.