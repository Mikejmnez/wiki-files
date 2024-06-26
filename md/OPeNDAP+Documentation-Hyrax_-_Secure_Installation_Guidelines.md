# Overview

Security is an important and unfortunately complex issue. Any computer
security expert will tell you that the best way to keep your systems
secure is to never, ever, let them have network access. Obviously that's
not really what you had in mind or you wouldn't be thinking about
installing Hyrax. You can improve the security of Hyrax using a number
of mechanisms, from following best practices for installation, to
requiring secure authentication for the entire server.

**Disclaimer:** At OPeNDAP we consider security to be a top priority.
However, we are not security experts. What follows is a summary of what
we currently know to be the most effective methods for securing your
Hyrax installation.

# Best Practices For Secure Installation

1.  **Always use a firewall** - Keep your Hyrax server behind a firewall
    and configure the firewall to only forward requests to the
    appropriate port (typically 8080 for Tomcat and 80 for Apache) on
    your Hyrax system. Be sure to have the firewall block direct access
    to the BES.
2.  **Separate the BES and the OLFS** - We feel that it is better to run
    the BES on a second machine where **only** the BES port is open, and
    where the BES system is completely blocked by the firewall.
3.  **Restrict Log and Configuration File Access** - It is an
    unfortunate fact that many (if not most) IT security problems arise
    from within an organization and not from outside attacks. Given this
    situation it is important to restrict access to the log files
    generated, and the configuration files used, by Hyrax.
    - *Log Files* - Logs can reveal how the code works and allow a
      hostile observer to interact with the server and view important
      details about the resulting effect.
    - *Configuration Files* - By default Hyrax comes with logging set up
      to record access and errors. This can be further reduced if one
      desires. However unrestricted access to the Hyrax configuration
      files could allow a hostile individual to turn on extensive
      logging in order to learn more about the system.
    - ***Secure the logs, secure the configuration.***
4.  **Run Hyrax as a Restricted user.** - We strongly recommend that you
    run Hyrax as a restricted user. Running Hyrax as *root* or the
    *super user* is actively discouraged, as doing so creates the
    potential for dire consequences. What this means is that you should
    create a special user for bot the BES and Tomcat. These users should
    have restricted privileges and should only be allowed to write to
    the directories required by Tomcat and the BES.
5.  **Additional articles:**
    - Open Web Application Security Project (OWASP) article on how to
      secure Tomcat:



    <http://www.owasp.org/index.php/Securing_tomcat>

    - Tomcat 6 uses a different directory structure, has some logging
      changes, and has done away with the need for a deployment
      descriptor for a web app. There's an overview in this Covalent
      presentation:



    <http://www.springsource.com/files/uploads/all/pdf_files/news_event/Intro_to_Tomcat6.pdf>

# Restricting System Access

One may also choose to restrict user access to Hyrax. This can be done
by configuring Tomcat to demand user authentication, and if required,
[TSL/SSL](http://en.wikipedia.org/wiki/Secure_Sockets_Layer).

For Tomcat 5.x see:

- [Our instructions for enabling Tomcat User
  Autentication](Hyrax_-_OLFS_Configuration#Authentication_.26_Authorization "wikilink")
- [Tomcat 5.x
  Documentation](http://tomcat.apache.org/tomcat-5.5-doc/index.html)
  - [Section 6: Configuring/Managing User
    Realms](http://tomcat.apache.org/tomcat-5.5-doc/realm-howto.html)
  - [Section 12: Configuring
    SSL](http://tomcat.apache.org/tomcat-5.5-doc/ssl-howto.html)

For Tomcat 6.x see:

- [Tomcat 6.x
  Documentation](http://tomcat.apache.org/tomcat-6.0-doc/index.html)
  - [Section 6: Configuring/Managing User
    Realms](http://tomcat.apache.org/tomcat-6.0-doc/realm-howto.html)
  - [Section 12: Configuring
    SSL](http://tomcat.apache.org/tomcat-6.0-doc/ssl-howto.html)

Requiring user authentication and using SSL doesn't actually change
Hyrax's vulnerability to attack, but it willl increase the security of
your server by:

- Limiting the number of users to those with authentication credentials.
- Protecting those authentication credentials by using SSL encryption.
- Protecting data content by transmitting it in an encrypted form.