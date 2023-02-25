DNS BIND 9 - Quick Setup
=======================================
More info with RFC 1591. Exists a DNS client, a DNS server running on port 53. DNS servers simply resolve domain names to IPs.  Remember our lab is setup currently where VirtualBox is NAT'ing address. The VirtualBox IP Gateway is :code:`10.0.2.1`

DNS Centos VM
---------------------------------------------
Duplicate the DHCP VM using VirtualBox tools, and rename the host to:

- ns1

Keep all of the other settings.

Install BIND 9 on Centos VM
---------------------------------------------


