DNS BIND 9 - Foundational Setup
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

.. code-block:: bash

    systemctl status named

.. code-block:: bash

    systemctl start named

.. code-block:: bash

    systemctl enable named


Example Output

.. code-block:: bash

    ● named.service - Berkeley Internet Name Domain (DNS)
    Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; vendor preset: disabled)
    Active: active (running) since Thu 2023-03-02 13:35:32 CST; 3min 48s ago
    Process: 851 ExecStart=/usr/sbin/named -u named -c ${NAMEDCONF} $OPTIONS (code=exited, status=0/SUCCESS)
    Process: 842 ExecStartPre=/bin/bash -c if [ ! "$DISABLE_ZONE_CHECKING" == "yes" ]; then /usr/sbin/named-checkconf -z "$NAMEDCONF"; else echo "Checking of zone files is disabled"; fi (code=exited, status=0/SUCCESS)
    Main PID: 853 (named)
        Tasks: 5 (limit: 11028)
    Memory: 23.2M
    CGroup: /system.slice/named.service
            └─853 /usr/sbin/named -u named -c /etc/named.conf

    Mar 02 13:35:32 ns1.example.com named[853]: command channel listening on 127.0.0.1#953
    Mar 02 13:35:32 ns1.example.com named[853]: configuring command channel from '/etc/rndc.key'
    Mar 02 13:35:32 ns1.example.com named[853]: command channel listening on ::1#953
    Mar 02 13:35:32 ns1.example.com named[853]: managed-keys-zone: loaded serial 0
    Mar 02 13:35:32 ns1.example.com named[853]: zone 2.0.10.in-addr.arpa/IN: loaded serial 3
    Mar 02 13:35:32 ns1.example.com named[853]: zone example.com/IN: loaded serial 3
    Mar 02 13:35:32 ns1.example.com named[853]: all zones loaded
    Mar 02 13:35:32 ns1.example.com named[853]: running
    Mar 02 13:35:32 ns1.example.com systemd[1]: Started Berkeley Internet Name Domain (DNS).
    Mar 02 13:35:33 ns1.example.com named[853]: listening on IPv4 interface enp0s3, 10.0.2.5#53


Review IP Network Info of BIND9 Server
---------------------------------------------

We're Temporarily using VirtualBox to get out of the network. :code:`servers: 10.0.2.1`

.. code-block:: bash

    nmcli

.. code-block:: bash

    ping google.com


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

    options {
            directory "/var/named";
            dump-file "/var/named/data/cache_dump.db";
            statistics-file "/var/named/data/named_stats.txt";
            memstatistics-file "/var/named/data/named_mem_stats.txt";
            recursion yes;
            allow-query { any; };
            forwarders { 10.0.2.1; };
    };

    zone "example.com" 
        {
        type master;
        file "/etc/named/zones/db.example.com"; 
        };

    zone "2.0.10.in-addr.arpa"
        {
        type master;
        file "/etc/named/zones/db.2.0.10";
        };


.. code-block:: bash

    named-checkconf -z /etc/named.conf

**db.local / db.2.0.10**

.. code-block:: bash

    mkdir /etc/named/zones

.. code-block:: bash

    touch /etc/named/zones/db.example.com

.. code-block:: bash
    
    vim /etc/named/zones/db.example.com

.. code-block:: bash

    named-checkzone example.com /etc/named/zones/db.example.com


File contents :code:`db.local` -> :code:`db.example.com`

.. code-block:: bash

    $ORIGIN example.com.
    $TTL	1w
    example.com.    IN	SOA     ns1.example.com. hostmaster.example.com. (
                        3		    ; Serial
                        1w		    ; Refresh
                        1d		    ; Retry
                        28d		    ; Expire
                        1w) 	    ; Negative Cache TTL
                
    ; name servers - NS records
                    IN	NS      ns1.example.com.

    ; name servers - A records
    ns1.example.com.		IN	A	10.0.2.5

    ; 10.0.2.0/24 - A records
    dhcp1.example.com.          IN  A	10.0.2.4
    id1.example.com             IN  A   10.0.2.6
    centos-client.example.com   IN  A   10.0.2.7



**db.127 / db.2.0.10**

.. tip::
    Feel free to come back here and add a statement for client-centos under the "; PTR Records" such as "101 IN PTR  client-centos.example.com"

.. code-block:: bash

    touch /etc/named/zones/db.2.0.10

.. code-block:: bash

    vim /etc/named/zones/db.2.0.10

File Contents :code:`db.127` -> :code:`db.2.0.10`

.. code-block:: bash

    $TTL	1w
    @	    IN	SOA	ns1.example.com. hostmaster.example.com. (
                3		; Serial
                1w 		; Refresh
                1d		; Retry
                28d		; Expire
                1w) 	; Negative Cache TTL
                
    ; name servers - NS records
            IN	NS	ns1.example.com.

    ; PTR Records
    5    	IN	PTR	ns1.example.com.
    4    	IN	PTR	dhcp1.example.com.
    6    	IN	PTR	id1.example.com.
    7    	IN	PTR	client-centos.example.com.


.. code-block:: bash

    named-checkzone 2.0.10.in-addr.arpa /etc/named/zones/db.2.0.10

**Output from my terminal**

.. code-block:: bash

    zone 2.0.10.in-addr.arpa/IN: loaded serial 3
    OK

Change NS1 DNS to itself
--------------------------------------------

Initially, every system was set with DNS to the VirtualBox gateway. The ns1 (BIND9) server needs to have this changed to point to itself as the DNS server.

.. code-block:: bash

    sudo nmcli connection modify "enp0s3" ipv4.dns 10.0.2.5

.. code-block:: bash

    nmcli con mod enp0s3 ipv4.dns-search "example.com"

Reboot or flash the NIC from inside the VM

.. code-block:: bash

    reboot

Start / Reload BIND9 service
------------------------------------

Use instructions :ref:`here if needed. <dnsfigure1>`. Make sure there are no errors. 


Test BIND9 Service
-----------------------------

**Dig forward test**

.. code-block:: bash

    dig 10.0.2.5


**Dig Reverse Test:**

The command "dig -x 10.0.2.5" specifically is used to perform a reverse DNS lookup on the IP address "10.0.2.5". "-x" - This option tells the "dig" command to perform a reverse DNS lookup (i.e., a lookup of a domain name given an IP address). "10.0.2.5" - This is the IP address for which a reverse DNS lookup will be performed

.. code-block:: bash

    dig -x 10.0.2.5

**Dig Output**

status: NOERROR means success. NXDOMAIN means it not working correctly. 

.. tip::

    check DNS settings for NS1 server. They should be set to itself.

.. code-block:: bash

    ; <<>> DiG 9.11.36-RedHat-9.11.36-7.el8 <<>> -x 10.0.2.5
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 44917
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 1232
    ; COOKIE: 7f0b2cd815be6e152b5f848c63fe49c44eb1b0199fad9465 (good)
    ;; QUESTION SECTION:
    ;5.2.0.10.in-addr.arpa.		IN	PTR

    ;; ANSWER SECTION:
    5.2.0.10.in-addr.arpa.	604800	IN	PTR	ns1.example.com.

    ;; AUTHORITY SECTION:
    2.0.10.in-addr.arpa.	604800	IN	NS	ns1.example.com.

    ;; ADDITIONAL SECTION:
    ns1.example.com.	604800	IN	A	10.0.2.5

    ;; Query time: 0 msec
    ;; SERVER: 10.0.2.5#53(10.0.2.5)
    ;; WHEN: Tue Feb 28 12:36:52 CST 2023
    ;; MSG SIZE  rcvd: 137

**Check Ports**

.. code-block:: bash

    ss -tulnw

**Terminal Output**
Notice  :code:`10.0.2.5:53` That's the open BIND9 port for the server. (UDP/TCP)


.. code-block:: bash

    Netid                   State                    Recv-Q                   Send-Q                                      Local Address:Port                                        Peer Address:Port                   Process                   
    icmp6                   UNCONN                   0                        0                                                       *:58                                                     *:*                                                
    udp                     UNCONN                   0                        0                                                 0.0.0.0:56362                                            0.0.0.0:*                                                
    udp                     UNCONN                   0                        0                                                10.0.2.5:53                                               0.0.0.0:*                                                
    udp                     UNCONN                   0                        0                                                 0.0.0.0:5353                                             0.0.0.0:*                                                
    udp                     UNCONN                   0                        0                                                    [::]:53                                                  [::]:*                                                
    udp                     UNCONN                   0                        0                                                    [::]:5353                                                [::]:*                                                
    udp                     UNCONN                   0                        0                                                    [::]:37126                                               [::]:*                                                
    tcp                     LISTEN                   0                        10                                               10.0.2.5:53                                               0.0.0.0:*                                                
    tcp                     LISTEN                   0                        128                                               0.0.0.0:22                                               0.0.0.0:*                                                
    tcp                     LISTEN                   0                        5                                               127.0.0.1:631                                              0.0.0.0:*                                                
    tcp                     LISTEN                   0                        128                                             127.0.0.1:953                                              0.0.0.0:*                                                
    tcp                     LISTEN                   0                        10                                                   [::]:53                                                  [::]:*                                                
    tcp                     LISTEN                   0                        128                                                  [::]:22                                                  [::]:*                                                
    tcp                     LISTEN                   0                        5                                                   [::1]:631                                                 [::]:*                                                
    tcp                     LISTEN                   0                        128                                                 [::1]:953                                                 [::]:*    


**NS1 Dig**

.. code-block:: bash

    dig ns1.example.com

.. code-block:: bash

    ; <<>> DiG 9.11.36-RedHat-9.11.36-7.el8 <<>> ns1.example.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 11590
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 1232
    ; COOKIE: bfe3f843c088c43a41ee022f63fe4b79c68d538f0e729329 (good)
    ;; QUESTION SECTION:
    ;ns1.example.com.		IN	A

    ;; ANSWER SECTION:
    ns1.example.com.	604800	IN	A	10.0.2.5

    ;; AUTHORITY SECTION:
    example.com.		604800	IN	NS	ns1.example.com.

    ;; Query time: 0 msec
    ;; SERVER: 10.0.2.5#53(10.0.2.5)
    ;; WHEN: Tue Feb 28 12:44:09 CST 2023
    ;; MSG SIZE  rcvd: 102


Configure DHCP Server to handout New DNS IP
--------------------------------------------------

Head over to the DHCP server.

.. code-block:: bash

    sudo vim /etc/kea/kea-dhcp4.conf

Change config.

.. code-block:: bash

    {
    "name": "domain-name-servers",
    "data": "10.0.2.5"},


Test CentOS 8 Client System
----------------------------------

- make sure cient system is attached to correct network
- set hostname to just "centos-client" not "centos-client.example.com"?

Review "DNS configuration:"

We want the DNS server to point to our new BIND9 server, and the default gateway to be moved from 10.0.2.5 to 10.0.2.1. 

.. warning::
    
    ?? I'm not sure why we initially set the DHCP server router config to 10.0.2.5. I need to research this later. ==> PS I found out when the course trainer changed this setting. He uses Debian as his test VM, where I'm using CentOS. You see him alter these settings starting on 10:13:41 for id1 etc. He doesn't really alter DNS on CentOS machines until later and as needed. 

.. code-block:: bash

    nmcli

.. code-block:: bash

    dnf install bind-utils.x86_64 -y
    dhclient -r
    dhclient

.. code-block:: bash

    ping dhcp1

.. code-block:: bash

    PING dhcp1.example.com (10.0.2.4) 56(84) bytes of data.
    64 bytes from dhcp1.example.com (10.0.2.4): icmp_seq=1 ttl=64 time=0.173 ms
    64 bytes from dhcp1.example.com (10.0.2.4): icmp_seq=2 ttl=64 time=0.272 ms
    64 bytes from dhcp1.example.com (10.0.2.4): icmp_seq=3 ttl=64 time=0.237 ms

Make sure these systems are running.

Forward Lookup Testing

.. code-block:: bash

    ping -c 2 dhcp1
    ping -c 2 ns1
    ping -c 2 id1
    ping -c 2 google.com

Reverse Lookup Testing

.. code-block:: bash

    host 10.0.2.5
    host 10.0.2.4
    host 10.0.2.6
    dig -x 10.0.2.5
    dig -x 10.0.2.4
    dig -x 10.0.2.6
    dig +noall +answer -x 10.0.2.5
    dig +noall +answer -x 10.0.2.4
    dig +noall +answer -x 10.0.2.6



**Output from my terminal**

.. code-block:: bash

    [student@centos-client ~]$ host 10.0.2.5
    5.2.0.10.in-addr.arpa domain name pointer ns1.example.com.
    [student@centos-client ~]$ host 10.0.2.4
    4.2.0.10.in-addr.arpa domain name pointer dhcp1.example.com.
    [student@centos-client ~]$ host 10.0.2.6
    6.2.0.10.in-addr.arpa domain name pointer id1.example.com.




Other / Temporarily
------------------------------

Disable firewalld
.. code-block:: bash
    
    sudo -i
    systemctl stop firewalld
    systemctl disable firewalld
