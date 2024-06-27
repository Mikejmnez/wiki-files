## Current Problem

Currently, any modules that the BES loads adds information to the one
BES configuration file. This causes problems in many scenarios.

- Development work where you have to do a 'make install' many times,
  which over-writes the BES configuration file
- Upgrading the BES, the install over-writes the configuration file

## Suggested Solution

1.  The BES configuration file holds only the information for the BES,
    no loaded modules
2.  Add new keywords in the configuration file to include other
    configuration files, like what Apache HTTPD does
    - include conf.d/\*.conf - we would do something similar to this
    - allow regular expression matching of files (If we want it where
      they don't have to edit the bes.conf file, then we will need this)
    - allow for a direct include
    - allow multiple includes
    - recommend that they leave the include line alone, and rename conf
      files if they don't want them installed (like with Apache HTTPD)
    - Modify the behavior for BES.modules, or have a new keyword like
      "Add BES.modules", or BES.addModules=nc and keep the
      BES.nc.module=<path_to_so>
3.  When doing an install, don't install a new configuration file, but
    update the one that is there, if need be. Same with the install
    target to make, only make changes that need to be made.
    - Do not edit an existing configuration file as part of make
      install. Install a replacement file that, maybe, is built from the
      existing file using a new name. So read bes.conf and use that to
      build bes.conf.new that is then copied to the new directory. We
      can include a special target to do the 'dangerous' operation of
      moving the bes.conf.new to bes.conf so our automated builds run
      more smoothly, but sysadmins will balk at having 'make install'
      modify their configuration(s). This also applies to the rpm
      upgrade process.
4.  Allow for addition to parameters
    - Perhaps BES.Var+=
    - Perhaps BES.Var=, and if there is already a value, add to it

[BES Configuration File](Category:Development "wikilink") [BES
Configuration File](Category:Hyrax_Development "wikilink")