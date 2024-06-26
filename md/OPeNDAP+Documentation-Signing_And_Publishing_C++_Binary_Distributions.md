How to sign and publish the C++ binaries for libad and the BES.

## Retrieve the binary RPM packages built using the CI/CD process

1.  Pull the binary packages from our opendap.travis.build S3 bucket.
    Log into the OPeNDAP AWS account and goto:
    <https://s3.console.aws.amazon.com/s3/buckets/opendap.travis.build?region=us-east-1&tab=objects>
2.  Locate and download the most recent builds of the libdap4 to your
    system. This is easily done by filtering on the string "libdap" and
    then sorting by date so the most recent builds are at the top.. Be
    sure to get the library, developer, and debug info rpms for each
    architecture. For example if the version is <numbers> and the
    architecture is CentOS-7 get:
    - *libdap-<numbers>.el7.x86_64.rpm*
    - *libdap-devel-<numbers>.el7.x86_64.rpm*
    - *libdap-debuginfo-<numbers>.el7.x86_64.rpm*
3.  Locate and download the most recent builds of the BES to your
    system. This is easily done by filtering on the string "bes" and
    then sorting by date so the most recent builds are at the top. Be
    sure to get the library, developer, and debug info rpms for each
    architecture. For example if the version is <numbers> and the
    architecture is CentOS-7 get:
    - *bes-<numbers>.el7.x86_64.rpm*
    - *bes-devel-<numbers>.el7.x86_64.rpm*
    - *bes-debuginfo-<numbers>.el7.x86_64.rpm*

## Sign the RPM packages

In the directory containing only the RPM packages for the release run
the following command:

*for i in \*.rpm;do gpg --detach-sign --local-user security@opendap.org
\$i; done*

## Publish the RPM packages and their signatures

1.  Use sftp to log into www.opendap.org
2.  *cd httpdocs/pub/binary/hyrax.XX.YY/<os-name>*

    (where XX.YY is the major release number, ex: 1.16 and <os-name> is
    something like centos-7 for CentOS-7 RPMs):
    <https://www.opendap.org/pub/binary/hyrax-1.16/centos-7.x/>
3.  *put \*.rpm\**
4.  *exit*