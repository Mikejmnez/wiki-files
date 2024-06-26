## Security Working Group

### Motivation

We want to develop a policy that helps both OPeNDAP and the people who
run our software to be confident that using the software does not
substantially increase the level of risk of a computer/network security
problem. We know that risk is inherent in using computer networks, but
it can be managed and reduced by avoiding certain behaviors. The policy
we develop here should address those behaviors. As we do this, we can
hopefully increase awareness about computer security in the OPeNDAP
community to the point where more services become available for users.

### Statement of Work

1.  Evaluate existing Computer and Network security policies
2.  Distill from those elements which apply to OPeNDAP and its community
    of users
3.  Determine if we need to address both Servers and Clients in separate
    policies or not, or if we only need to address Server security
4.  Make recommendations to OPeNDAP regarding its Interim policy
5.  Develop a Community policy if that's appropriate
6.  Move on from policy to procedures, it that seems appropriate

### Working Documents

1.  [Strawman Security Policies](Strawman_Security_Policies "wikilink")
2.  [OPeNDAP Security Policy and
    Procedures](http://scm.opendap.org/trac/wiki/NetworkServerSecurity)

### Members

1.  [James Gallagher](mailto:jgallagher@opendap.org)
2.  [Jerry Pan](mailto:pany@ornl.gov)
3.  [Chris Lynnes](mailto:Christopher.S.Lynnes@nasa.gov)
4.  [John Caron](mailto:caron@unidata.ucar.edu)
5.  [Rob De Almeida](mailto:rob@pydap.org)

### Resources

Most of these resources are aimed at sites running computers. We do
that, but information targeted at developers of the software others run
seems harder to find. See below for more comments.

- [OWASP](http://www.owasp.org/index.php/Main_Page) The Open Web
  Application Security Project has produced a
  [Guide](http://www.owasp.org/index.php/OWASP_Guide_Project)
  [(pdf)](http://prdownloads.sourceforge.net/owasp/OWASPGuide2.0.1.pdf?download)
  which is widely used as an introduction to security issues.
- [cert.org](http://www.cert.org/) This is a good site for information
  about code standards for C and C++ (there's nothing here for Java I
  could find).
- [The Center for Internet Security](http://www.cisecurity.org/)
- [ISO 17799](http://17799.denialinfo.com/whatisiso17799.htm) ISO 17799
  (and its sister standard, whose number escapes me) are the 'big
  daddies' of security policies. They are certainly overkill for us, but
  [chapter 8](http://17799.denialinfo.com/chapter8.htm) and [chapter
  13](http://17799.denialinfo.com/chapter13.htm) address software
  development and might have some interesting ideas. It would be a good
  idea to not contradict this standard, at the least.
- [SANS](http://www.sans.org/resources/policies/) SANS seems like one of
  the best resources out there. I found this on my own and Jeff Ogata
  also recommended it. This is their section on policies, but they offer
  much more.
- [information-security-policies-and-standards.com](http://www.information-security-policies-and-standards.com/)
- Here's a paper on [Free and/or Open Source
  Software](http://www.dwheeler.com/oss_fs_why.html) that has some
  interesting references.

[Security](Category:Working_Groups "wikilink")