[\<\<\< Back to Developer Info](Developer_Info#AWS_Tips "wikilink")

If you trust the AWS firewall, then you'll probably want to shut off the
now redundant Linux firewall:

1.  Stop the ipchains service: **service ipchains stop**
2.  Stop the iptables service: **service iptables stop**
3.  Stop the ipchains service from starting when you restart the server:
    **chkconfig ipchains off**
4.  Stop the iptables service from starting when you restart the server:
    **chkconfig iptables off**