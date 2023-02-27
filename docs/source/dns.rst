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

4 BIND Config files
-------------------------

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

