# The Emerging DODS Data Standards

Using DODS, a scientist or data center can make their data readily
available to anyone who knows about it. But there is a difference
between "readily available" and "readily usable." Just because DODS can
easily retrieve data doesn't mean that it is easy to use.

The DODS project has found that the single biggest obstacle to ease of
use of a dataset's data is incompatibility of metadata, or data
attributes.\footnote{Please see the \DODSuser for a discussion of

DODS treatment of attributes, or metadata.} This incompatibility can
range from the seemingly simple, such as different data names, to the
far less tractable, such as incompatible time representations.

To address this problem, the DODS project has created the data attribute
standard described in this chapter. This standard is *entirely optional*
. A server can still serve data from a DODS dataset that does not
conform to this standard, and a DODS client can still read that data.
However, a dataset that conforms to this standard will be more easily
read and processed by another user.

> NOTE:The DODS project has an original emphasis on oceanography.
> Therefore, the DODS standard described here has been defined, where
> possible, to be compatible with existing geo-spatial metadata
> conventions, including the COARDS and the ISO Z39.50 GeoProfile
> conventions. It should be noted that the DODS standard is a small
> subset of these metadata definitions, and may well be applicable to
> data from other scientific (or other) disciplines. In addition, the
> architecture of the DODS software does not preclude the adoption of
> alternate data standards that may be more appropriate to those other
> fields.

In a similar effort, attempting to assert some order over incompatible
data attributes among netCDF files, the COARDS project
\[<http://ferret.wrc.noaa.gov/noaa_coop/coop_cdf_profile.html><cite>"Cooperative
Ocean/Atmosphere Research Data Service"</cite>\] has specified some
standard attributes that shall be defined in a dataset. Note that this
has little or nothing to do with netCDF itself. You can have a netCDF
dataset that doesn't comply with the COARDS standard. But it will be
more useful to others if it does.

Note that the DODS architecture allows data attributes to be added to
any dataset *without* modifying the data files themselves. This makes
the task of conforming to the data standard fairly simple, in most cases
consisting of adding a few files to a directory.

Finally, this is not an exhaustive list of attributes possible. Beyond
syntax-checking, the DODS software does no parsing of attribute
structures for a dataset. You are free to add whatever attributes you
choose to any dataset served by a DODS server. If you want to add enough
data attributes to make your metadata 100\\ ISO Z39.50 GeoProfile, there
is no reason not to do so.\footnote{We would suggest that if you do add
GeoProfile attributes not mentioned in this document to your dataset,
you should use the SGML attribute tags as attribute names. In the event
that future DODS servers support a part of the GeoProfile protocol, this
will allow your datasets to be merged seamlessly with that standard.}

## Essential and Optional Attributes

One of the motivating principles of the DODS project is to keep the
burden on the data provider as light as possible. To further decrease
the burden of making data available, the DODS data standard attributes
are separated into two groups:

- Those that are essential to minimal data attribute interoperability,
  and
- Those that are very useful, but not essential.

The second group can be further split up among the more and less useful
attributes.

The DODS project recognizes these as different "classes" of
compatibility. For example, a class 0 DODS dataset is one that is served
by DODS, but does not comply with the DODS data standards, except in the
most minimal way. A class 1 dataset conforms, but only contains the
essential attributes. A class 2 datasets conforms and contains the
essential and the class 2 attributes, and so on. The attributes
described in the following sections are identified by their classes in
this way.

### Global and Variable Attributes

DODS attributes consist of sets of name-value pairs
[(1)](Wiki_Testing/dods-standards "wikilink"). To say that a data
variable has attributes is the same as saying that there is a set of
attributes (in the dataset's DAS) with the same name as this variable.
The set can contain one or more name-value pairs, such as "units" with
"degreesC" and "scale_factor" with "2.35".

Consider the dataset with a DDS like this:

    Dataset {

    Int16 temp[time = 16][lat = 17][lon = 21];

    Float32 lat[lat = 17];

    Float32 lon[lon = 21];

    Float32 time[time = 16];
    } test1;

The DAS for this DODS-compliant dataset might look like this:

    Attributes {

    temp {

    String units "degreesC";

    String long_name "Surface Temperature";

    String missing_value "-32767";

    String scale_factor "0.005";

    String short_name "Temp";

    }

    lat {

    String units "degree North";

    String short_name "Latitude";

    }

    lon {

    String units "degree East";

    String short_name "Longitude";

    }

    time {

    String units "hours from base_time";

    String short_name "Time";

    }

    DODS {

    String conventions "DODS", "COARDS";

    }
    }

One of the attribute containers in this DAS does not correspond to any
data variables. The attributes in this container are said to be "global"
attributes, and modify the entire dataset. There is nothing special
about the names; an attribute container can be called anything at all.
However, the container called "DODS", if it exists, should contain the
global attributes described in the DODS data standard, such as
<font color='green'>conventions</font>,
<font color='green'>Acknowledge</font>, and
<font color='green'>history</font>.

## Utterly Essential Attributes (Class 0)

The following attributes must appear in any DODS dataset for it to be
considered class 0 compliant. These attributes must be defined for
*each* data variable in the dataset.

<font color='green'>long_name</font> : A long descriptive name (title). This could be used for labelling plots, for example. If a variable has no <font color='green'>long_name</font> attribute assigned, the variable name should be used as a default. This corresponds to the "Detailed Variable" field of the GCMD variable naming hierarchy.

<!-- -->

<font color='green'>units</font> : A character array that specifies the units used for the variable's data. The units attribute should be formatted as per the recommendations in the Unidata [<cite>udunits</cite>](http://www.unidata.ucar.edu/packages/udunits/) package.

<!-- -->

<font color='green'>scale_factor</font> : If present for a variable, the data are to be multiplied by this factor after the data are read by the application that accesses the data. One or both of <font color='green'>scale_factor</font> or <font color='green'>add_offset</font> must be present if the data are not stored in the specified units, unless there is also a <font color='green'>transform</font> specified.

<!-- -->

<font color='green'>add_offset</font> : If present for a variable, this number is to be added to the data after it is read by the application that accesses the data. One or both of <font color='green'>scale_factor</font> or <font color='green'>add_offset</font> must be present if the data are not stored in the specified units, unless there is also a <font color='green'>transform</font> specified. If both <font color='green'>scale_factor</font> and <font color='green'>add_offset</font> attributes are present, the data are first scaled before the offset is added.

<!-- -->

<font color='green'>transform</font> : This is a string containing a transformation function used to convert the raw data into the units specifed in the <font color='green'>units</font> attribute.

There are some special cases, outlined in the next two sections.
However, if your dataset contains no null values, and no data stored as
DODS Arrays, the above list is complete.

> NOTE: A DODS Array is a different data type than a Grid, and contains
> less information about its independent variables. For more
> information, see below, or refer to the
> \[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>OPeNDAP
> User Guide</cite>\] .

### Missing Data

If a dataset contains missing data flagged with special values, those
values must be specified in the attribute list of the variable. That is,
if you have a data sequence called <font color='green'>ralph</font>, and
it contains missing values flagged with -999, the DAS for the dataset
should have an attribute container like this:

    Attributes {

    ...

    ralph {

    Float32 missing_value -999.0;

    ...

    }

    ...
    }

The DODS metadata standard provides three categories of missing data.
These attribute values are essential only in the sense that they must be
in the data variable's attribute container *if you use them* . If your
data doesn't use these values---that is, if there are no missing values
flagged with special numeric values--these attributes need not be
specified.

<font color='green'>missing_value</font> : This is a conventional name for a missing value that will not be treated in any special way by the client application. This attribute is part of the COARDS standard.

<!-- -->

<font color='green'>null_value</font> : A null value differs from a missing value in that it describes data that isn't there, but shouldn't have been, either. That is, where a missing value might be used to fill in for a sensor malfunction, a null value is used to indicate that no data was taken. A dataset that contained random data interpolated onto a grid might use a null value on those grid points too distant from data values to make an accurate estimate.

<!-- -->

<font color='green'>default_value</font> : A default value is yet another sort of missing data. In this case, data would never have been at those points. Land points in gridded sea-surface temperature data would be default values, as would the end of profile data vectors filled to uniform length. This differs some from the semantics of the COARDS <font color='green'>_FillValue</font>, but perhaps not so far as to prevent the DODS project from adopting the COARDS name as a synonym.

### Array Data

A dataset containing only an array may be missing some important
information about the dataset's independent variables. To make the
dataset conpliant with the Class 0 of the DODS standard, this
information must be included in the attribute list.

The information missing from an Array variable is the location of that
array's corners---the <font color='green'>min</font> and
<font color='green'>max</font> for each dimension---and other
information about the array dimensions. The requirements, therefore, are
that an Array's dimensions be named, and that attribute containers with
those same names be contained in the dataset DAS. That is, for a dataset
with the following array data:

    Dataset {

    Array {

    Byte dsp_band_1[lat = 1024][lon = 1024];

    } dsp_band_1;

The DAS should look something like the following:

    Attributes {

    dsp_band_1 {

    String long_name "AVHRR sea surface temperature data";

    String units "DegreesC";

    Float32 scale_factor 0.15625;

    Float32 add_offset 5.0;

    Float32 missing_value -999.0;

    }

    lat {

    String long_name "Latitude";

    String units "Degrees North";

    Float32 min 0.0;

    Float32 max 70.0;

    }

    lon {

    String long_name "Longitude";

    String units "Degrees East";

    Float32 min -100.0;

    Float32 max 0.0;

    }
    }

If using the name of the dimension would cause a name collision with
some other variable in the dataset, you can use the name of the
dimension prefixed by the name of the array. That is, in the above
example, the attribute containers for the array dimensions would be
<font color='green'>dsp_band_1.lat</font> and
<font color='green'>dsp_band_1.lon</font>.

## Less Essential, But Still Useful Attributes (Class 1)

The optional attributes are divided into two different classes. This
again allows a data provider some latitude in the class of compliance
with the standard.

> <font color='green'>short_name</font> : This is the name of the variable, taken from a list of DODS names (see \appref{std-names}). Including this attribute is a way to ensure that two datasets can be usefully compared with one another on a variable-by-variable basis.
>
> <!-- -->
>
> <font color='green'>Convention</font> : This global attribute is recommended to identify the dataset as conforming to the DODS data standard, identified here. The attribute should be a string with the value "DODS". Note that this is a global attribute, and should appear in the "DODS" attribute container, like this:
>
> <!-- -->
>
>     DODS {
>
>     String Convention "DODS"; }
>
> Note that the presence of the "DODS" attribute container is itself a
> clue to whether the dataset is DODS-compliant or not. To be compliant
> with the GeoProfile, one element in the Convention vector should read
> "<font color='green'>FGDC Content Standards for Digital Geospatial
> Metadata</font>".

## Very Useful Attributes, But Not Essential (Class 2)

This is a set of attributes that has been found to be quite useful in
the use of DODS datasets. A dataset can be considered to be class 2
compliant if it contains more than one of these attributes.

<font color='green'>Acknowledge</font> : This string should contain an acknowledgement paragraph that can appear in papers that use the data from this dataset. For example: "The principal investigators in the production of these data are J.D. Elms, S.D. Woodruff, and

S. Worley
\[<http://www.cdc.noaa.gov/coads/participants.html><cite>cite</cite>\]."
and so on. This is a global attribute, and should appear in the "DODS"
attribute container.

<font color='green'>history</font> : The <font color='green'>history</font> attribute is recommended to record the evolution of the data contained within a DODS data file. Applications which process this data can append their information to this attribute. This is a global attribute, and should appear in the "DODS" attribute container.

<!-- -->

<font color='green'>Data_Use_Policy</font> : If there are any restrictions on the use of the data, they should be noted in the string with this name. This is a global attribute, and should appear in the "DODS" attribute container.

<!-- -->

<font color='green'>Theme</font> : A string containing one or more of the GCMD parameter valid names. See the \[<http://gcmd.gsfc.nasa.gov/cgi-bin/md/valids_display.pl><cite>GCMD Parameters" list maintained at GCMD</cite>\]