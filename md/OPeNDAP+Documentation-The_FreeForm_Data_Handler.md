# Preface

This document describes the OPeNDAP FreeForm ND Data Handler, which can
be used with the OPeNDAP data server. It is not a complete description
of the FreeForm ND software. For that, please refer to the ND manual.

This document contains much material originally written at the National
Oceanic and Atmospheric Administration's National Environmental
Satellite, Data, and Information Service, which is part of the National
Geophysical Data Center in Boulder, Colorado.

We are interested in your comments about the OPeNDAP software, and the
FreeForm ND software and this document. Send them to:
\[[mailto:support@opendap.org](mailto:support@opendap.org)<cite>support@opendap.org</cite>\].

Using FreeForm ND with OPeNDAP, a researcher can easily make his or her
data available to the wider community of OPeNDAP users without having to
convert that data into another data file format. This document presents
the FreeForm ND software, and shows how to use it with the OPeNDAP
server.

## Tasks Illustrated in this Guide

For a quick start to using the FreeForm ND software, see the list below
of tasks described in this document.

- Quick start.
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/dquick><cite>here</cite>\])
- Serving tabular data.
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/tblfmt><cite>here</cite>\])
- Array tabular data.
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/arrayfmt><cite>here</cite>\])
- Dealing with data file headers
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/hdrfmts><cite>here</cite>\])
- Setup of File servers
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/fileserv><cite>here</cite>\])

### Serving data with timestamps in the file names

This handler can read data stored in files that incorporate data strings
in their names. This feature was added to support serving data produced
and hosted by Remote Sensing Systems (RSS) and while the run-time
parameters bear the name of that organization, they can be used for any
data that fit the naming conventions they have developed. The naming
convention is as follows:

The convention: <data source> + '_' + <date_string> + <version> + \[_d3d\]
Daily data: When <date_string> includes YYYYMMDDVV, the file contains *daily* data.
Averaged data: When <date_string> only includes YYYYMMVV (no *DD*), or includes (*DD*) and optional *_d3d* then the file contains *averaged* data.

For *daily* data the format file should be named
*<data source>_daily.fmt* while averaged data should be named
*<data source>_averaged.fmt*.

To use this feature, set the run-time parameter *FF.RSSFormatSupport* to
*yes* or *true*. If you store the format files (and optional ancillary
DAS files) in a directory other than the data, use the parameter
*FF.RSSFormatFiles* to name that other directory. Like all handler
run-time configuration parameters, these can go in either the *bes.conf*
or *ff.conf* file. Here's an example sniplet from ff.conf showing how
these are used:

``` bash
#
# Data Handler Specific key/value parameters
#
FF.RSSFormatSupport = yes
FF.RSSFormatFiles = /usr/local/RSS
```

## Who is this Guide for?

This guide is for people who wish to use FreeForm ND to serve scientific
datasets using the OPeNDAP software. Scientists who wish to share their
data with colleagues may also find this a useful system, since it is a
relatively simple matter to set up a server that can allow remote access
to your data.

This documentation assumes that the readers are familiar with computers
and the internet, but are not necessarily programmers. More than a
passing familiarity with different data file formats will be useful, as
will an understanding of elementary internet concepts, such as URLs and
http.

This manual also assumes some familiarity with the OPeNDAP software. If
you are starting from scratch, knowing nothing at all about OPeNDAP, we
strongly encourage you to browse the
\[<http://docs.opendap.org/index.php/Wiki_Testing/OpeNDAP_User%27s_Guide><cite>The
OPeNDAP User Guide</cite>\] before reading too far here.

## Organization of this Document

This book contains both introductory and reference material. There is
also a description of the installation procedure.

> [Chapter 1](Wiki_Testing/dintro "wikilink") : contains an overview of the OPeNDAP FreeForm ND Data Handler software, including how to get it and install it.
>
> <!-- -->
>
> [Chapter 2](Wiki_Testing/dquick "wikilink") : provides a brief introduction to writing format descriptions and using the OPeNDAP FreeForm ND Data Handler.
>
> <!-- -->
>
> [Chapter 3](Wiki_Testing/tblfmt "wikilink") :provides detailed information about writing format descriptions to facilitate access to data in tabular formats.
>
> <!-- -->
>
> [Chapter 4](Wiki_Testing/arrayfmt "wikilink") : provides detailed information about writing format descriptions to facilitate access to data in non-tabular (array) formats.
>
> <!-- -->
>
> [Chapter 5](Wiki_Testing/hdrfmts "wikilink") : tells you how to work with header formats.
>
> <!-- -->
>
> [Chapter 6](Wiki_Testing/ff-server "wikilink") : describes the operation of the OPeNDAP FreeForm ND Data Handler, with tips for writing format files.
>
> <!-- -->
>
> [Chapter 7](Wiki_Testing/fileserv "wikilink") : describes the OPeNDAP file server.
>
> <!-- -->
>
> [Chapter 8](Wiki_Testing/convs "wikilink") : presents FreeForm ND file name conventions, the search rules for locating format files, and standard command line arguments for FreeForm ND programs.
>
> <!-- -->
>
> [Chapter 9](Wiki_Testing/fmtconv "wikilink") : shows you how to use the FreeForm ND program <font color='green'>newform</font> to convert data from one format to another and also how to read the data in a binary file.
>
> <!-- -->
>
> [Chapter 10](Wiki_Testing/datachk "wikilink") : discusses the FreeForm ND program <font color='green'>checkvar</font>, which you can use to check data distribution and quality.
>
> <!-- -->
>
> [Appnedix A](Wiki_Testing/hdfutils "wikilink") : provides explanations for a small selection of tools that will be useful for programmers working with the HDF file format.
>
> <!-- -->
>
> [Appendix B](Wiki_Testing/errors "wikilink") : presents a list of common FreeForm ND error messages. These are the error messages that may be issued by the FreeForm ND utilities, such as <font color='green'>newform</font>, not the OPeNDAP FreeForm ND Data Handler.

A position box is often used in this book to indicate column position of
field values in data files. It is shown at the beginning of a data list
in the documentation, but does not appear in the data file itself. It
looks something like this:

    1         2         3         4         5         6
    012345678901234567890123456789012345678901234567890

[Freeform](Category:BES_Modules "wikilink")