# Preface

This document describes how to use the OPeNDAP toolkit software to build
OPeNDAP data servers, clients and client-libraries. Using the objects
and functions contained in the toolkit, you can create programs which
serve data over the internet as well as programs that can request data
from any OPeNDAP server.

This document covers release 3.4 and later of the DODS software.

## Who is this Guide for?

This guide is for people who wish to use the OPeNDAP software to write a
new OPeNDAP data server, a new client, or a new client library.
Typically, this will only be those people who wish to serve data in a
format that is not currently supported by the DODS team, or who have an
existing application that uses an idiosyncratic or unusual API for data
access. Most people will be able to use one of the already written
servers or client libraries.

This documentation assumes that the readers are C++ programmers, are
familiar with networked applications, and the POSIX programming
environment. The DODS/OPeNDAP project also provides a native Java class
library (API) that parallels the C++ spftware described
here.[(1)](ProgrammerGuideFootnotes "wikilink")

Because the type of information presented in a document like this
depends to a large extent on the needs of its readers we welcome your
feedback and comments. In particular, if you have any questions about
individual sections, email those questions and we'll send back an answer
as well as including that information in the next version of this
document. Send queries to: <support@opendap.org>.

## Organization of this Document

This Guide is divided into five chapters.

> [Chapter 1](ProgrammerGuideChapter1 "wikilink") :
>
> provides background information on the organization of the toolkit
> software.
>
> [Chapter 2](ProgrammerGuideChapter2 "wikilink") :
>
> `describes how to use the Network I/O classes to manage virtual connections.`
>
> [Chapter 3](ProgrammerGuideChapter3 "wikilink") :
>
> `discusses how to sub-class the toolkit \Cpp classes so that they are specialized for your specific use.`
>
> [Chapter 4](ProgrammerGuideChapter4 "wikilink") :
>
> `describes in detail how to write certain sections of both the data server and the client-library for a new API.`
>
> [Chapter 5](ProgrammerGuideChapter5 "wikilink") :
>
> `linking your program`
>
> [Chapter 6](ProgrammerGuideChapter6 "wikilink") :
>
> `a overview of the OPeNDAP server Architecture`
>
> [Chapter 7](ProgrammerGuideChapter7 "wikilink") :
>
> `describes how to link user programs with the new client-library implementation of an API.`
>
> [Footnotes](ProgrammerGuideFootnotes "wikilink") :
>
> `Footnotes`

Note that this is "not" a reference volume. See
\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
OPeNDAP Programmer's Guide</cite>\]ref for a concise listing of the
member functions in each of the toolkit's classes.