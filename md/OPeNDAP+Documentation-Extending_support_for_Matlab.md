Expanding OPeNDAP’s Support for Matlab

Why does this matter?

OPeNDAP’s fundamental mission is to provide support for the Data Access
Protocol and to further its extension by keeping its features in sync
with changes taking place in the rapidly evolving sphere of networked
data systems. One important change that has taking place in the last
decade is the rise in prominence of data analysis tools that effectively
replace programming languages like FORTRAN and (to some extent) C. Two
such examples are the Matlab and IDL tools developed by Mathworks, Inc.
and RSI, Inc., respectively. Many users of the DAP depend on these tools
day in and day out, so continued support for them is crucial both for
those users and for the viability of the DAP.

What is the problem?

Unfortunately our support for these all-important tools has lagged
behind our efforts in other areas. Throughout its existence, OPeNDAP has
maintained an excellent program of server upgrades, and that software is
well regarded by many who rely on it to serve their data. Furthermore,
even though the project is yet to formally complete, the NSF SDCI effort
to modernize our netCDF client library has been, so far, an outstanding
success. Contrast these efforts with the piecemeal approach taken
towards supporting command extensions for Matlab so data from DAP
servers can be read directly into this analysis tool. Because support
has been sporadic, the command extension often lags behind Matlab by as
much as a whole year and does not track changes in the APIs it supports.
Compounding the problems created by this situation is that the current
design and implementation of the command extension is cumbersome and
expensive to maintain. A bad situation keeps getting worse because the
effort needed to sustain the extension is so costly.

Our proposed solution

We propose to redesign and reimplement the Matlab command extensions
using our new C library for client access (called OC) that has been
developed as part of the NSF SDCI project to improve our netCDF
client-library software. There are advantages to switching to this new
library for this command extension. For the Matlab client, a redesign
will eliminate a chief complaint of users that the two programs that
make up the command extension are too hard to install and use. Adopting
a single library as the base for both these and the netCDF
client-library will allow us to amortize many support costs across three
clients (our IDL client already is being rewritten to use the new
library), creating a clearer path for reliable support into the future.

[Category:Google Summer of
Code](Category:Google_Summer_of_Code "wikilink")