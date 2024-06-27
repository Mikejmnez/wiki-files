## Overview

This document is intended to help those that have been asked to deploy
Hyrax into an environment where authentication services are provided by
an instance of [Shibboleth](http://shibboleth.net).

## Terms

[Identity Provider (IdP)](http://en.wikipedia.org/wiki/Identity_provider)
Also known as an **Identity Assertion Provider** an **Identity Provider
(IdP)** is a service that provides authentication and identity
information services. An **IdP** is a kind of provider that creates,
maintains, and manages identity information for principals and provides
principal authentication to other service providers within a federation,
such as with web browser profiles.

<!-- -->

Service Provider (SP)
A **Service Provider (SP)** is a Web Service that utilizes an IdP
service to determine the identity of it's users. Or more broadly, a role
donned by a system entity where the system entity provides services to
principals or other system entities.

With respect to this document Hyrax, Tomcat, and Apache all become part
of an **SP** through the installation and configuration of the Apache
**httpd** Shibboleth module (mod_shib).

See [Service Providers, Identity Providers & Security Token Services
explained](http://www.thedotnetfactory.com/learningcenter/technologies/service-identity-providers)
for more.

## Install and Configure Shibboleth

The Shibboleth wiki provides excellent documentation on how to get
Shibboleth authentication services working with Tomcat. This is
primarily an Apache *httpd* activity.

Basically you need to [follow the instructions for a Native Java
Install](https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPJavaInstall)
and remember - Hyrax does not use either Spring or Grails.

### Installation

The logical starting point for this is with the [Native Java SP
Installation](https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPJavaInstall):

- <https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPJavaInstall>

But as far as the organization of the work is concerned it is really the
last page you need to process, as it will send you off to do a platform
dependent Shibboleth Native Service Provider for Apache installation
which needs to be completed, working, and configured before you'll
return to the [Native Java SP
Installation](https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPJavaInstall)
to enable the part where Tomcat and *mod_shib* pass authenticated user
information into Tomcat.

The document path on the [Natvie Java Install wiki
page](https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPJavaInstall)
will send you off to do Shibboleth Native Service Provider installation
which is platform dependent:

- <https://wiki.shibboleth.net/confluence/display/SHIB2/Installation>
  - Install a *Native Service Provider* on your target system.
  - In the initial testing section for Linux they suggest accessing the
    Status page <https://localhost/Shibboleth.sso/Status>, but you may
    have to use the loopback address to be able to do so:
    <https://127.0.0.1/Shibboleth.sso/Status>

Return to the [Native Java SP
Installation](https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPJavaInstall)
and complete the instructions there.

### Configuration

Once the SP installation is completed go to the Native SP Configuration
page:

- <https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPConfiguration>

Read that page and then follow the link to the instructions for Apache:

- <https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPApacheConfig>

Follow those instructions.

- Do not be confused by the section [Making URLs Used by mod_shib Get
  Properly
  Routed](https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPApacheConfig#NativeSPApacheConfig-MakingURLsUsedbymod_shibGetProperlyRouted).
  While you must add this *Location* directive to "reveal" the
  shibboleth module to the world don't think the URL
  <https://yourhost/Shibboleth.sso> is a valid access point to the
  module. That URL may always return a Shibboleth error page even if
  *mod_shib* and *shibd* are configured and working correctly.
- Read and understand the section [Enabling the Module for
  Authentication](https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPApacheConfig#NativeSPApacheConfig-EnablingtheModuleforAuthentication)

The Shibboleth instructions should have had you add something like this:

``` apache
<Location /opendap>
  AuthType shibboleth
  ShibRequestSetting requireSession 1
  require valid-user
</Location>
```

to *httpd.conf*. This will require users to authenticate to access any
part of Hyrax which may be exactly what you want. If you want more fine
grained control you may want use multiple `Location` elements with
different `require` attributes. For example:

``` apache
<Location /opendap>
  AuthType shibboleth
  ShibCompatWith24 On
  require shibboleth
</Location>
<Location /opendap/AVHRR>
  AuthType shibboleth
  ShibCompatWith24 On
  ShibRequestSetting requireSession 1
  require valid-user
</Location>
</apache>
```

In this example the first `Location` establishes Shibboleth as the
authentication tool for the entire */opendap* application path, and
enables the Shibboleth module over the entire Hyrax Server.

- Since there is no `ShibRequestSetting requireSession 1` line it does
  not require a user to be logged in order to access the path.
- The `require shibboleth` command activates mod_shib for all of Hyrax.

The second `Location` states that only valid-users may have access
"/opendap/AVHRR" URL path.

- The `require valid-user` command requires user authentication.
- The `AuthType` command is set to `shibboleth` so *mod_shib* will be
  called upon to perform the authentication.

For more examples and better understanding see the [Apache Configuration
section of the Shibboleth
wiki.](https://wiki.shibboleth.net/confluence/display/SHIB2/NativeSPApacheConfig#NativeSPApacheConfig-AuthConfigOptions)