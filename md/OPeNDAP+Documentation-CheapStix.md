CheapStix is a small project to build a VM using Ubuntu on VMware to
deliver Hyrax. The VM will be copied onto cheap memory sticks and given
out. It would be great if the stuff was ready for the ESDSWG meeting
which starts on 21 Oct. 2008. This project was funded by NASA.

***See also: [Using Virtual Machines to Serve
Data](Using_Virtual_Machines_to_Serve_Data "wikilink")***

Steps:

1.  Find out a place to get memory sticks at a decent price
    1.  How much/many? ~\$11.50ea for 100 @ 2GB
    2.  With a logo? + 0.99ea for each color after two



Sent in for quotes from usb-depot.com (Styles: London and Chicago look
good). Cost is ~\$11.50ea plus 0.99ea for each color more than two if we
get 100 2GB sticks. Lead-time of about a week

What about [Chicago](http://www.usb-depot.com/usb/chicago.shtml) in Red
or [London](http://www.usb-depot.com/usb/london.shtml) in Silver?

Also requested quote from usb-flashdrive.com

Other sources: customusb.com

1.  Build the VM
    1.  Special dist of Ubuntu or not
        1.  Ubuntu: Gets us experience with debian packages and maybe
            support for building them in Makefiles
        2.  Cento gets us something we can use on our rented VMs, maybe
    2.  VMware virtual appliance?

------------------------------------------------------------------------

## Update

<img src="Memory_stick_2.png" title="Memory_stick_2.png" width="200"
alt="Memory_stick_2.png" /> **29 Oct 2008** The project was completed on
the 17th in time for the meeting and we presented a poster on the idea
there.

I used usb-flashdrive.com and ordered 100 2GB *Twister 1* drives in Blue
with a two-color logo. Cost, including shipping was \$10.25 ea. I've
checked the logo (which is a vector graphics file, .ai) into the SVN
*graphics* project.

## Building the VMs

I built two VMs, both using Ubuntu 8.04 (Hardy). The first one uses the
JeOS distribution of Hardy and does not contain a build environment or a
GUI in its distribution form while the second does contain both of those
things. However, I built the server binaries using a third version of
the VM - JeOS with a build environment.

While doing this, make liberal use of the 'snapshot' feature of WS. I
mention how I used it to go 'back in time' to a smaller VM, but I
actually used it much more than to make just one snapshot (see the
picture at below at right).

There is also a bug somewhere in Ubuntu and/or VMware involving network
persistence. There's a subsection below on a fix for that.

### Load a development environment and build & test Hyrax

Here's the procedure:

1.  Get VMware Workstation - I did all of the work in Workstation since
    it's much easier to build and fiddle with the VM in Workstation than
    server and the two are mutually exclusive.
2.  Download the ISO for JeOS - I got this from the main Ubuntu site.
3.  Crank up VM Workstation, make a new VM and tie the ISO image to the
    CDROM drive using the VM's configuration options. Also, turn on
    shared folders since they simplify transfer of stuff between the VM
    and the host.
4.  Boot the VM - There's an option to create an initial user. I used
    *jimg* and then had to hassle to change it to *opendap* later.
    Either way, it's set to use sudo and you do most of this as root
    (i.e., *sudo su*)
5.  Install VMware tools - Use the menu in Workstation (WS) and then in
    the VM as root:

`cp -a /media/cdrom0/VMwareTools*.gz /tmp`
`cd /tmp`
`tar -xzf VMwareTools*.gz`
`cd vmware-tools-distrib`
`./vmware-install.pl`

1.  Make a snapshot of the VM at this point so we can come back to this
    state (before we load up the VM with the development tools)
    later.![](VM_snapshots.png "VM_snapshots.png")
2.  Now use *apt-get* to install stuff we need to build the server
    (i.e., *sudo apt-get install <package>*)
    1.  build-essential
    2.  libcurl3, curl, libcurl4-openssl-dev - I may have used
        libcurl3-dev and not needed curl...
    3.  libxml2-dev
    4.  subversion, autoconf, automake, libtool, flex, bison - probably
        not needed if you use the tar.gz distributions
    5.  wget (curl works too, but I like wget)
    6.  libreadline-dev
    7.  libnetcdf-dev, libhdf4g-dev, libhdf5-serial-dev
    8.  sun-java6-jre - this you need to run tomcat. Don't use apt-get
        to get tomcat 5.5; I describe how to get Tomcat 6 later on.
3.  Use subversion to get the Hyrax server software (or use the tar.gz
    dists from the web page)
4.  Build the BES:
    1.  libdap 3.8.2 - configure using prefix (I built it in my own
        directory once and then again in /opt/Hyrax-1.4.2 which is where
        the code is on the distributions). Once built, add \$prefix/bin
        to PATH and always use --prefix with configure.
    2.  BES 3.6.2
    3.  dap-server 3.8.5, netcdf_handler 3.7.9, hdf4_h 3.7.9, freeform
        3.7.9, hdf5 1.2.3 - use *make install* and *make bes-conf*
    4.  Add *bes* user and group (vi /etc/group and then *useradd -g bes
        -d /dev/null -s /bin/false bes*)
    5.  Edited *bes.conf* to user the bes user/group. I added (mkdir)
        /opt/Hyrax-1.4.2/log and set the log file to
        /opt/Hyrax\*/log/bes.log)
    6.  chmod/grp bes /opt/Hyrax-1.4.2/ - so the log file works and may
        otherwise be needed.
    7.  **Worked** That is, the BES starts and I can talk to it with
        *bescmdln*
5.  Get Tomcat - the tomcat 5.5 code available from apt-get is hosed.
    Instead, download tomcat from Apace (*wget
    .../apache-tomcat-6.0.18.tar.gz*)
6.  Unpack Tomcat in /opt - I made a sym link from /opt/tomcat to
    /opt/apache-tomcat-...
7.  Get the OLFS 1.4.2 webapp.jar from the web page, open and cp the
    opendap.war in /opt/apache-tomcat-.../webapps
8.  Start Tomcat - You need to set JAVA_HOME for this to work (*export
    JAVA_HOME=/usr/lib/jvm/java-6-sun*)
9.  **Worked** Given that the BES was already running and the *make
    bes-conf* targets installed sample data and modified the *bes.conf*
    file to match.

### Shrink the JeOS VM

There is a trick I used to get the JeOS VM small: Load development
packages to build the server, then go back to a snapshot before anything
was loaded except VMware Tools and load only those runtime (not
development) packages needed. For example, the dist VM uses libnetcdf4
instead of libnetcdf4-dev which was needed to build the server.

1.  Run *make clean* in the Hyrax source dirs or remove the source.
2.  Use tar to package the binary build - the entire /opt/Hyrax-1.4.2
    tree
3.  Copy to the shared folder directory to put that tar on the host OS -
    the shared folder directory mounts under /mnt when VMware Tools is
    running.
4.  Now, shut down the VM and revert to the earlier snapshot where
    VMware tools had just been installed but nothing else was done. I
    did this by copying the VM directory on the host and then using the
    revert option in WS, but I think the copy was not really necessary
    so long as you take snapshots of any state to which you want to
    return.
5.  Boot the *snapshot2* VM
6.  Copy the tar file from the shared folder to /opt and expand.
7.  Add /opt/Hyrax-1.4.2/bin to PATH
8.  Use *apt-get* to add *libcurl3* and *libxml2*
9.  Now the client *getdap* (which was built/installed as part of the
    libdap build) works
10. Starting The BES
    1.  added the *bes* user: Use *vi* to add the *bes* group to
        */etc/group*, then *useradd -g bes -d /dev/null -s /bin/false
        bes*
    2.  apt-get libhdf4g, libhdf5-serial-1.6.5-0 libnetcdf4
    3.  BES worked - start and then test with *bescmdln*
11. Get Tomcat working
    1.  apt-get sun-java6-jre
    2.  Get tomcat 6.0.18.tar.gz (using wget)
    3.  Expand that tar.gz file in /opt. I Made a symlink from
        /opt/tomcat to /opt/apache...
    4.  Set JAVA_HOME to /var/lib/jvm/java-6-sun (note that it's not
        exactly the same as the package name)
    5.  Copy *opendap.war* to tomcat's webapps
    6.  Set CATALINA_HOME to /opt/tomcat - I don't know if this was
        necessary
    7.  Start tomcat (assuming the BES is still running from before)
    8.  Test with getdap
12. Hyrax works

Now we have a small VM with a working Hyrax and only the packages needed
to run the code - take a snapshot.

#### The udev hack

I found that sometimes the VM would start in WS with networking broken.
I don't see a pattern, but looking at the network devices using
*ifconfig -a*, eth0 is hosed (it does not say 'UP'). to fix, cd to
/etc/udev and the file *70-persistent-net-rules* to remove the line
about eth0 and edit the line for eth1 replacing 'eth1' with 'eth0'.
Restart udev and networking using the eponymous scripts in /etc/init.d.
Now ifconfig should show eth0 as 'UP'

### Package the VM for distribution

Now set the VM so that the image will prompt the first person to start
it for a password. Since we're distributing the VM with a known username
and password, this provides some assurance that the password really will
be changed. This is also a good time to make permanent the changes to
PATH and JAVA_HOME. Lastly, really shrink the VM.

Edit /etc/bash.bashrc so that it calls /opt/initial-config unless that
has already been called (use a semaphore file)

    export JAVA_HOME=/usr/lib/jvm/java-6-sun
    export CATALINA_HOME=/opt/tomcat
    export hyrax_prefix=/opt/Hyrax-1.4.2
    export PATH=$hyrax_prefix/bin:$PATH

    if [ ! -e /etc/opt/initial-config-run ]; then
        /opt/init/initial-config.sh
        sudo touch /etc/opt/initial-config-run
    fi

I added the environment variable stuff here, too.

Here's the shell script it runs:

    #!/bin/sh
    #

    # Let's change the user's password
    echo "Thank you for using the OPeNDAP Hyrax appliance"
    echo "For the security of the appliance, we need you to change this user passwor
    d now."
    passwd

    # You can add here any first user login actions that you require

Since the *opendap* user can run sudo, we could use *sudo passwd
opendap* in place of *passwd*. The former would be slicker since the
initial user would not be asked to type their password (which they have
just entered and is well known) to then change their password.

#### Last steps

Remove anything that smacks of *ssh*. This means that if any ssh
packages were loaded, remove them using *apt-get --purge remove* and
also look for *.ssh* directories in login accounts. It's important to
not distribute VMs with this stuff...

Now, shrink the disk files - to get the VM to fit in the smallest space
without using compression, we need to do the following: Zero fill the
disks (files, really) and then use a VMware tool to 'shrink' them. As
root:

    cat /dev/zero > zero.fill
    sync
    sleep 1
    sync
    rm -f zero.fill

Stop the VM (shutdown) and in the host OS run *vmware-vdiskmanager -k
<disk file master>* (there are likely a bunch of 'vmdk' files, the
'master' file is named something like *Ubuntu JeOS-000008-cl1.vmdk* and
is relatively small. Ignore the ones name ...s001.vmdk.

I used zip to compress the resulting VM (which is a directory). I could
not figure out how to run zip from the command line, but Fedora/Gnome
has a decent 'Create Archive...' option. I chose zip because I know
windows users have it.

## Running the VM

Use VM server to run the VM.

### Data Access

To access data, the VM will need to either have a copy of the data on
its local disk or use a network share. Let's assume the latter.

On the host OS, export the share using NFS or Samba. It's a good idea to
export the share as read-only. On the VM use apt-get to add samba or nfs
and configure it to mount the remote share. Edit
/opt/Hyrax-1.4.2/etc/bes/bes.conf so that it uses the remote share as
the DataRoot directory.

### Network Access

NB: I made these changes to the RSS copy of the VM only.
[jimg](User:Jimg "wikilink") 09:59, 12 February 2009 (PST)

VMware Server has extensive documentation on this topic.

The current VM built with Ubuntu JeOS uses Bridged networking and a
static IP, based on work/feedback from Marty Brewer at RSS, Inc. The
static IP number is 192.168.0.100 and the DNS servers are set to the
servers provided by the OpenDNS project. You may be able to use it as
is. However, here are the files you need to edit to change these
settings:

In /etc/network/interfaces change the IP number. Here's what the file
looks like now:

    # This file describes the network interfaces available on your system
    # and how to activate them. For more information, see interfaces(5).

    # The loopback network interface
    auto lo
    iface lo inet loopback

    # This is a list of hotpluggable network interfaces.
    # They will be activated automatically by the hotplug subsystem.
    mapping hotplug
            script grep
            map eth0

    # The primary network interface
    auto eth0
    iface eth0 inet static
            address 192.168.0.100
            netmask 255.255.255.0
            network 192.168.0.0
            broadcast 192.168.0.255
            gateway 192.168.0.1

In /etc/resolv.conf change the IP numbers of the two *nameserver* lines
to local names servers if you don't want to use the OpenDNS servers.

The VM supports both bridged and NAT networking options and you can
switch between these modes in the VMware Server Settings dialog.