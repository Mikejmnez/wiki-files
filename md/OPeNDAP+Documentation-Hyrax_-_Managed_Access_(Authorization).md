## Overview

The authentication and authorization mechanisms in both the Apache Web
Server (*httpd*) and the Tomcat Servlet container are well documented
and widely used.

We hope to address the following perceived shortcomings of the apache
product's authorization components:

1.  Difficult to specify fine grain access control policies for complex
    heterogeneous service stacks.
2.  The option to centralize the PDP so that multiple services can be
    controlled from a single policy profile.

A simple policy decision point (SimplPDP) was coded that can accommodate
many different types of policies. A simple regex policy was implemented
that uses regex matching to white list users, resources, http verbs, and
queries.

The remainder of this document describes how to get, build, install,
configure, and run the Hyrax Managed Access prototype.

## Terms

[Authentication](http://en.wikipedia.org/wiki/Authentication)
This is the process of confirming the identity of the user. The end
result is a User ID (*uid* or *UID*) which may be accessed by the
software components via (both?) the Apache API (mod_\*) and the Java
ServletAPI (Tomcat servlets) used to trigger authorization policy chains
or may be logged along with relevant request information.

<!-- -->

[Authorization](http://en.wikipedia.org/wiki/Authorization)
Authorization is the function of specifying access rights to resources.
A user may be *authorized* (some say *permitted*) to access a resource.

<!-- -->

[Identity Provider (IdP)](http://en.wikipedia.org/wiki/Identity_provider)
Also known as an **Identity Assertion Provider** an **Identity Provider
(IdP)** is a service that provides authentication and identity
information services. An **IdP** is a kind of provider that creates,
maintains, and manages identity information for principals and provides
principal authentication to other service providers within a federation,
such as with web browser profiles.

<!-- -->

Policy Administration Point (PAP)
Point which manages access authorization policies

<!-- -->

Policy Decision Point (PDP)
Point which evaluates access requests against authorization policies
before issuing access decisions

<!-- -->

Policy Enforcement Point (PEP)
Point which intercepts user's access request to a resource, makes a
decision request to the PDP to obtain the access decision (i.e. access
to the resource is approved or rejected), and acts on the received
decision

<!-- -->

Policy Information Point (PIP)
The system entity that acts as a source of attribute values (i.e. a
resource, subject, environment)

<!-- -->

Policy Retrieval Point (PRP)
Point where the XACML access authorization policies are stored,
typically a database or the filesystem.

<!-- -->

Service Provider (SP)
A **Service Provider (SP)** is a Web Service that utilizes an IdP
service to determine the identity of it's users. Or more broadly, a role
donned by a system entity where the system entity provides services to
principals or other system entities.

See [Service Providers, Identity Providers & Security Token Services
explained](http://www.thedotnetfactory.com/learningcenter/technologies/service-identity-providers)
for more.

## Requirements

- JDK-1.6.x or higher for your target system.
- ANT 1.7.0 or newer
- Hyrax 1.9.7 or greater

## Git

- Download the managed-access branch of the OLFS from GitHub.


The branch is here:
<https://github.com/OPENDAP/olfs/tree/managed-access>

And the download URL is:
<https://github.com/OPENDAP/olfs/archive/managed-access.zip>

- Unzip the file, open a terminal (if you haven't yet), and cd into the
  managed-access directory that was created when you unzipped the file.

## Build

- In your terminal enter the command



`ant -f server`

If successful, this will create the file *build/dist/opendap.war*

## Install

- Copy the new WebArchive (.war) file into the Tomcat web apps directory



`cp build/dist/opendap.war $CATALINA_HOME/webapps`

- Restart Tomcat

## Web Service Configuration

- Copy the configuration file from the web application into the Hyrax
  persistent content directory:



`cp $CATALINA_HOME/webapps/opendap/initialContent/PEPFilter.xml $CATALINA_HOME/content/opendap/`

- Edit the Hyrax web.xml file and make sure the PEPFilter config
  parameter's value is set to the fully qualified path to the file you
  copied in the previous step.

Example:

``` xml
    <filter>
        <filter-name>PEP</filter-name>
        <filter-class>opendap.auth.PEPFilter</filter-class>
        <init-param>
            <param-name>config</param-name>
            <param-value>/usr/local/hyrax/tomcat/content/opendap/PEPFilter.xml</param-value>
        </init-param>
    </filter>
```

This excerpt from the *web.xml* file shows the *<filter>* element. The
PEPFilter will look for it's configuration in the file:
*/usr/local/hyrax/tomcat/content/opendap/PEPFilter.xml*

## PEP/PDP Configuration

Edit the PEPFilter.xml file that you referenced in the web.xml file
above. It should look something like this:

``` xml
<PolicyEnforcementPointFilter>


    <!-- You can use a RemotePDP -->
    <!-- PolicyDecisionPoint class="opendap.auth.RemotePDP">
        <PDPServiceEndpoint>http://localhost:8080/pdp</PDPServiceEndpoint>
    </PolicyDecisionPoint -->


    <!-- You can use an in memory local PDP -->
    <PolicyDecisionPoint class="opendap.auth.SimplePDP">
        <Policy class="opendap.auth.RegexPolicy">
            <role>.*</role>
            <resource>.*(/|\.(css|png|jpg|ico|gif|xsl|jsp)|/contents.html|/catalog.html|/catalog.xml)$</resource>
            <queryString>.*$</queryString>
            <allowedAction>GET</allowedAction>
        </Policy>
        <Policy class="opendap.auth.RegexPolicy">
            <role>guest</role>
            <resource>.*\.(dds|html|das|ddx)$</resource>
            <queryString>.*$</queryString>
            <allowedAction>GET</allowedAction>
        </Policy>
        <Policy class="opendap.auth.RegexPolicy">
            <role>manager</role>
            <resource>.*$</resource>
            <queryString>.*$</queryString>
            <allowedAction>GET</allowedAction>
            <allowedAction>POST</allowedAction>
        </Policy>
        <Memberships>
           <group id="guest" >
               <user id="GUEST" />
           </group>

           <group id="users" >
               <user id="ndp_opendap" />
               <user id="jhrg" />
           </group>

           <group id="cmip" >
               <user id="ndp_opendap" />
           </group>

           <group id="managers" >
               <user id="root" />
               <user id="ndp_opendap" />
               <user id="jhrg" />
           </group>

           <role id="manager">
               <group id="managers" />
               <group id="cmip" />
           </role>

           <role id="guest">
               <group id="guest" />
           </role>

        </Memberships>
    </PolicyDecisionPoint>
</PolicyEnforcementPointFilter>
```

The PEP must be associated with a PDP. In this example the PEP is
configured to use an in memory instance of the SimplePDP class.

### Policy Decision Point

The *PolicyDecisionPoint* element identifies the PDP that will be used
by the PEP filter, and provides the configuration for the PDP class
referenced.

This *PolicyDecisionPoint* element identifies the
*opendap.auth.SimplePDP* class as the PDP to use.

``` xml
    <PolicyDecisionPoint class="opendap.auth.SimplePDP">
        .
        .
        .
    </PolicyDecisionPoint>
```

The contents of the *PolicyDecisionPoint\>* element are the specific
configuration components for the named class, in this case
*opendap.auth.SimplePDP*.

### A Simple Policy Decision Point

The class opendap.auth.SimplePDP is an Policy Decision Point
implementation crafted to hold a heterogeneous collection of policy
objects. For any particular request the SimplePDP will work through it's
ordered list of policies asking each on in turn to evaluate the request.
If a policy allows the request, then the request is allowed. If no
policy allows the request, then the request is denied.

#### Policy

The Policy elements in the configuration each define an access control
policy. Different Policy implementations will have different content in
the body of the Policy element.

Currently we have implemented a RegexPolicy. A RegexPolicy is defined by
3 regular expressions: role, resource, & queryString, and list of
allowedAction elements:

``` xml
        <Policy class="opendap.auth.RegexPolicy">
            <role>.*</role>
            <resource>.*(/|\.(css|png|jpg|ico|gif|xsl|jsp)|/contents.html|/catalog.html|/catalog.xml)$</resource>
            <queryString>.*$</queryString>
            <allowedAction>GET</allowedAction>
        </Policy>
        <Policy class="opendap.auth.RegexPolicy">
            <role>guest</role>
            <resource>.*\.(dds|html|das|ddx)$</resource>
            <queryString>.*$</queryString>
            <allowedAction>GET</allowedAction>
        </Policy>
        <Policy class="opendap.auth.RegexPolicy">
            <role>user</role>
            <resource>.*$</resource>
            <queryString>.*$</queryString>
            <allowedAction>GET</allowedAction>
            <allowedAction>POST</allowedAction>
        </Policy>
```

When a RegexPolicy is evaluated each of the 4 user datums are matched
against their companion regex, or list in the case of action. If and
only if all 4 match will the policy allow the access:

``` java
if(_rolePattern.matcher(roleId).matches()){
    if(_resourcePattern.matcher(resourceId).matches()){
        if(_queryStringPattern.matcher(queryString).matches()) {
            if (_allowedActions.contains(HTTP_METHOD.valueOf(httpMethod))) {
                return true;
            }
        }
    }
}
```

#### Memberships: Users, Groups, and Roles

The PDP needs to know about Membership relations in it's user
population. One can certainly imagine retrieving such information from
an LDAP server or even from Shibboleth. However the SimplePDP reads this
information from its configuration. The *Memberships* element in the
config contains two types of child elements: *group* & *role*. The
*group* element may contain zero or more child *user* elements and
nothing else. The *role* element may contain zero or more *group*
elements and nothing else.

``` xml
        <Memberships>
           <group id="guest" >
               <user id="GUEST" />
           </group>

           <group id="users" >
               <user id="ndp_opendap" />
               <user id="jhrg" />
           </group>

           <group id="cmip" >
               <user id="ndp_opendap" />
           </group>

           <group id="manager" >
               <user id="root" />
               <user id="ndp_opendap" />
               <user id="jhrg" />
           </group>

           <role id="manager">
               <group id="managers" />
               <group id="cmip" />
           </role>

           <role id="guest">
               <group id="guest" />
           </role>

        </Memberships>
```

#### Operation

At start up the SimplePDP reads it's configuration and populates in
memory objects for it's Polices and it's user memberships information.
When a request for a resource is received the following things are
passed to the PDP:

- uid
- resourceId
- queryString
- HTTP verb

(While most web access security assertions act on three things: uid,
resourceId, and HTTP verb we felt that Hyrax and other servers that may
perform significant server side activities in response to content in the
query string would benefit from having the opportunity to write Policy
implementations that can look more closely at the actual activities
being invoked in order to make better crafted allow/deny access
decisions)

The SimplePDP uses the uid to locate the list of *roles* that the user
is "in". Then for each *role* that the user is in the SimplePDP applies
each policy to the guadtuple: role, resourceId, queryString, HTTP verb.
If a policy allows the request then the PDP allows the request. If all
the policies deny the request then PDP denies the request.

request is allowed
When ANY policy allows it.

<!-- -->

request is denied
When EVERY policy denies it.

#### Examples

Allow anybody to browse around Hyrax but not to access data.
The following RegexPolicy allows any user (even one that's not in a role
since the regex ".\*" matches empty string) to browse around Hyrax but
doesn't allow them to access any data products.

``` xml
        <Policy class="opendap.auth.RegexPolicy">
            <role>.*</role>
            <resource>.*(/|\.(css|png|jpg|ico|gif|xsl|jsp)|/contents.html|/catalog.html|/catalog.xml)$</resource>
            <queryString>.*$</queryString>
            <allowedAction>GET</allowedAction>
        </Policy>
```

Allow someone in the *guest* role get the DDS, DAS, DDX, and HTML data request forms.
The following RegexPolicy allows the users in the *guest* role to access
dataset metadata.

``` xml
       <Policy class="opendap.auth.RegexPolicy">
            <role>guest</role>
            <resource>.*\.(dds|html|das|ddx)$</resource>
            <queryString>.*$</queryString>
            <allowedAction>GET</allowedAction>
        </Policy>
```

Allow the *users* role full access to Hyrax.
The following RegexPolicy allows the users in the *user* role to access
dataset metadata.

``` xml
        <Policy class="opendap.auth.RegexPolicy">
            <role>user</role>
            <resource>.*$</resource>
            <queryString>.*$</queryString>
            <allowedAction>GET</allowedAction>
            <allowedAction>POST</allowedAction>
        </Policy>
```