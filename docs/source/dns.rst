DNS BIND 9 - Complete Setup
=======================================


Install BIND 9
-------------------

.. code-block:: bash

    sudo dnf update
    sudo dnf install bind bind-utils
    named -v

**My version**

.. code-block:: bash

    BIND 9.11.36-RedHat-9.11.36-7.el8 (Extended Support Version) <id:68dbd5b>

Start the service
-------------------

Start up the BIND 9 service.

:ref:`Start, Enable, Status <dnsfigure1>`

Example Output

.. code-block:: bash

    ● named.service - Berkeley Internet Name Domain (DNS)
    Loaded: loaded (/usr/lib/systemd/system/named.service; disabled; vendor preset: disabled)
    Active: active (running) since Mon 2023-02-27 14:01:55 CST; 27s ago
    Process: 3635 ExecStart=/usr/sbin/named -u named -c ${NAMEDCONF} $OPTIONS (code=exited, status=0/SUCCESS)
    Process: 3631 ExecStartPre=/bin/bash -c if [ ! "$DISABLE_ZONE_CHECKING" == "yes" ]; then /usr/sbin/named-checkconf >
    Main PID: 3636 (named)
    Tasks: 5 (limit: 11028)
    Memory: 15.8M
    CGroup: /system.slice/named.service
        └─3636 /usr/sbin/named -u named -c /etc/named.conf

    Feb 27 14:01:55 ns1.example.com named[3636]: network unreachable resolving './DNSKEY/IN': 2001:500:200::b#53
    Feb 27 14:01:55 ns1.example.com named[3636]: network unreachable resolving './NS/IN': 2001:500:200::b#53
    Feb 27 14:01:55 ns1.example.com named[3636]: network unreachable resolving './DNSKEY/IN': 2001:7fe::53#53
    Feb 27 14:01:55 ns1.example.com named[3636]: network unreachable resolving './NS/IN': 2001:7fe::53#53
    Feb 27 14:01:55 ns1.example.com named[3636]: network unreachable resolving './DNSKEY/IN': 2001:7fd::1#53
    Feb 27 14:01:55 ns1.example.com named[3636]: network unreachable resolving './NS/IN': 2001:7fd::1#53
    Feb 27 14:01:55 ns1.example.com named[3636]: network unreachable resolving './DNSKEY/IN': 2001:dc3::35#53
    Feb 27 14:01:55 ns1.example.com named[3636]: network unreachable resolving './NS/IN': 2001:dc3::35#53
    Feb 27 14:02:05 ns1.example.com named[3636]: managed-keys-zone: Unable to fetch DNSKEY set '.': timed out
    Feb 27 14:02:05 ns1.example.com named[3636]: resolver priming query complete


Review IP Network Info of BINd Server
---------------------------------------------

.. code-block:: bash

    enp0s3: connected to enp0s3
            "Intel 82540EM"
            ethernet (e1000), 08:00:27:89:EE:03, hw, mtu 1500
            ip4 default
            inet4 10.0.2.5/24
            route4 10.0.2.0/24 metric 100
            route4 default via 10.0.2.1 metric 100
            inet6 fe80::a00:27ff:fe89:ee03/64
            route6 fe80::/64 metric 1024

    lo: unmanaged
            "lo"
            loopback (unknown), 00:00:00:00:00:00, sw, mtu 65536

    DNS configuration:
            servers: 10.0.2.1
            interface: enp0s3

    Use "nmcli device show" to get complete information about known devices and
    "nmcli connection show" to get an overview on active connection profiles.

    Consult nmcli(1) and nmcli-examples(7) manual pages for complete usage details.

**Check BIND via Dig Command**

.. code-block:: bash

    dig 127.0.0.1 . NS

Example output.

.. code-block:: bash

        ; <<>> DiG 9.11.36-RedHat-9.11.36-7.el8 <<>> 127.0.0.1 . NS
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 34289
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 65494
    ;; QUESTION SECTION:
    ;127.0.0.1.			IN	A

    ;; AUTHORITY SECTION:
    .			7148	IN	SOA	a.root-servers.net. nstld.verisign-grs.com. 2023022701 1800 900 604800 86400

    ;; Query time: 0 msec
    ;; SERVER: 10.0.2.1#53(10.0.2.1)
    ;; WHEN: Mon Feb 27 14:11:40 CST 2023
    ;; MSG SIZE  rcvd: 113

    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 34102
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 65494
    ;; QUESTION SECTION:
    ;.				IN	NS

    ;; ANSWER SECTION:
    .			7076	IN	NS	a.root-servers.net.
    .			7076	IN	NS	h.root-servers.net.
    .			7076	IN	NS	d.root-servers.net.
    .			7076	IN	NS	k.root-servers.net.
    .			7076	IN	NS	l.root-servers.net.
    .			7076	IN	NS	i.root-servers.net.
    .			7076	IN	NS	b.root-servers.net.
    .			7076	IN	NS	f.root-servers.net.
    .			7076	IN	NS	m.root-servers.net.
    .			7076	IN	NS	c.root-servers.net.
    .			7076	IN	NS	e.root-servers.net.
    .			7076	IN	NS	j.root-servers.net.
    .			7076	IN	NS	g.root-servers.net.

    ;; Query time: 0 msec
    ;; SERVER: 10.0.2.1#53(10.0.2.1)
    ;; WHEN: Mon Feb 27 14:11:40 CST 2023
    ;; MSG SIZE  rcvd: 239

Change DNS Server on DHCP Server
-----------------------------------------

Allow DHCP server to start using ns1.example.com for a DNS server rather than :code:`10.0.2.1` (aka VirtualBox)

Look for  :code:`"data": "10.0.2.1"` amd replace the IP with the ns1 IP :code:`10.0.2.5`

.. code-block:: bash

    vim /etc/kea/kea-dhcp4.conf

And restart the service.

.. code-block:: bash

    systemctl restart kea-dhcp4

Disable Firewall Temporarily
---------------------------------

.. code-block:: bash

    systemctl disable firewalld

BIND 9 on Centos 8 - 11 Config Files
------------------------------------------
BIND 9 on CentOS 8 has the following config files available for reference and usage:

.. code-block:: bash

    1. /etc/logrotate.d/named
    2. /etc/named.conf
    3. /etc/named.rfc1912.zones
    4. /etc/named.root.key
    5. /etc/rndc.conf
    6. /etc/rndc.key
    7. /etc/sysconfig/named
    8. /var/named/named.ca
    9. /var/named/named.empty
    10. /var/named/named.localhost
    11. /var/named/named.loopback

4 BIND Config files Centos 8
----------------------------

**2. /etc/named.conf**

The :code:`/etc/named.conf` file on CentOS 8 is the main configuration file for the Bind 9 DNS server. It contains global options and settings for the DNS server, as well as definitions for the zones that the DNS server will serve.

The :code:`named.conf` file is used to define the DNS server's behavior and to specify which zones it will serve. For example, the options section might specify whether or not the DNS server will perform recursion, and the zone sections will define the DNS records for each zone, such as the Start of Authority (SOA) record, Name Server (NS) records, and the Resource Records (RRs) that map domain names to IP addresses.

The /etc/named.conf file is critical to the operation of the Bind 9 DNS server, as it defines the server's behavior and the zones that it serves. Any changes to this file will require the DNS server to be restarted in order for the changes to take effect.

**3. /etc/named.rfc1912.zones**
Reference to make your own zone file for a domain such as "example.com"

**10. /var/named/named.localhost**

The /var/named/named.localhost file on CentOS 8 is a zone file for the localhost domain. This file maps the name localhost to the loopback address 127.0.0.1, which is the IP address of the local system.

**11. /var/named/named.loopback**

The /var/named/named.loopback file on CentOS 8 is a zone file for the loopback address 127.in-addr.arpa. This file maps the IP address 127.0.0.1 to the name localhost.

Bind 9 Configuration on CentOS 8
-----------------------------------

Backup existing conf files.

.. code-block:: bash

    mv /etc/named.conf /etc/named.conf.bak

.. code-block:: bash

    touch /etc/named.conf

.. code-block:: bash

    options {
        directory "/var/cache/bind";
        auth-nxdomain no;

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable 
        // nameservers, you probably want to use them as forwarders.  
        // Uncomment the following block, and insert the addresses replacing 
        // the all-0's placeholder.

        listen-on  { 172.21.0.43; };
        allow-query { localhost; 172.21.0.0/16; };
        allow-transfer { none; };

        forwarders { 172.21.0.1; };
        recursion yes;

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================

        //dnssec-validation auto;

        //listen-on-v6 { any; };
        };


4 BIND Config files Debian
----------------------------

Backup existing conf files.

.. code-block:: bash

    mv /etc/bind/named.conf.options /etc/bind/named.conf.options.bak

.. code-block:: bash

    touch /etc/bind/named.conf.options

Copy named.conf.options into VIM

.. code-block:: bash

    vim /etc/bind/named.conf.options

**named.conf.options**
Configuration file that sets :code:`global options that apply to the DNS server as a whole`. Contains settings such as the logging configuration, the directory where the zone files are stored and security options.

.. code-block:: bash


**named.conf.local**
Configuration file for defining the :code:`DNS zones that the server is authoritative for`. Contains information such as zone name, the type of zone (e.g. forwarding or reverse), and the location of the zones data file.

.. code-block:: bash


**db.127**
Lookup zones define actual :code:`name information` (e.g. name to IP, and IP to name)

.. code-block:: bash


**db.local**
Lookup zones define actual :code:`name information` (e.g. name to IP, and IP to name)

.. code-block:: bash

