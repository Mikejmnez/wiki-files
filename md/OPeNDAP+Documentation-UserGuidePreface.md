# Preface

This document describes version 3.4 of the
\[<http://www.opendap.org/><cite>Distributed Oceanographic Data
System</cite>\] (OPeNDAP), a data system intended to allow researchers
transparent access to oceanographic data---stored in any of several
different file formats---across the Internet. Using OPeNDAP function
libraries, many existing data analysis programs can be easily modified
to accommodate access of distant datasets in a manner identical to the
access of local datasets. OPeNDAP includes a protocol for the
transmission of data across the Internet, and supports selection of data
using constraint expressions, and translation of data from one format to
another.

An overview of the system's use is presented, and specific tasks
illustrated, for data providers as well as for users.

## Tasks Illustrated in this Guide

For a quick start to getting, installing, and using OPeNDAP software,
see the list below of tasks described in this document.

- Getting the OPeNDAP software.
  (\[<http://www.opendap.org/><cite>install</cite>\])

<!-- -->

- Installing the OPeNDAP software.
  (\[<http://www.opendap.org/><cite>install</cite>\])

<!-- -->

- Using an OPeNDAP client.
  (\[<http://www.opendap.org/><cite>opd-client,using-example</cite>\])

<!-- -->

- Re-linking a data analysis or display application to become a OPeNDAP
  client.
  (\[<http://www.opendap.org/><cite>opd-client,link-example</cite>\])

<!-- -->

- Using the OPeNDAP data locator to find data.

<!-- -->

- Creating and installing an OPeNDAP server.

<!-- -->

- - Installing the OPeNDAP server and CGI filters.

(\[<http://www.opendap.org/><cite>opd-server,cgi-install</cite>\])

- - Starting and configuring the httpd server. Due to the variety

of available servers, this task is beyond the scope of the manual.
Please refer to the documentation for the particular server in question
for more information.

- - Implementing a new DODS-compliant API.
    (\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
    OPeNDAP Programmer's Guide</cite>\])

<!-- -->

- Writing an OPeNDAP CGI program.
  (\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
  OPeNDAP Programmer's Guide</cite>\] )

<!-- -->

- Writing the CGI service programs.
  (\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
  OPeNDAP Programmer's Guide</cite>\] )

<!-- -->

- A list of all supported APIs.
  (\[<http://www.opendap.org/><cite>opd-client,supported-APIs</cite>\])

## Who is this Guide for?

The user documentation for OPeNDAP covers two groups of users: those who
want to provide access to data vian OPeNDAP and those who want to use
data. In many cases the people will be one and the same since most
providers will also be data users.

This documentation assumes that the readers are familiar with computers,
but are not necessarily programmers.

This guide also contains technical information that will be of
assistance to programmers who plan to write new DODS-compliant APIs for
as yet unsupported data models. Data providers and data consumers may
find some general questions answered by this material, but it is not
necessary to know any of it in order to use the system.

## Organization of this Document

This book is organized into separate sections for data providers, data
consumers, and technical reference material for programmers.

> Part I is for everybody who wants to use OPeNDAP.
>
> > [Chapter 1](UserGuideChapter1 "wikilink") provides a high-level
> > overview of the entire system.
>
> > [Chapter 2](UserGuideChapter2 "wikilink") describes the use of
> > OPeNDAP with an OPeNDAP client program.
>
> Part II is for data consumers, that is, the people who want to look at
> data using the OPeNDAP system.
>
> > [Chapter 3](UserGuideChapter3 "wikilink") shows how to look at data
> > using OPeNDAP. It includes a section about the theoretical and
> > practical problems of data model translation. It also explains how
> > to build an OPeNDAP client, which is the program used to look at
> > OPeNDAP data.
>
> > [Chapter 4](UserGuideChapter4 "wikilink") explains how to subsample
> > the data with constraint expressions.
>
> Part III is for data providers, or people who want to make their data
> available through OPeNDAP servers.
>
> > [Chapter 5](UserGuideChapter5 "wikilink") shows how to use OPeNDAP
> > to make your data available to others. It explains how to set up a
> > OPeNDAP server to provide OPeNDAP data to OPeNDAP clients, and also
> > contains information about modifying or writing an OPeNDAP server.
>
> Part IV contains technical information about how OPeNDAP works. This
> information is provided to people who want to write new libraries to
> use OPeNDAP through a currently unsupported API.
>
> > [Chapter 6](UserGuideChapter6 "wikilink") contains general
> > information about Data and Data Models. This is important
> > information to have for people intending to use OPeNDAP to provide
> > data to others. It covers the OPeNDAP data attribute and data
> > descriptor structures. The chapter also contains a section outlining
> > the problems associated with Data Model translation.
>
> Appendices
>
> > [Appendix A](UserGuideAppendix "wikilink") contains the instructions
> > for installing the OPeNDAP libraries, and software that requires
> > these libraries.
> >
> > Glossary A small but useful collection of terms.