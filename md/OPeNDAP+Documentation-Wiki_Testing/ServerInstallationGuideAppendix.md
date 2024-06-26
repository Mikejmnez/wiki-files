# Getting the OPeNDAP Software

Get the software from \DODShome . If you can, it is usually advisable to
download the pre-compiled binaries. If you do so, be sure to follow the
directions that acompany the distributions, even if you're upgrading a
server, since the places where the server's components are installed can
change as the software evolves. Installation instructions for the binary
distributions can be found on the web pages where you downloadded the
software.

If you can't use the pre-compiled binaries, download the source code
from the \DODShome . You will need a few different files, depending
onthe handler(s) you want to install. The "Download Software" page has
instructions specifying which files to use. If you are building the
software yourself, be sure to read the README, INSTALL and NEWS files
included in the distributions.

You will need to download the dap-server \`base' software and one or
more handlers. The base software and each handler each is contained in a
single <font color='green'>tar.gz</font> or source
<font color='green'>rpm</font> file. To build the source files, expand
them, run the enclosed <font color='green'>configure</font> script, run
<font color='green'>make</font> and then <font color='green'>make
install</font>. Once the software is installed, see the configuration
instructions in ([<cite> dods-server,install</cite>](http://www)).