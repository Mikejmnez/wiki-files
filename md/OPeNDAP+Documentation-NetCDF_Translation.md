NetCDF Client Library Data Type Translation

N.B.: The Wiki was down for several months total time starting in early
December 2004 and lots of entries are missing. The library has been
renamed as libnc-dap and was released on 13 May 2005 after many lapses
in development. The library now has its CVS module. It requires libdap++
3.5.x. -- Main.JamesGallagher - 16 May 2005

## Goal/Vision

We want the <nop>NetCDF CL to be able to read from any data source that
is served by a DAP-compatible server. This will enable access for any
<nop>OPeNDAP program linked with the C-linguage netCDF CL (Ferret,
<nop>GrADS, ...) to grids and arrays that are embedded in structures and
to the broad range of structures used to represent 'point data'
(scattered profiles, Lagrangian drifter tracks, scattered time series,
scattered point observations) which will greatly extend the types of
problems that the tool (and others like it which use netCDF to access
data) can be used to address.

To do this I will extend the current 'translation' capabilities so that
Sequences appear as collections of arrays. I will also add support so
that attributes are correctly translated along with the variables.

This is a continuation of the work Reza started, to enable the netCDF CL
to read from data sources no matter what data types they use. The
earlier version of the CL could only read data sources with arrays (and
with hacked support for Grids). We've extended the code so that it can
now read Structures, too. Still left is support for Sequences, which
this project will add.

The target users are developers of analysis programs which use netCDF to
data. However, since interaction with point-data data sources is usually
interactive, target users will also be end users.

## Use cases -- updated 25 Oct 2004

I moved the [NetCDF Translation Use
Cases](NetCDF_Translation_Use_Cases "wikilink") to a separate document.
-- Main.JamesGallagher - 25 Oct 2004

### From Steve Hankin: Added -- Main.JamesGallagher - 27 Jul 2004

I sense that my role in this conversation is to help to "think out loud"
just **how far** down the road towards translation it makes sense to go.
So I'll go topic by topic in increasing levels of implementation
complexity and explore this -- apologies if it runs on.

#### Background:

Are you working from the API specification that Joe Wielgosz (sp) (et.
al.) wrote on this subject? One version of that is still on-line at
[Translation Spec draft
4](http://www.po.gso.uri.edu/tracking/opendap/translation/translationspecdraft4.html)

There were three "styles" of translation that were conceptually
developed. I don't recall for sure all three of the names given to them,
but I will call them "flatten", pointer, and "array"

- "flatten" -- flattens nested Sequences into a single Sequence with

repeated data values

- "pointer" -- I believe inner and outer sequences became independent

arrays, where the inner Sequences were appended one to the next.
Additional variables were synthesized so that the outer array elements
could point to the correct starting position of the elements in the
inner sequence.

- "array" -- knowing the maximum length of the (selected) Sequences

(or coaching from the client), it turns nested sequences into
multi-dimensional arrays.

Part of the proposed API was that the client could supply simple
directives to coach a server into what maximum array sizes to return.
This is important. It eliminates issues about overly long times to
perform ncopn. The "100 elements" that your Twiki refers to represents a
default limit, if the client fails to specify a specific limit.

Clearly, the full 3-style approach is over-kill. I'd eliminate "array"
right off the bat. The "pointer" arrangement seems the most powerful,
general (and in many ways) straightforward of all of them. The "flatten"
is appealing because the result set of an [RDBMS](RDBMS "wikilink")
operation **is** a flattened answer. So flatten is sufficient to allow a
netCDF client functionally able to perform a relational query through
[OPeNDAP](OPeNDAP "wikilink").

Note that constraint expressions must be included in the URLs to be
"opened" (translated). (As described in your use case 3). It is vital.
The [OPeNDAP](OPeNDAP "wikilink") constraint expression syntax is "the
API" for making requests from the sequence servers. Under "Risks" the
Twiki suggests "Users may find that reading from point data sources is
too different from raster/array/Grid data sources and may not use the
code." Speaking from the p.o.v. of a Ferret user, it will be the syntax
of constraint expressions that represents the "new" knowledge they need.
Working with the 1D data that gets read after the constraint expression
has been typed in will be familiar and easy. One-dimensional data sets
are after all common in netCDF.

#### Structures

There is no question that the translation of
[OPeNDAP](OPeNDAP "wikilink") arrays and grids that are embedded into
structures has plenty of "users". It will enable us to pull lots of
"gridded" HDF datasets into the [NVODS](NVODS "wikilink") LAS that have
been out of reach. (It is probably essential to the PMEL contribution to
the [REASoN](REASoN "wikilink") proposal.) It would be a similar
break-through for [GrADS](GrADS "wikilink") users. One issue that we may
not have explored in the past: since these HDF datasets are satellite
data with Grids-in-Strictures they likely need to be time-aggregated.
Fortunately, if they become accessible through the netCDF CL both GDS
and FDS "should" (in principle) be able to do this. We'll see what
problems materialize ...

#### Single level Sequences

The ability to read simple (Use Case 1) Sequences has many potential
customers. The "catalog servers", themselves, represent a source of this
class of data -- translation will make these accessible to netCDF
[OPeNDAP](OPeNDAP "wikilink") applications. This should also be a
relatively "easy" problem, I'd think.

#### 2-level nested Sequences

Firstly, the question of how useful this is to client depends critically
upon the available of data sources. In the past such data sources have
been hard to come by. The GDS/BUFR server has added some important data
streams -- using a rigidly-defined doubly nested structure. The
[DAPPER](DAPPER "wikilink") server has followed suit. So we have some
important data using a well-defined small subset of Sequence nestings.
As time has gone on we have seen i) [GrADS](GrADS "wikilink") has
developed a private API (companion to GDS/BUFR); ii) LAS has developed a
"hack" based upon [ASCIIVAL](ASCIIVAL "wikilink"); iii) ncbrowse has
gone to using the native Java OPeNDAP libraries. So time has erased a
significant fraction of the user demand. If translation of 2-level
nested Sequences existed in the netCDF CL it would get used by Ferret,
for sure. Further exploration of cost/benefit may be in order.

## Notes about the paper

The design paper was written by Joe W., et al.:[Translation Spec draft
4](http://www.po.gso.uri.edu/tracking/opendap/translation/translationspecdraft4.html)

In Section 5, The client-side parameters are prefixed to the URL. This
will be hard to implement since the URL is passed into Connect and
parsed there. It would be much easier to prefix the parameters to the CE
which will be extracted as a unit (not parsed) by the client-side code.
Th CL can grab this and process it more in NCConnect.

In section 8.5, Non-nested Sequences are translated into arrays whose
names are simply the names of the original Sequence's fields. That
introduces the possibility of a name collision and also will break the
'association by name' scheme on which the current translation code
relies.

## One solution

All data sources are first 'opened' by the <nop>NetCDF CL before any
other operation is performed. When a data source is opened, the DDS and
DAS are read. At this time, variables are translated. When a Sequence is
found, read its values. Use the CE or default to the 'first 100 values'
if no CE is given. Hold the values until the client program asks for
them.

### Solution, updated -- Main.JamesGallagher - 13 Sep 2004

The current code will translate a single-level Sequence that is composed
of simple variables (not arrays). This corresponds to a great many data
sources, so it's useful. the current scheme uses name equivalence to map
variables in the returned [DataDDS](DataDDS "wikilink") to variable in
the 'translated DDS' so that values can be extracted from the
[DataDDS](DataDDS "wikilink") and then returned as if they can from the
types in the translated DDS. Using this scheme, the 'dot notation' is
used to ensure that the translated variable can be mapped to the
original variable. This is also used when building the CE so that the
server will know which variable to return.

In the current design, a method in NCConnect performs the translation
(translate_dds()). This method translates variables in the DDS 'in
place.' That is, it removes the old variable and replaces it with a new
variable. The original DDS is modified. When data are requested,
however, the original type/variable is returned. Methods in the NCAccess
interface figure out how to access the data and return it in a way that
will be consistent with types in the translated DDS.

A better design would be to translate the types on the fly as the DDS is
receieved. This could be done by modifying the types' virtual
constructors. The virtual ctor for Structure would build arrays, et
cetera. As an alternative, the NCConnect::translate_dds() method could
return the translated DDS while not altering the original DDS. Ether way
would be fine, although the latter would probably be easier to
understand... The two DDS objects would hold variables which could
reference each other. That is, through the NCAccess interface, it could
be possible to go from source to derived variable and back.

Given this configuration, it should be possible to translate the DDS
returned with the data and access the data values using the translated
variables. Note that this could also be used to build the CE since the
translated variable could ask the original variable what its name is and
can find the names of its parents (and thus, the original variable's
full path, which is needed to request the variable).

Note that while access to fields originally in a Structure is pretty
simple, it's not so easy for those from a Sequence. Even though the
translated variables are all arrays, you *must* read the data using the
Sequence object. See Sequence::read_row(). This is why you cannot use
the destructive NCConnect::translate_dds() to translate the returned
[DataDDS](DataDDS "wikilink") (because the CL cannot read data without
the Sequence type which has been removed as part of the translation
process).

## Risks

Users may find that reading from point data sources is too different
from raster/array/Grid data sources and may not use the
code.%GREEN%Steve says this should not be a problem in
practice.%ENDCOLOR%

Users may provide CEs which return huge amounts of data. There's no way
to ask a server how much data will be returned before actually getting
the data.

## Schedule -- updated -- Main.JamesGallagher - 16 May 2005

Refactor so that the netCDF API code is separate from the CL interface
with the server. About 2 days. %GREEN%This took close to two weeks, I
think. A good bit of the time (75%) spent on Attribute support was
refactoring existing code to figure what it did and how it did it. I
also spent some time *fixing* the libdap support for attributes added
when I added attribute tables to
[BaseTypes](BaseTypes "wikilink").%ENDCOLOR%

Sequence Support: 8/2%RED%8/27%ENDCOLOR%: Reread document from Joe W.
8/2: Build code to grab 100 records from a single level sequence and
intern as Arrays. 8/3%RED%8/30%ENDCOLOR%: more detailed planning and
schedule. %GREEN%completed 9/13. Close to 50% of the time was spent
refactoring the existing NC client library code and learning how it
worked. I refactored it so that functions which used large switch
statements becaome methods in a new interface called `NCAccess`. This
interface contains new methods which should be common to all of the
NC_type_ classes. These functionts build the CE which will ask for data
requested by the client given that the variable the client sees is not
the variable in the dataset, and extract the returned value, given that
the variable returned is not the type the client expects. See One
solution above for a comment about this.%ENDCOLOR%

Attribute Support: 7/26 -- 8/2: Complete support for attributes. Add
support for global attributes and for migrating attributes of/for
variables that are 'translated.' Must also fix the
DDS::transfer_attributes() method. %GREEN%This was actually completed on
8/4, with a little more testing and a bug in libdap fixed on 8/26. I was
on other tasks and on vacation for between 8/3 and 8/25.%ENDCOLOR%

Restart work: 10/25: After almost a month hiatus working on the Stennis
tutorial/workshop and [SEEDS](SEEDS "wikilink") meeting poster (and DAP
2 RFC changes), I'm back on this task. At this point translation of
simple Sequences (those with only atomic types) works, although without
much flexibility. I'll spend a day going over the design written up by
Joe W, et al., and update the use cases. This will make it easier to
figure which parts should be done together (e.g., Structure translation
now works, but I don't think the attributes that are supposed to be
created are, in fact, created. So grouping the attributes with Structure
translation will make it easy to see what needs to be done to complete
that feature). I'll group the Use cases so that the three (or four)
distincet features are easy to identify. %GREEN%Done 10/25 except for
Strings.%ENDCOLOR%

Start work on common attributes which should be added to translated
datasets and variables: 10/25--10/26. %GREEN%Done. 10/26%ENDCOLOR%

Start work on client-side parameter parsing and devise a schedule:
10/27-10/28. %GREEN%Done 10/28.%ENDCOLOR%

Schedule for remaining use cases:

1 Add processing of the limit parameter to Sequence and Stirng: 5 days.
11/1-11/5 %GREEN% Started 10/28, 11/1-2 on other things, works for
Sequences 11/3. Move the String translation task up and start on it.
Released w/o string limit.%ENDCOLOR% 1 String translation: 2 days
11/18-11/19 %GREEN% Start 11/3 I'm not really sure how to proceed on
this or it if needs to be done. Complete translation for Structure and
Sequence and release. Revisit strings later. 11/3. Added; works Jan
2005??%ENDCOLOR% 1 Complete translation of Structures and Sequences when
they are nested. %GREEN% Start 11/4. Done 11/16 But, must revisit value
transmission, which seems broken. Fixed April 2005 %ENDCOLOR% 1 Test
libdap++ after changes. %GREEN%Done 11/16. %ENDCOLOR% 1 Test libnc-dods
after changes and fix data value translation. %GREEN% Start 11/16. Done
March 2005 %ENDCOLOR% 1 Add preload parameter (only for Sequence?): 3
days. 11/8-11/10 %RED% Bail on this for the current version. %ENDCOLOR%
1 CE processing: 5 days. 11/11-17 %GREEN% Completed sometime in April
2005 %ENDCOLOR% 1 Package/Test: 5 days: 11/22-11/26 %GREEN% Completed in
May 2005 %ENDCOLOR%

## Process/Tools

I'm trying the Rational Unified Process and the Eclipse IDE.

-- Main.JamesGallagher - 21 Jul 2004

[category:](category: "wikilink")