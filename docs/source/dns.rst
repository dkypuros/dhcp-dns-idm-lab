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
    *2. /etc/named.conf
     3. /etc/named.rfc1912.zones
     4. /etc/named.root.key
     5. /etc/rndc.conf
     6. /etc/rndc.key
     7. /etc/sysconfig/named
     8. /var/named/named.ca
     9. /var/named/named.empty
    *10. /var/named/named.localhost
    *11. /var/named/named.loopback

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

Backup existing conf files. Example of original :code:`named.conf` file_ [*select RAW*]

.. _file: https://github.com/dkypuros/dhcp-dns-idm-lab/blob/main/docs/source/raw-output/named.conf.orig.txt

.. code-block:: bash

    mv /etc/named.conf /etc/named.conf.bak

.. code-block:: bash

    touch /etc/named.conf

.. code-block:: bash

    vim /etc/named.conf

**named.conf**

.. code-block:: bash

    //
    // named.conf
    //
    // Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
    // server as a caching only nameserver (as a localhost DNS resolver only).
    //
    // See /usr/share/doc/bind*/sample/ for example named configuration files.
    //

    options {
        listen-on port 53 { 10.0.2.5; };
        listen-on-v6 port 53 { ::1; };
        directory 	"/var/named";
        dump-file 	"/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file	"/var/named/data/named.secroots";
        recursing-file	"/var/named/data/named.recursing";
        allow-query     { localhost; };

        /* 
        - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
        - If you are building a RECURSIVE (caching) DNS server, you need to enable 
        recursion. 
        - If your recursive DNS server has a public IP address, you MUST enable access 
        control to limit queries to your legitimate users. Failing to do so will
        cause your server to become part of large scale DNS amplification 
        attacks. Implementing BCP38 within your network would greatly
        reduce such attack surface 
        */
        recursion yes;

        dnssec-enable yes;
        dnssec-validation yes;

        managed-keys-directory "/var/named/dynamic";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";

        /* https://fedoraproject.org/wiki/Changes/CryptoPolicy */
        include "/etc/crypto-policies/back-ends/bind.config";
    };

    logging {
            channel default_debug {
                    file "data/named.run";
                    severity dynamic;
            };
    };

    zone "." IN {
        type hint;
        file "named.ca";
    };

    include "/etc/named.rfc1912.zones";
    include "/etc/named.root.key";

    zone "example.com" IN {
        type master;
        file "/var/named/example.com.zone";
        allow-update { none; };
        allow-query { any; };
        notify yes;
    };

    zone "2.0.10.in-addr.arpa" IN {
        type master;
        file "/var/named/example.com.rev";
        allow-update { none; };
        allow-query { any; };
        notify yes;
    };

.. code-block:: bash

    touch /var/named/example.com.zone
    vim /var/named/example.com.zone
    

.. code-block:: bash

    $ORIGIN example.com.
    $TTL	1w
    example.com.	IN	SOA	ns1.example.com. hostmaster.example.com. (
                3		; Serial
                1w		; Refresh
                1d		; Retry
                28d		; Expire
                1w) 	; Negative Cache TTL
                
    ; name servers - NS records
            IN	NS	ns1.example.com.

    ; name servers - A records
    ns1.example.com.		IN	A	10.0.2.5

    ; 10.0.2.0/24 - A records
    dhcp1.example.com.		IN	A	10.0.2.4
    id1.example.com.		IN	A	10.0.2.6


.. code-block:: bash

    touch /var/named/example.com.rev
    vim /var/named/example.com.rev


.. code-block:: bash

    $TTL	1w
    @	IN	SOA	ns1.example.com. hostmaster.example.com. (
                3		; Serial
                1w 		; Refresh
                1d		; Retry
                28d		; Expire
                1w) 		; Negative Cache TTL
                
    ; name servers - NS records
            IN	NS	ns1.example.com.

    ; PTR Records
    5    	IN	PTR	ns1.example.com.
    4    	IN	PTR	dhcp1.example.com.
    6    	IN	PTR	id1.example.com.

Check DNS Conf files
------------------------


.. code-block:: bash

    named-checkzone example.com example.com.zone

**output**

.. code-block:: bash

    zone example.com/IN: loaded serial 3
    OK
