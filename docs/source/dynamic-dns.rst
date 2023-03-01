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


Back to DHCP Kea Server 

**dhcp1**

.. code-block:: bash

    vim /etc/kea/kea-dhcp4.conf

Kea DHCPv.conf file we want to use. Here is the original file that comes with CentOS 8 when you install ISC-DHCP (incl. DDNS) default-config_

.. _default-config: https://raw.githubusercontent.com/dkypuros/dhcp-dns-idm-lab/main/docs/source/raw-output/dhcp-ddns-original.txt

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
        "ddns-replace-client-name": "when-present",
        "ddns-generated-prefix": "test",
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

    x

.. code-block:: bash

    x

.. code-block:: bash

    x

.. code-block:: bash

    x

.. code-block:: bash

    x

