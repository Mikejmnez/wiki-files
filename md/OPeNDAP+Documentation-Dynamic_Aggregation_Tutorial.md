### Overview

Dynamic aggregation is achieved through the use of the *scan* element.

The NcML-2.2 *scan* element schema:

``` xml
<xsd:element name="scan" minOccurs="0" maxOccurs="unbounded">
  <xsd:complexType>
    <xsd:attribute name="location" type="xsd:string" use="required"/>
    <xsd:attribute name="regExp" type="xsd:string" />
    <xsd:attribute name="suffix" type="xsd:string" />
    <xsd:attribute name="subdirs" type="xsd:boolean" default="true"/>
    <xsd:attribute name="olderThan" type="xsd:string" />
    <xsd:attribute name="dateFormatMark" type="xsd:string" />
    <xsd:attribute name="enhance" type="xsd:string"/>
  </xsd:complexType>
</xsd:element>
```

This page discusses the use and significance of *scan* in creating
dynamically aggregated datasets.

### Location (Location Location...)

The most important attribute of the scan element is the *scan@location*
element that specifies the top-level search directory for the scan,
relative to the BES data root directory specified in the BES
configuration.

**IMPORTANT**: *ALL* locations are interpreted relative to the BES root
directory and **NOT** relative to the location of the NcML file itself!
This means that all data to be aggregated **must** be in a subdirectory
of the BES root data directory and that these directories *must* be
specified fully, not relative to the NcML file.

For example, if the BES root data dir is "/usr/local/share/hyrax", let
\${BES_DATA_ROOT} refer to this location. If the NcML aggregation file
is in "\${BES_DATA_ROOT}/data/ncml/myAgg.ncml" and the aggregation
member datasets are in "\${BES_DATA_ROOT}/data/hdf4/myAggDatasets", then
the location in the NcML file for the aggregation data directory would
be:

`<scan `**`location="data/hdf4/myAggDatasets"`**` />`

which specifies the data directory relative to the BES data root as
required.

Again, due to security reasons, the data is always searched under the
BES data root. Trying to specify an absolute filesystem path, such as:

`<scan `**`location="/usr/local/share/data"`**` />`

will *NOT* work. This directory will also be assumed to be a
subdirectory of the \${BES_DATA_ROOT}, regardless of the preceding "/"
character.

### Suffix Criterion

The simplest criterion is to match only files of a certain datatype in a
given directory. This is useful for filtering out text files and other
files that may exist in the directory but which do not form part of the
aggregation data.

Here's a simple example:

<?xml version="1.0" encoding="UTF-8"?>

<netcdf title="Example of joinNew Grid aggregation using the scan element.">
` `
` `<aggregation type="joinNew" dimName="filename">
`   `<variableAgg name="dsp_band_1"/>` `
`   <scan location="data/ncml/agg/grids" `**`suffix=".hdf"`**` />`
` `</aggregation>` `
` `
</netcdf>

Assuming that the specified location "data/ncml/agg/grids" contains no
subdirectories, this NcML will return all files in that directory that
end in ".hdf" in alphanumerical order. In the case of our installed
example data, there are four HDF4 files in that directory:

    data/ncml/agg/grids/f97182070958.hdf
    data/ncml/agg/grids/f97182183448.hdf
    data/ncml/agg/grids/f97183065853.hdf
    data/ncml/agg/grids/f97183182355.hdf

These will be included in alphanumerical order, so the scan element will
in effect be equivalent to the following list of <netcdf> elements:

    <netcdf location="data/ncml/agg/grids/f97182070958.hdf"/>
    <netcdf location="data/ncml/agg/grids/f97182183448.hdf"/>
    <netcdf location="data/ncml/agg/grids/f97183065853.hdf"/>
    <netcdf location="data/ncml/agg/grids/f97183182355.hdf"/>

By default, scan will search subdirectories, which is why we mentioned
"grids has no subdirectories". We discuss this in the next section.

### Subdirectory Searching (The Default!)

If the author specifies the *scan@subdirs* attribute to the value "true"
(which is the default!), then the criteria will be applied recursively
to any subdirectories of the *scan@location* base scan directory as well
as to any regular files in the base directory.

For example, continuing our previous example, but giving a higher level
location:

<?xml version="1.0" encoding="UTF-8"?>

<netcdf title="joinNew Grid aggregation using the scan element.">
`  `
` `<aggregation type="joinNew" dimName="filename">
`   `<variableAgg name="dsp_band_1"/>` `
`   `
`   <scan location="data/ncml/agg/" suffix=".hdf" `**`subdirs="true"`**`/>`
` `</aggregation>` `
` `
</netcdf>

Assuming that only the "grids" subdir of "/data/ncml/agg" contains HDF4
files with that extension, the same aggregation as prior will be
created, i.e. an aggregation isomorphic to:

    <netcdf location="data/ncml/agg/grids/f97182070958.hdf"/>
    <netcdf location="data/ncml/agg/grids/f97182183448.hdf"/>
    <netcdf location="data/ncml/agg/grids/f97183065853.hdf"/>
    <netcdf location="data/ncml/agg/grids/f97183182355.hdf"/>

The *scan@subdirs* attribute is much for useful for turning off the
default recursion. For example, if recursion is **NOT** desired, but
only files with the given suffix in the given directory are required,
the following will do that:

<?xml version="1.0" encoding="UTF-8"?>

<netcdf title="joinNew Grid aggregation using the scan element.">
` `
` `<aggregation type="joinNew" dimName="filename">
`   `<variableAgg name="dsp_band_1"/>` `
`   `
`   <scan location="data/ncml/agg/grids" suffix=".hdf" `**`subdirs="false"`**`/>`
` `</aggregation>` `

</pre>

### OlderThan Criterion

The *scan@olderThan* attribute can be used to filter out files that are
"too new". This feature is useful for excluding partial files currently
being written by a daemon process, for example.

The value of the attribute is a duration specified by a number followed
by a basic time unit. The time units recognized are:

- **seconds**: { s, sec, secs, second, seconds }
- **minutes**: { m, min, mins, minute, minutes }
- **hours**: { h, hour, hours }
- **days**: { day, days }
- **months**: { month, months }
- **years**: { year, years }

The strings inside { } are all recognized as referring to the given time
unit.

For example, if we are following our previous example, but we suspect a
new HDF file may be written at any time and usually takes 5 minutes to
do so, we might use the following NcML:

<?xml version="1.0" encoding="UTF-8"?>

<netcdf title="joinNew Grid aggregation using the scan element.">
` `
` `<aggregation type="joinNew" dimName="filename">
`   `<variableAgg name="dsp_band_1"/>` `
`   <scan location="data/ncml/agg/grids" suffix=".hdf" subdirs="false" `**`olderThan="10 mins"`**` />`
` `</aggregation>

</netcdf>

Assuming the file will always be written withing 10 minutes, this files
does what we wish. Only files whose modification date is older than the
given duration from the current system time are included.

**NOTE** that the modification date of the file, not the creation date,
is used for the test.

### Regular Expression Criterion

The *scan@regExp* attribute may be used for more complicated filename
matching tests where data for multiple variables, for example, may live
in the same directory by whose filenames can be used to distinguish
which are desired in the aggregation. Additionally, since the pathname
including the location is used for the test, a regular expression test
may be used in conjunction with a recursive directory search to find
files in subdirectories where the directory name itself is specified in
the regular expression, not just the filename. We'll give examples of
both of these cases.

We also reiterate that this test is used *in conjunction* with any other
tests --- the author may also include a suffix and an olderThan test if
they wish. All criteria must match for the file to be included in the
aggregation.

We recognize the POSIX regular expression syntax. For more information
on regular expressions and the POSIX syntax, please see:
<http://en.wikipedia.org/wiki/Regular_expression>.

We will give two basic examples:

- Finding all subdirectories with a given name
- Matching a filename starting with a certain substring

#### Matching a Subdirectory Name

Here's an example where we use a subdirectory search to find ".hdf"
files in all subdirectories named "grids":

<?xml version="1.0" encoding="UTF-8"?>

<netcdf title="Example of joinNew Grid aggregation using the scan element with a regexp">
` `
` `<aggregation type="joinNew" dimName="filename">
`   `<variableAgg name="dsp_band_1"/>` `
`   `
`   <scan `
`      location="data/" `
`      subdirs="true" `
`      `**`regExp="^.*/grids/.+\.hdf$"`**
`      />`
` `</aggregation>` `
</netcdf>

The regular expression here is "^.\*/grids/.+\\hdf". Let's pull it apart
quickly (this is not intended to be a regular expression tutorial):

The "^" matching the beginning of the string, so starts at the beginning
of the location pathname. (without this we can match substrings in the
middle of strings, etc)

We then match ".\*" meaning 0 or more of any character.

We then match the "/grids/" string explicitly, meaning we want all
pathnames that contain "/grids/" as a subdirectory.

We then match ".+" meaning 1 or more of any character.

We then match "\\" meaning a literal "." character (the backslash
"escapes" it).

We then match the suffix "hdf".

Finally, we match "\$" meaning the end of the string.

So ultimately, this regular expression finds all filenames ending in
".hdf" that exist in some subdirectory named "grids" of the top-level
location.

In following with our previous example, if there was only the one
"grids" subdirectory in the \${BES_DATA_ROOT} with our four familiar
files, we'd get the same aggregation as before.

#### Matching a partial filename

Let's say we have a given directory full of data files whose filename
prefix specifies which variable they refer to. For example, let's say
our "grids" directory has files that start with "grad" as well as the
files that start with "f" we have seen in our examples. We still want
just the files starting with "f" to filter out the others. Here's an
example for that:

<?xml version="1.0" encoding="UTF-8"?>

<netcdf title="Example of joinNew Grid aggregation using the scan element with a regexp">
` `
` `<aggregation type="joinNew" dimName="filename">
`   `<variableAgg name="dsp_band_1"/>` `
`   `
`   <scan `
`      location="data/" `
`      subdirs="true" `
`      `**`regExp="^.*/grids/f.+\.hdf$"`**
`      />`
` `</aggregation>` `
</netcdf>

Here we match all pathnames ending in "grids" and files that start with
the letter "f" and end with ".hdf" as we desire.

### Date Format Mark and Timestamp Extraction

This section shows how to use the *scan@dateFormatMark* attribute along
with other search criteria in order to extract and sort datasets by a
timestamp encoded in the filename. All that is required is that the
timestamp be parseable by a pattern recognized by the Java language
"SimpleDateFormat" class
([java.text.SimpleDateFormat](http://java.sun.com/j2se/1.4.2/docs/api/java/text/SimpleDateFormat.html)),
which has also been implemented in C++ in the [International Components
for Unicode](http://site.icu-project.org/) library which we use.

We base this example from the Unidata site [Aggregation
Tutorial](http://www.unidata.ucar.edu/software/netcdf/ncml/v2.2/Aggregation.html).
Here we have a directory with four files whose filenames contain a
timestamp describable by a SimpleDataFormat (SDF) pattern. We will also
use a regular expression criterion and suffix criterion in addition to
the dateFormatMark since we have other files in the same directory and
only wish to match those starting with the characters "CG" that have
suffix ".nc".

Here's the list of files (relative to the BES data root dir):

    data/ncml/agg/dated/CG2006158_120000h_usfc.nc
    data/ncml/agg/dated/CG2006158_130000h_usfc.nc
    data/ncml/agg/dated/CG2006158_140000h_usfc.nc
    data/ncml/agg/dated/CG2006158_150000h_usfc.nc

Here's the NcML:

<?xml version="1.0" encoding="UTF-8"?>

<netcdf title="Test of joinNew aggregation using the scan element and dateFormatMark">
`  `
` `<aggregation type="joinNew" dimName="fileTime">
`   `<variableAgg name="CGusfc"/>`  `
`   <scan `
`       location="data/ncml/agg/dated" `
`       suffix=".nc" `
`       subdirs="false"`
`       regExp="^.*/CG[^/]*"`
`       `**`dateFormatMark="CG#yyyyDDD_HHmmss"`**
`   />`
` `</aggregation>` `

</netcdf>

So here we joinNew on the new outer dimension *fileTime*. The new
coordinate variable **fileTime**\[*fileTime*\] for this dimension will
be an Array of type String that will contain the parsed [ISO
8601](http://en.wikipedia.org/wiki/ISO_8601) timestamps we will extract
from the matching filenames.

We have specified that we want only Netcdf files (suffix ".nc") which
match the regular expression "^.\*/CG\[^/\]\*". This means match the
start of the string, then any number of characters that end with a "/"
(the path portion of the filename), then the letters "CG", then some
number of characters that do *not* include the "/" character (which is
what "\[^/\]\*" means). Essentially, we want files whose basename (path
stripped) start with "CG" and end with ".nc". We also do not want to
recurse, but only look in the location directory "/data/ncml/agg/dated"
for the files.

Finally, we specify the *scan@dateFormatMark* pattern to describe how to
parse the filename into an ISO 8601 date. The *dateFormatMark* is
processed as follows:

- Skip the *number* of characters prior to the "#" mark in the pattern
  while scanning the base filename (no path)
- Interpret the next characters of the file basename using the given
  SimpleDateFormat string
- Ignore any characters after the SDF portion of the filename (such as
  the suffix)

First, note that we **do not match** the characters in the
dateFormatMark --- they are simply counted and skipped. So rather than
"CG#" specifying the prefix before the SDF, we could have also used
"XX#". This is why we must also use a regular expression to filter out
files with other prefixes that we do not want in the aggregation. Note
that the "#" is just a marker for the start of the SDF pattern and
doesn't count as an actual character in the matching process.

Second, we specify the dateFormatMark (DFM) as the following SDF
pattern: "yyyyDDD_HHmmss". This means that we use the four digit year,
then the day of the year (a three digit number), then an underscore
("_") separator, then the 24 hour time as 6 digits. Let's take the
basename of the first file as an example:

"CG2006158_120000h_usfc.nc"

We skip two characters due to the "CG#" in the DFM. Then we want to
match the "yyyy" pattern for the year with: "2006".

We then match the day of the year as "DDD" which is "158", the 158th day
of the year for 2006.

We then match the underscore character "_" which is only a separator.

Next, we match the 24 hour time "HHmmss" as 12:00:00 hours:mins:secs
(i.e. noon).

Finally, any characters after the DFM are ignored, here "h_usfc.nc".

We see that the four dataset files are on the same day, but sampled each
hour from noon to 3 pm.

These parsed timestamps are then converted to an ISO 8601 date string
which is used as the value for the coordinate variable element
corresponding to that aggregation member. The first file would thus have
the time value "2006-06-07T12:00:00Z", which is 7 June 2006 at noon in
the GMT timezone.

The matched files are then **sorted using the ISO 8601 timestamp as the
sort key** and added to the aggregation in this order. Since ISO 8601 is
designed such that lexicographic order is isomorphic to chronological
order, this orders the datasets monotonically in time from past to
future. This is different from the <scan> behavior *without* a
dateFormatMark specified, where files are ordered lexicographically
(alphanumerically by full pathname) --- this order may or may not match
chronological order.

If we project out the ASCII dods response for the new coordinate
variable, we see all of the parsed timestamps and that they are in
chronological order:

    String fileTime[fileTime = 4] = {"2006-06-07T12:00:00Z",
    "2006-06-07T13:00:00Z",
     "2006-06-07T14:00:00Z",
    "2006-06-07T15:00:00Z"};

We also check the resulting DDS to see that it is added as a map vector
to the Grid as well:

    Dataset {
        Grid {
          Array:
            Float32 CGusfc[fileTime = 4][time = 1][altitude = 1][lat = 29][lon = 26]
    ;
          Maps:
            String fileTime[fileTime = 4];
            Float64 time[time = 1];
            Float32 altitude[altitude = 1];
            Float32 lat[lat = 29];
            Float32 lon[lon = 26];
        } CGusfc;
        String fileTime[fileTime = 4];
    } joinNew_scan_dfm.ncml;

Finally, we look at the DAS with global metadata removed:

    Attributes {
      CGusfc {
            Float32 _FillValue -1.000000033e+32;
            Float32 missing_value -1.000000033e+32;
            Int32 numberOfObservations 303;
            Float32 actual_range -0.2876400054, 0.2763200104;
            fileTime {
    --->            String _CoordinateAxisType "Time";
            }
            CGusfc {
            }
            time {
                String long_name "End Time";
                String standard_name "time";
                String units "seconds since 1970-01-01T00:00:00Z";
                Float64 actual_range 1149681600.0000000, 1149681600.0000000;
            }
            altitude {
                String long_name "Altitude";
                String standard_name "altitude";
                String units "m";
                Float32 actual_range 0.000000000, 0.000000000;
            }
            lat {
                String long_name "Latitude";
                String standard_name "latitude";
                String units "degrees_north";
                String point_spacing "even";
                Float32 actual_range 37.26869965, 38.02470016;
                String coordsys "geographic";
            }
            lon {
                String long_name "Longitude";
                String standard_name "longitude";
                String units "degrees_east";
                String point_spacing "even";
                Float32 actual_range 236.5800018, 237.4799957;
                String coordsys "geographic";
            }
        }
        fileTime {
    --->     String _CoordinateAxisType "Time";
        }
    }

We see that the aggregation has also automatically added the
"_CoordinateAxisType" attribute and set it to "Time" (denoted by the
"--\>") as defined by the NcML 2.2 specification. The author may add
other metadata to the new coordinate variable as discussed previously.

### Order of Inclusion

In cases where a dateFormatMark is *not* specified, the member datasets
are added to the aggregation in alphabetical order *on the full
pathname*. This is important in the case of subdirectories since the
path of the subdirectory is taken into account in the sort.

In cases where a dateFormatMark *is* specified, the extracted ISO 8601
timestamp is used as the sorting criterion, with older files being added
before newer files.

[Category:Aggregation](Category:Aggregation "wikilink")
[Category:NCML](Category:NCML "wikilink")