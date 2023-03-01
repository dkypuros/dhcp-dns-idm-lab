DNS BIND 9 - Dynamic Setup
============================

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


Back to DHCP Kea Server - Configure DHCP fo DDNS
=====================================================

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


Back to DNS BIND9 Server - Configure DNS fo DDNS
=====================================================

.. code-block:: bash

    vim /etc/named.conf

Update BIND9 Config to :code:`allow-update` from DHCP server.

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

.. code-block:: bash

    x

.. code-block:: bash

    x

.. code-block:: bash

    x

.. code-block:: bash

    x

