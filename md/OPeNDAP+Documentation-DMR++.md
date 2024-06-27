*How to build & deploy dmr++ files for Hyrax*

## What? Why?

It is a fast and flexible way to serve data stored in S3. The dmr++
encodes the location of the data content residing in a binary data
file/object (e.g., an hdf5 file) so that it can be directly accessed,
without the need for an intermediate library API, by using the file with
the location information. The binary data objects may be on a local
filesystem, or they may reside across the web in something like an S3
bucket.

## How Does It Work?

The *dmr++* ingest software reads a data file (see **note**) and builds
a document that holds all of the file's metadata (the names and types of
all of the variables along with any other information bound to those
variables). This information is stored in a document we call the Dataset
Metadata Response (DMR). The *dmr++* adds some extra information to this
(that's the '++' part) about where each variable can be found and how to
decode those values. The *dmr++* is simply an special annotated DMR
document.

This effectively decouples the annotated DMR (*dmr++*) from the location
of the granule file itself. Since dmr++ files are typically
significantly smaller than the source data granules they represent, they
can be stored and moved for less expense. They also enable reading all
of the file's metadata in one operation instead of the iterative process
that many APIs require.

If the dmr++ contains references to the source granules location on the
web, the location of the the dmr++ file itself does not matter.

Software that understands the dmr++ content can directly access the data
values held in the source granule file, and it can do so without having
to retrieve the entire file and work on it locally, even when the file
is stored in a Web Object Store like S3.

If the granule file contains multiple variables and only a subset of
them are needed, the dmr++ enabled software can retrieve just the bytes
associated with the desired variables parts.

**note:** The OPeNDAP software currently supports HDF5 and NetCDF4.
Other formats can be supported, such as zarr.

## Supported Data Formats

The *dmr++* software currently works with *hdf5* and *netcdf-4* files.
(The *netcdf-4* format is a subset of *hdf5* so *hdf5* tools are
utilized for both.) Other formats like *zarr*, *hdf4*, *netcdf-3* are
not currently supported by the *dmr++* software, but support could be
added if requested.

### *hdf5*

The *hdf5* data format is quite complex and many of the options and edge
cases are not currently supported by the *dmr++* software.

These limitations and how to quickly evaluate an *hdf5* or *netcdf-4*
file for use with the *dmr++* software are explained below.

#### *hdf5* filters

The *hdf5* format has several filter/compression options used for
storing data values. The *dmr++* software currently supports data that
utilize the H5Z_FILTER_DEFLATE, H5Z_FILTER_SHUFFLE, and
H5Z_FILTER_FLETCHER32 filters. [You can find more on hdf5 filters
here.](https://support.hdfgroup.org/HDF5/doc/RM/RM_H5Z.html)

#### *hdf5* storage layouts

The *hdf5* format also uses a number of "storage layouts" that describe
various structural organizations of the data values associated with a
variable in the granule file. The *dmr++* software currently supports
data that utilize the H5D_COMPACT, H5D_CHUNKED, and H5D_CONTIGUOUS
storage layouts. These are all of the storage layouts defined by the
*hdf5* library, but others can be added. [You can find more on hdf5
storage layouts
here.](https://support.hdfgroup.org/HDF5/doc1.6/Datasets.html)

#### Is my *hdf5* or *netcdf-4* file suitable for *dmr++*?

To determine the *hdf5* filters, storage layouts, and chunking scheme
used in an *hdf5* or *netcdf-4* file you can use the command:

`h5dump -H -p `<filename>

To get a human readable assessment of the file that will show the
storage layouts, chunking structure, and the filters needed for each
variable (aka DATASET in the *hdf5* vocabulary) [h5dump info can be
found
here.](https://support.hdfgroup.org/HDF5/doc/RM/Tools.html#Tools-Dump)

*h5dump example output:*

`$ h5dump -H -p chunked_gzipped_fourD.h5`
`HDF5 "chunked_gzipped_fourD.h5" {`
`GROUP "/" {`
`  DATASET "d_16_gzipped_chunks" {`
`     DATATYPE  H5T_IEEE_F32LE`
`     DATASPACE  SIMPLE { ( 40, 40, 40, 40 ) / ( 40, 40, 40, 40 ) }`
`     STORAGE_LAYOUT {`
`        CHUNKED ( 20, 20, 20, 20 )`
`        SIZE 2863311 (3.576:1 COMPRESSION)`
`     }`
`     FILTERS {`
`        COMPRESSION DEFLATE { LEVEL 6 }`
`     }`
`     FILLVALUE {`
`        FILL_TIME H5D_FILL_TIME_ALLOC`
`        VALUE  H5D_FILL_VALUE_DEFAULT`
`     }`
`     ALLOCATION_TIME {`
`        H5D_ALLOC_TIME_INCR`
`     }`
`  }`
` }`
`}`

#### Is my netcdf file *netcdf-3* or *netcdf-4*?

It is an unfortunate state of affairs that the file suffix ".nc" is the
commonly used naming convention for both *netcdf-3* and *netcdf-4*
files. You can use the command:

`ncdump -k `<filename>` `

to determine if a *netcdf* file is either classic *netcdf-3* (classic)
or *netcdf-4* (netCDF-4).

- The *netcdf* library must be installed on the system upon which the
  command is issued.

[You can learn more in the NetCDF documentation
here.](http://www.bic.mni.mcgill.ca/users/sean/Docs/netcdf/guide.txn_79.html)

## Building *dmr++* files with get_dmrpp

The application that builds the *dmr++* files is a command line tool
called *get_dmrpp*. It in turn utilizes other executables such as
*build_dmrpp*, *reduce_mdf*, *merge_dmrpp* (which rely in turn on the
*hdf5_handler* and the *hdf5* library), along with a number of UNIX
shell commands.

All of these components are install with each recent version of the
Hyrax Data Server

You can see the *get_dmrpp* usage statement with the command:
`get_dmrpp -h`

### Using *get_dmrpp*

The way that *get_dmrpp* is invoked controls the way that the data are
ultimately represented in the resulting *dmr++* file(s).

The *get_dmrpp* application utilizes software from the Hyrax data server
to produce the base DMR document which is used to construct the *dmr++*
file.

The Hyrax server has a long list of configuration options, several of
which can substantially alter the the structural and semantic
representation of the dataset as seen in the dmr++ files generated using
these options.

### Command line options

The command line switches provide a way to control the output of the
tool. In addition to common options like verbose output or testing
modes, the tool provides options to build extra (aka 'sidecar') data
files that hold information needed for CF compliance if the original
HDF5 data files lack that information (see the *missing data* section ).
In addition, it is often desirable to build *dmr++* files before the
source data files are uploaded to a cloud store like S3. In this case,
the URL to the data may not be known when the *dmr++* is built. We
support this by using placeholder/template strings in the *dmr++* and
which can then be replaced with the URL at runtime, when the *dmr++*
file is evaluated. See the '-u' and '-p' options below.

#### Inputs

-b
The fully qualified path to the top level data directory. Data files
read by *get_dmrpp* must be in the directory tree rooted at this
location and their names expressed as a path relative to this location.
The value may not be set to `/` or `/etc` The default value is /tmp if a
value is not provided. All the data files to be processed must be in
this directory or one of its subdirectories. If *get_dmrpp* is being
executed from same directory as the data then `` -b `pwd` `` or `-b .`
works as well.

-u
This option is used to specify the location of the binary data object.
It’s value must be an http, https, or file (file://) URL. This URL will
be injected into the dmr++ when it is constructed. If option -u is not
used; then the template string `OPeNDAP_DMRpp_DATA_ACCESS_URL` will be
used and the dmr++ will substitute a value at runtime.

-c
The path to an alternate bes configuration file to use.

-s
The path to an optional addendum configuration file which will be
appended to the default BES configuration. Much like the site.conf file
works for the full server deployment it will be loaded last and the
settings there-in will have an override effect on the default
configuration.

#### Output

-o
The name of the file to create.

#### Verbose Output Modes

-h
Show help/usage page.

-v:
verbose mode, prints the intermediate DMR.

-V
Very verbose mode, prints the DMR, the command and the configuration
file used to build the DMR.

-D
Just print the DMR that will be used to build the DMR++

-X
Do not remove temporary files. May be used independently of the -v
and/or -V options.

#### Tests

-T
Run ALL hyrax tests on the resulting dmr++ file and compare the
responses the ones generated by the source hdf5 file.

-I
Run hyrax inventory tests on the resulting dmr++ file and compare the
responses the ones generated by the source hdf5 file.

-F
Run hyrax value probe tests on the resulting dmr++ file and compare the
responses the ones generated by the source hdf5 file.

#### Missing Data Creation

-M
Build a 'sidecar' file that holds missing information needed for CF
compliance (e.g., Latitude, Longitude and Time coordinate data).

-p
Provide the URL for the Missing data sidecar file. If this is not given
(but -M is), then a template value is used in the dmr++ file and a real
URL is substituted at runtime.

-r
The path to the file that contains missing variable information for sets
of input data files that share common missing variables. The file will
be created if it doesn't exist and the result may be used in subsequent
invocations of get_dmrpp (using -r) to identify the missing variable
file.

#### AWS Integration

The `get_dmrpp` application supports both S3 hosted granules as inputs,
and uploading generated dmr++ files to an S3 bucket.

S3 Hosted granules are supported by default,
When the `get_dmrpp` application sees that the name of the input file is
an S3 URL it will check to see if the AWS CLI is configured and if so
`get_dmrpp` will attempt retrieve the granule and make a dmr++ utilizing
whatever other options have been chosen.


Example: **`` get_dmrpp -b `pwd` s3://bucket_name/granule_object_id ``**

<!-- -->

-U
The **`-U`** command line parameter for `get_dmrpp` instructs the
`get_dmrpp` application to upload the generated dmr++ file to S3, but
only when the following conditions are met:

- The name of the input file is an S3 URL
- The AWS CLI has been configured with credentials that provide r+w
  permissions for the bucket referenced in the input file S3 URL.
- The `-U` option has been specified.

If all three of the above are true then `get_dmrpp` will copy the
retrieve the granule, create a dmr++ file from the granule, and copy the
resulting dmr++ file (as defined by the -o option) to the source S3
bucket using the well known NGAP sidecar file naming convention:
**`s3://bucket_name/granule_object_id.dmrpp`**


Example:
**`` get_dmrpp -U -o foo -b `pwd` s3://bucket_name/granule_object_id ``**

### *hdf5_handler* Configuration

Because *get_dmrpp* uses the *hdf5_handler* software to build the
*dmr++* the software must inject the *hdf5_handler*'s configuration.

The default configuration is large, but any valued may be altered at
runtime.

Here are some of the commonly manipulated configuration parameters with
their default values:

`H5.EnableCF=true`
`H5.EnableDMR64bitInt=true`
`H5.DefaultHandleDimension=true`
`H5.KeepVarLeadingUnderscore=false`
`H5.EnableCheckNameClashing=true`
`H5.EnableAddPathAttrs=true`
`H5.EnableDropLongString=true`
`H5.DisableStructMetaAttr=true`
`H5.EnableFillValueCheck=true`
`H5.CheckIgnoreObj=false`

#### *Note to DAACs with existing Hyrax deployments.*

If your group is already serving data with Hyrax and the data
representations that are generated by your Hyrax server are
satisfactory, then a careful inspection of the localized configuration,
typically held in /etc/bes/site.conf, will help you determine what
configuration state you may need to inject into *get_dmrpp*.

### The *H5.EnableCF* option

Of particular importance is the *H5.EnableCF* option, which instructs
the *get_dmrpp* tool to produce [Climate Forecast convention
(CF)](https://cfconventions.org/) compatible output based on metadata
found in the granule file being processed.

Changing the value of *H5.EnableCF* from ***false**'' to***true**'' will
have (at least) two significant effects.

It will:

- Cause *get_dmrpp* to attempt to make the dmr++ metadata CF compliant.
- Remove Group hierarchies (if any) in the underlying data granule by
  flattening the Group hierarchy into the variable names.

By default *get_dmrpp* the *H5.EnableCF* option is set to false:

`H5.EnableCF = false`

There is a much more comprehensive discussion of this key feature, and
others, in the ***[HDF5 Handler section of the Appendix in the Hyrax
Data Server Installation and Configuration
Guide](https://opendap.github.io/hyrax_guide/Master_Hyrax_Guide.html#_hyrax_handlers)***

### Missing data, the CF conventions and *hdf5*

Many of the *hdf5* files produced by NASA and others do not contain the
domain coordinate data (such as latitude, longitude, time, etc.) as a
collection of explicit values. Instead information contained in the
dataset metadata can used to reproduce these values.

In order for a dataset to be Climate Forecast (CF) compatible it must
contain these domain coordinate data values.

The Hyrax *hdf5_handler* software, utilized by the *get_dmrpp*
application, can create this data from the dataset metadata. The
*get_dmrpp* application places these generated data in a “sidecar” file
for deployment with the source *hdf5/netcdf*-4 file.

## Hyrax - Serving data using dmr++ files

There are three fundamental deployment scenarios for using dmr++ files
to serve data with the Hyrax data server.

This can be simple categorized as follows: The dmr++ file(s) are XML
files that contain a root `dap4:Dataset` element with a `dmrpp:href`
attribute whose value is one of:

1.  An http(s):// URL referencing to the underlying granule files via
    http.
2.  A <file://> URL that references the granule file on the local
    filesystem in a location that is inside the BES' data root tree.
3.  The template string `OPeNDAP_DMRpp_DATA_ACCESS_URL`

Each will discussed in turn below.

**Note**: By default Hyrax will automatically associate files whose name
ends with ".dmrpp" with the dmr++ handler.

### Using dmr++ with http(s) URLs

If the dmr++ files that you wish to serve contain `dmrpp:href`
attributes whose values are http(s) URLs then there are 2+1 steps to
serve the data:

1.  Place the dmr++ files on the local disk inside of the directory tree
    identified by the `BES.Catalog.catalog.RootDirectory` in the BES
    configuration
2.  Ensure that the Hyrax AllowedHosts list is configured to allow Hyrax
    to access those target URLs. This can be accomplished by adding new
    regex entires to the AllowedHosts list in /etc/bes/site.conf,
    creating that file as need be.
3.  If the data URLs require authentication to access then you'll need
    to configure Hyrax for that too.

### Using dmr++ with file URLs

Using dmr++ files with locally held files can be useful for verifying
that dmr++ functionality is working without relying on network access
that may have data rate limits, authenticated access configuration, or
security access constraints. Additionally, in many cases the dmr++
access to the locally held data may be significantly faster than through
the native netcdf-4/hdf5 data handlers.

In order to use dmr++ files that contain <file://> URLs:

1.  Place the dmr++ files on the local disk inside of the directory tree
    identified by the `BES.Catalog.catalog.RootDirectory` in the BES
    configuration
2.  Ensure the the dmr++ files contain only <file://> URLs that refer to
    data granule files inside of the directory tree identified by the
    `BES.Catalog.catalog.RootDirectory` in the BES configuration.

Note: For Hyrax, a correctly formatted file URL must start with the
protocol [`file://`](file://) followed by the full qualified path to the
data granule, for example:

`/usr/share/hyrax/ghrsst/some_granule.h5`

so the the completed URL will have three slashes after the first colon:

[`file:///usr/share/hyrax/ghrsst/some_granule.h5`](file:///usr/share/hyrax/ghrsst/some_granule.h5)

### Using dmr++ with the template string. (NASA)

Another way to serve dmr++ files with Hyrax is to build the dmr++ files
**without** valid URLs but with a template string that is replaced at
runtime. If no target URL is supplied to get_drmpp at the time that the
dmr++ is generated the template string:
<b>`OPeNDAP_DMRpp_DATA_ACCESS_URL`</b> will added to the file in place
of the URL. The at runtime it can be replaced withe the correct value.

Currently the only implementation of this is Hyrax's NGAP service which,
when deployed in the NASA NGAP cloud, will accept "restified path" URLs
that are defined as having a URL path component with two mandatory and
one optional parameters:

`MANDATORY: "/collections/UMM-C:{concept-id}"`
`OPTIONAL:  "/UMM-C:{ShortName} '.' UMM-C:{Version}"`
`MANDATORY: "/granules/UMM-G:{GranuleUR}"`

**Example:**
[`https://opendap.earthdata.nasa.gov/collections/C1443727145-LAADS/MOD08_D3.v6.1/granules/MOD08_D3.A2020308.061.2020309092644.hdf.nc`](https://opendap.earthdata.nasa.gov/collections/C1443727145-LAADS/MOD08_D3.v6.1/granules/MOD08_D3.A2020308.061.2020309092644.hdf.nc)

When encountering this type of URL Hyrax will decompose it and use the
content to formulate a query to the NASA CMR in order to retrieve the
data access URL for the granule and for the dmr++ file. It then
retrieves the dmr++ file and injects the data URL so that data access
can proceed as described above.

[More on the Restified Path can be found
here](https://wiki.earthdata.nasa.gov/display/DUTRAIN/Feature+analysis%3A+Restified+URL+for+OPENDAP+Data+Access),

## Recipe: Building and testing dmr++ files

There are two recipes shown here, one using Hyrax docker containers and
a second using the container that is part of the EOSDIS Cumulous task.
Prerequisites:

- Docker daemon running on a system that also supports a shell (the
  examples use `bash` in this section)

### Recipe: Building dmr++ files using a Hyrax docker container.

1.  Acquire representative granule files for the collection you wish to
    import. Put them on the system that is running the Docker daemon.
    For this recipe we will assume that these files have been placed in
    the directory:

    `/tmp/dmrpp`
2.  Get the most up to date Hyrax docker image:

    <b>`docker pull opendap/hyrax:snapshot`</b>
3.  Start the docker container, mounting your data directory on to the
    docker image at `/usr/share/hyrax`:

    <b>`docker run -d -h hyrax -p 8080:8080 --volume /tmp/dmrpp:/usr/share/hyrax --name=hyrax opendap/hyrax:snapshot`</b>
4.  Get a first view of your data using `get_dmrpp` with it's default
    configuration.
    1.  If you want you can build a dmr++ for an example
        "<b>`input_file`</b>" using a `docker exec` command:

        <b>`docker exec -it hyrax get_dmrpp -b /usr/share/hyrax -o /usr/share/hyrax/input_file.dmrpp -u "`[`file:///usr/share/hyrax/input_file`](file:///usr/share/hyrax/input_file)`" "input_file"`</b>
    2.  Or if you want more scripting flexibility you can login to the
        docker conainer to do the same:
        1.  Login to the docker container:

            <b>`docker exec -it hyrax /bin/bash`</b>
        2.  Change working dir to data dir:

            <b>`cd /usr/share/hyrax`</b>
        3.  This sets the data directory to the current one
            (`-b $(pwd)`) and sets the data URL (`-u`) to the fully
            qualified path to the input file.

            <b>`get_dmrpp -b $(pwd) -o foo.dmrpp -u "`[`file://"$(pwd)"/your_test_file`](file://%22$(pwd)%22/your_test_file)`" "your_test_file"`</b>


    ***Now that you have made a dmr++ file, use the running Hyrax server
    to view and test it by pointing your browser at:***


    **[`http://localhost:8080/opendap/`](http://localhost:8080/opendap/)**
5.  You can also batch process all of your test granules, if you want to
    go that route. This script assumes your ingestable data files end
    with '`.h5`'.

    *The resulting dmr++ files should contain the correct
    [`file://`](file://) URLs and be correctly located so that they may
    be tested with the Hyrax service running in the docker instance.*

<!-- -->

    #!/bin/bash
    # This script will write each output file as a sidecar file into
    # the same directory as its associated input granule data file.

    # The target directory to search for data files
    target_dir=/usr/share/hyrax
    echo "target_dir: ${target_dir}";

    # Search the target_dir for names matching the regex \*.h5
    for infile in `find "${target_dir}" -name \*.h5`
    do
        echo " Processing: ${infile}"

        infile_base=`basename "${infile}"`
        echo "infile_base: ${infile_base}"

        bes_dir=`dirname "${infile}"`
        echo "    bes_dir: ${bes_dir}"

        outfile="${infile}.dmrpp"
        echo "     Output: ${outfile}"

        get_dmrpp -b "${bes_dir}" -o "${outfile}" -u "file://${infile}" "${infile_base}"
    done

***Remember that you can use the Hyrax server that is running in the
docker container to view and test the dmr++ files you just created by
pointing your browser at:***



**[`http://localhost:8080/opendap/`](http://localhost:8080/opendap/)**

### Testing and qualifying dmr++ files

In the previous section/step we created some initial dmr++ files using
the default configuration. It is crucial to make sure that they provide
the representation of the data that you and your users are expecting,
and that they will work correctly with the Hyrax server. (See the
following sections for details). If the generated dmr++ files do not
match expectations then the default configuration of the `get_dmrpp` may
need to be amended using the `-s` parameter. If the data are currently
being served by your DAAC's on-prem team this is where understanding
exactly what the localizations made to the configurations of the on-prem
Hyrax instances deployed for the collection is important. These
localization will probably need to be injected into `get_drmpp` in order
to produce the correct data representation in the dmr++ files.

### Flattening Groups

By default `get_dmrpp` will preserve and show group hierarchies. If this
is not desired, say for CF-1.0 compatibility, then you can change this
by creating a small amendment to `get_dmrpp`'s default configuration.
First create the amending configuration file:

`echo "H5.EnableCF=true" > site.conf`

Then, change the invocation of `get_dmrpp` in the above example by
adding the `-s` switch:

`` get_dmrpp -s site.conf -b `pwd` -o "${dmrpp_file}" -u " ``[`file://`](file://)`` "`pwd`"/${file}" "${file}" ``

And re-run the dmr++ production as shown above.

### DAP representations

We have test and assurance procedures for DAP4 and DAP2 protocols below.
Both are important. For legacy datasets the DAP2 request API is widely
used by an existing client base and should continue to be supported.
Since DAP4 subsumes DAP2 (but with somewhat different API semantics) It
should be checked for legacy datasets as well. For more modern datasets
that content DAP4 types such as Int64 that are not part of the DAP2
specification or implementations we will need to relying eliding the
instances of unmapped types, or return an error when this is
encountered.

`# Test Constants:`
`GRANULE_FILE="some_name.h5"`
`# Granule URL`
`gf_url="`[`http://localhost:8080/opendap/${GRANULE_FILE}`](http://localhost:8080/opendap/$%7BGRANULE_FILE%7D)`"`

#### Inspect the dmr++ files

1.  Do the dmr++ files have the expected dmrpp:href URL(s)?

    `head -2 ${GRANULE_FILE}.dmrpp`

#### Check DAP4 DMR Response

Inspect `${gf_url}.dmrpp.dmr`

1.  Get the document, save as `foo.dmr`:

    `curl -L -o foo.dmr "${gf_url}.dmr"`
2.  Is each variable's data type correct and as expected?
3.  Are the associated dimensions correct?

#### DAP4 Check binary data response

For a particular granule GRANULE_FILE and a particular variable
VARIABLE_NAME (Where [VARIABLE_NAME is a full qualified DAP4
name.](https://docs.opendap.org/index.php?title=DAP4:_Specification_Volume_1#Fully_Qualified_Names)):


`curl -L -o dap4_subset_file "${gf_url}.dap?dap4.ce=VARIABLE_NAME"`

`curl -L -o dap4_subset_dmrpp "${gf_url}.dmrpp.dap?dap4.ce=VARIABLE_NAME"`

`cmp dap4_subset_file dap4_subset_dmrpp`

#### DAP4 UI test

- View and exercise the DAP4 Data Request Form `{gf_url}.dmr.html`

#### DAP2 Check DDS Response

1.  Inspect `${gf_url}.dds`
    1.  Is each variable's data type correct and as expected?
    2.  Are the associated dimensions correct?
2.  Compare DMR++ DDS with granule file DDS.

    For a particular granule GRANULE_FILE and a particular variable
    VARIABLE_NAME (Where [VARIABLE_NAME is a DAP2
    name.](https://cdn.earthdata.nasa.gov/conduit/upload/512/ESE-RFC-004v1.1.pdf)):


    `curl -L -o dap2_dds_file "${gf_url}.dds"`

    `curl -L -o dap2_dds_dmrpp "${gf_url}.dds"`

    `cmp dap2_dds_file dap2_dds_dmrpp`

#### DAP2 Check binary data response

For a particular granule GRANULE_FILE and a particular variable
VARIABLE_NAME (Where [VARIABLE_NAME is a DAP2
name.](https://cdn.earthdata.nasa.gov/conduit/upload/512/ESE-RFC-004v1.1.pdf)):


`curl -L -o dap2_subset_file "${gf_url}.dods?VARIABLE_NAME"`

`curl -L -o dap2_subset_dmrpp "${gf_url}.dmrpp.dods?VARIABLE_NAME"`

`cmp dap2_subset_file dap2_subset_dmrpp`

**Note**: One might consider doing this with two or more variables.

#### DAP2 UI Test

- View and exercise the DAP2 Data Request Form located here:
  `{gf_url}.html`
- Try it in Panoply!
  - Open Panoply.
  - From the **File** menu select **Open Remote Dataset...**
  - Paste the `{gf_url}.html` into the resulting dialog box.

---