Adding DDNS
============

Re-install DHCP Kea to include DDNS
-------------------------------------

On the DHCP server we'll run :code:`DHCP and DDNS` services together.

DHCP Kea Server 

**dhcp1**

.. code-block:: bash

    sudo -i
    systemctl stop kea-dhcp4
    systemctl status kea-dhcp4 

.. code-block:: bash

    dnf remove isc-kea -y

.. code-block:: bash

    dnf update -y
    
.. code-block:: bash

    curl -1sLf \
    'https://dl.cloudsmith.io/public/isc/kea-1-9/setup.rpm.sh' \
    | sudo -E bash

This covers :code:`DHCPv4, DHCPv6 and DDNS servers` More Info_ under "Open Source Package Names"

.. _Info: https://kb.isc.org/docs/isc-kea-packages

.. code-block:: bash

    sudo dnf install isc-kea -y 

.. code-block:: bash

    rpm -qa | grep kea

.. code-block:: bash

    systemctl status kea-dhcp4

.. code-block:: bash

    systemctl status kea-dhcp-ddns



DNS BIND9 Server 

**ns1**

.. code-block:: bash

    sudo -i
    dnf update -y


On the DHCP Kea Server - DDNS
----------------------------------

**dhcp1**

.. code-block:: bash

    vim /etc/kea/kea-dhcp4.conf
    :set syntax=json 

:code:`kea-dhcp4.conf` file for DDNS installation. 

.. code-block:: bash

    {
        "Dhcp4": { 
            "dhcp-ddns": {
                "enable-updates": true,
                "server-ip": "127.0.0.1",
                "server-port": 53001,
                "sender-ip": "",
                "sender-port": 0,
                "max-queue-size": 1024,
                "ncr-protocol": "UDP",
                "ncr-format": "JSON"
                },

            "ddns-send-updates": true,
            "ddns-override-no-update": true,
            "ddns-override-client-update": true,
            // Using an alias system to refer to hosts.
            // pro-10-0-2-181.example.com in this scenario.
            "ddns-replace-client-name": "when-present",
            "ddns-generated-prefix": "pro",
            "ddns-qualifying-suffix": "example.com.",
            "hostname-char-set": "[^A-Za-z0-9.-]",
            "hostname-char-replacement": "x",				


            "interfaces-config": {
                    "interfaces": [ "enp0s3" ],
                    "dhcp-socket-type": "raw"
                },
                "valid-lifetime": 180,
                "renew-timer": 60,
                "rebind-timer": 120,
            "lease-database": 
                { 
                "type": "memfile",
                "lfc-interval": 3600,
                "name": "/var/lib/kea/dhcp4.csv"
                },
            
                "subnet4": [
                {
                "subnet": "10.0.2.0/24",  
                "pools": [ { "pool": "10.0.2.181-10.0.2.200" } ],
                "option-data": [
                    {
                    "name": "routers",
                    "data": "10.0.2.1"},
                
                    {		
                    "name": "domain-name-servers",
                    "data": "10.0.2.5"},

                    {
                    "name": "domain-search",
                    "data": "example.com"
                    },
                    {
                    "name": "domain-name",
                    "data": "example.com"
                    }
                    ]
                }
                    ]
            }
                

        }


.. code-block:: bash

    vim /etc/kea/kea-dhcp-ddns.conf
    :set syntax=json 

:code:`kea-dhcp4-ddns.conf` file for DDNS installation. 

.. code-block:: bash

    {
        "DhcpDdns": {
            "ip-address": "127.0.0.1",
            "port": 53001,
            "dns-server-timeout": 100,
            "ncr-protocol": "UDP",
            "ncr-format": "JSON",
            "tsig-keys": [],
            "forward-ddns": {
                "ddns-domains": [
                    {
                        "name": "example.com.",
                        "key-name": "",
                        "dns-servers": [
                            {
                                "hostname": "",
                                "ip-address": "10.0.2.5",
                                "port": 53
                            }
                        ]
                    } // Missing closing brace here
                ]
            },
            "reverse-ddns": {
                "ddns-domains": [
                    {
                        "name": "2.0.10.in-addr.arpa.",
                        "key-name": "",
                        "dns-servers": [
                            {
                                "ip-address": "10.0.2.5",
                                "port": 53
                            }
                        ]
                    }
                ]
            }
        }
    }


.. code-block:: bash

    kea-dhcp4 -t /etc/kea/kea-dhcp4.conf

.. warning:: 
    Wrong tester. I'm not sure how to test :code:`kea-dhcp-ddns.conf` on CentOS. Debian uses "kea-dhcp4-ddns -t kea-dhcp-ddns.conf"

**Issue here** 

.. code-block:: bash

    kea-dhcp4 -t /etc/kea/kea-dhcp-ddns.conf


On the DNS Server - DDNS
--------------------------

.. code-block:: bash

    vim /etc/named.conf

Update BIND9 Config to :code:`allow-update` from DHCP server. 

.. tip:: 
    
    Keep all other zone files the same

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
        allow-update { 10.0.2.4; };
        };

    zone "2.0.10.in-addr.arpa"
        {
        type master;
        file "/etc/named/zones/db.2.0.10";
        allow-update { 10.0.2.4; };        
        };

Lower SElinux for non-production lab use case.

.. code-block:: bash

    setenforce 0
    getenforce

.. code-block:: bash

    systemctl start named

Start Both systems
--------------------

Fire Up all the Services (DHCP/DNS)

- Make sure both servers are up and running with no errors.
- Start / Restart DHCP kea Service
- Start / Restart DNS BIND9 Service

Test: DHCP-DDNS Server
--------------------------

- Do a simple status test on both servers.

Test: Client(s)
---------------

.. tip::

    The previous test system :code:`centos-client` already had a DHCP reservation, however we re-built the DHCP server and did not inlude any reservations.

- verify dhcp settings for :code:`centos-client`. If necessary reset DHCP client.

.. code-block:: bash

    dhclient -r
    dhclient

- reboot :code:`centos-client` system after verifying.
- login to :code:`centos-client` and check network.

.. code-block:: bash

    nmcli

- Check the status of the dhcp (DDNS)

.. code-block:: bash

    systemctl status kea-dhcp4

.. warning::

    You will get an error  :code:`DHCP/DDNS forward add rejected`. I.e. DHCP/DDNS service can't add the record to DN/BIND9. DNS/BIND9 will reject the request (to add the mapping) for :code:`pro-10-0-2-182.example.com` because of a file permissions issue on DNS/BIND9 zone files. Research RCODES with RFC2136 (error condition 0-8). e.g. RCODE 2 is an 'internal error' such as OS.

Verify the permissions in :code:`/etc/named/zones/`. If there aren't sufficient permission add them accordingly, and restart services if needed. Review status of service, and renew/release DHCP IP.

.. code-block:: bash

    cd /etc/named/zones/
    ls -la 
    chmod 777 * 
    cd ..
    chmod zones
    ls -la

You should now see :code:`.jnl` files in the zones directory.

Review DNS/BIND9 to see zones added
---------------------------------------

.. tip::

    You may need to allow time for the :code:`client-centos` system to refresh according to the timers set. If your in a hurry you can restart BIND9 service.

There should be additions to the file, including :code:`pro-10-0-2-182  A  10.0.2.182   `

.. code-block:: bash

    vim /etc/named/zones/db.example.com

.. code-block:: bash

    cat /var/lib/kea/kea-leases4.csv
    cat /var/lib/kea/dhcp4.csv

.. tip::

    If you look at :code:`client-centos` system hostname, it will only show you locally what the hostname is. You can't ping that hostname from another system. You have to use what was dynamically added to DNS.

**Final Client Test**

Many hosting companies will park a domain using DDNS. Ping "serverbash.com" and you might get :code:`ip-184-168-131-241.ip.secure.net` for example. Domain vs the hostname.

.. code-block:: bash

    ping pro-10.0.2.181.example.com

Troubleshooting Tips
------------------------

- SElinux
- Firewalld
- file permissions
- review all of your config files
- make sure status of services are green
- compare zone files to :code:`systemctl status XYZ` services.
