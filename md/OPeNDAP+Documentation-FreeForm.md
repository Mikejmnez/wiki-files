Using FreeForm ND with OPeNDAP, a researcher can easily make his or her
data available to the wider community of OPeNDAP users without having to
convert that data into another data file format. This document presents
the FreeForm ND software, and shows how to use it with the OPeNDAP
server.

## Tasks Illustrated in this Guide

For a quick start to getting, installing, and using the FreeForm ND
software, see the list below of tasks described in this document.

- Quick start.
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/dquick><cite>here</cite>\])
- Getting and installing the FreeForm ND software.
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/dintro#Installing_the_OPeNDAP_FreeForm_ND_Data_Handler><cite>here</cite>\])
- Serving tabular data.
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/tblfmt><cite>here</cite>\])
- Array tabular data.
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/arrayfmt><cite>here</cite>\])
- Dealing with data file headers
  (\[<http://docs.opendap.org/index.php/Wiki_Testing/hdrfmts><cite>here</cite>\])

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
strongly encourage you to read the
\[<http://docs.opendap.org/index.php/UserGuide><cite>The OPeNDAP User
Guide</cite>\] before reading further here.

## Organization of this Document

This book contains both introductory and reference material. There is
also a description of the installation procedure.

> [Introduction](Wiki_Testing/dintro "wikilink") : contains an overview of the OPeNDAP FreeForm ND Data Handler software, including how to get it and install it.
>
> <!-- -->
>
> [Quick Start](Wiki_Testing/dquick "wikilink") : provides a brief introduction to writing format descriptions and using the OPeNDAP FreeForm ND Data Handler.
>
> <!-- -->
>
> [Table Format](Wiki_Testing/tblfmt "wikilink") :provides detailed information about writing format descriptions to facilitate access to data in tabular formats.
>
> <!-- -->
>
> [Array Format](Wiki_Testing/arrayfmt "wikilink") : provides detailed information about writing format descriptions to facilitate access to data in non-tabular (array) formats.
>
> <!-- -->
>
> [Header Formats](Wiki_Testing/hdrfmts "wikilink") : tells you how to work with header formats.
>
> <!-- -->
>
> [FreeForm Server](Wiki_Testing/ff-server "wikilink") : describes the operation of the OPeNDAP FreeForm ND Data Handler, with tips for writing format files.
>
> <!-- -->
>
> [FreeForm Conventions](Wiki_Testing/convs "wikilink") : presents FreeForm ND file name conventions, the search rules for locating format files, and standard command line arguments for FreeForm ND programs.
>
> <!-- -->
>
> [Format Conversion](Wiki_Testing/fmtconv "wikilink") : shows you how to use the FreeForm ND program <font color='green'>newform</font> to convert data from one format to another and also how to read the data in a binary file.
>
> <!-- -->
>
> [Data Check](Wiki_Testing/datachk "wikilink") : discusses the FreeForm ND program <font color='green'>checkvar</font>, which you can use to check data distribution and quality.

A position box is often used in this book to indicate column position of
field values in data files. It is shown at the beginning of a data list
in the documentation, but does not appear in the data file itself. It
looks something like this:

    1         2         3         4         5         6
    012345678901234567890123456789012345678901234567890