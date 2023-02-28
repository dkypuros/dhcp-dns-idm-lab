DHCP Kea - Complete Setup
================================
System Prep: Let's get the system ready to provide DHCP v4 services.

Internet Access
------------------

Make sure system can get out to the internet. Your hypervisor of choice (e.g. VirtualBox) should be setup to temporarily provide networking services such as DHCP while we setup all of the services.

.. warning::
    Make sure you don't toggle the underlying systems's NIC to a different NIC/network. It will cause problems with VMs NAT/internet access.

Replace 'eth01' with your connection name. When this value is set to auto for :code:`ipv4.method: auto` that means DHCP.

.. code-block:: bash

    nmcli con show
    nmcli con show enp0s3
    nmcli con mod eth01 ipv4.method auto

Install EPEL on Centos 8
----------------------------

.. code-block:: bash

    sudo dnf install epel-release


Locate Official ISC Packages for Kea
-----------------------------------------

`Using Official ISC Packages for Kea Overview <https://kb.isc.org/docs/isc-kea-packages>`_

While RHEL, CentOS, Fedora, Debian, Ubuntu, and Alpine all have a binaries package of some sort for Kea, they may be different than ISC's packages. To avoid confusion, we'll download from ISC.

All ISC binary packages for Kea are contained in our repositories on Cloudsmith. Find the section with the title **Setting Up Repos on Fedora/CentOS/RHEL**

This will install the **DHCP Kea v4 repo** on the system**.

.. code-block:: bash

    curl -1sLf \
  'https://dl.cloudsmith.io/public/isc/kea-2-2/setup.rpm.sh' \
  | sudo -E bash


Install DHCP v4 Kea
-----------------------
  With all of the prep work out of the way, now install **isc-kea** using the repo above.

.. code-block:: bash

    sudo dnf install isc-kea

Start, and Enable Kea Service
------------------------------

First

.. code-block:: bash

    su -
    systemctl enable kea-dhcp4

Second

.. code-block:: bash

    systemctl start kea-dhcp4

Third

.. code-block:: bash

    systemctl status kea-dhcp4


Test: DHCP Config File
--------------------------------------------

Let's query information about an installed package.

The "q" option in the command below stands for "query" and is used to display information about an installed package. When used with the "rpm" command, it will display information about the specified package "isc-kea".

The "c" option stands for "list configuration files" and is used to display a list of configuration files included in the specified package. When used with the "rpm" command, it will display a list of configuration files included in the package.

When you run the "rpm -qc" command, it will display a list of configuration files included in the specified package

.. code-block:: bash

    rpm -qc isc-kea-2.2.0

The output show the location of the DHCP Configuration file is here: :code:`/etc/kea/kea-dhcp4.conf`

Let's :ref:`stop <figure3>` the service and peek at the file with vim.

.. code-block:: bash

    vim /etc/kea/kea-dhcp4.conf

You can also view the raw config file here on GitHub_

.. _GitHub: https://raw.githubusercontent.com/dkypuros/dhcp-dns-idm-lab/main/docs/source/raw-output/dhcp-config.txt

Review: DHCP Config Test Results
-----------------------------------------

.. tip:: 

   You can also test the configuration and review the output :ref:`info <figure2>` and check out the ports :ref:`ports <figure1>`


- Just as a pre-caution let's test the configuration file ( :ref:`Instructions here <figure2>` ). 

- Let's take a peak out my sample output here: DHCP_Test_Config_.
    
.. _DHCP_Test_Config: https://raw.githubusercontent.com/dkypuros/dhcp-dns-idm-lab/main/docs/source/raw-output/dhcp-test-config.txt

- One last thing. We can compare both the DHCP Config to the INFO presented from the configuration test.

Review: Compare Config to Test
----------------------------------

- Config Test INFO on dhcp-test-config.txt_ 
    
.. _dhcp-test-config.txt: https://raw.githubusercontent.com/dkypuros/dhcp-dns-idm-lab/main/docs/source/raw-output/dhcp-test-config.txt

- kea-dhcp4.conf on dhcp-config.txt_

.. _dhcp-config.txt: https://raw.githubusercontent.com/dkypuros/dhcp-dns-idm-lab/main/docs/source/raw-output/dhcp-config.txt

- Quick Compare "192.0.2.0/24" (Use CTRL-F on both documents)

Review: Start DHCP & View Journal
----------------------------------

- Start the :ref:`service<figure3>`
- View the :ref:`journal<figure5>`

Checkout my output DHCP-Journal_

.. _DHCP-Journal: https://raw.githubusercontent.com/dkypuros/dhcp-dns-idm-lab/main/docs/source/raw-output/dhcp-service.txt


Review: DHCP ports
-------------------------

- Use instructions :ref:`here <figure1>` to test DHCP ports.
- My output from running relevant commands SS_

.. _SS: https://github.com/dkypuros/dhcp-dns-idm-lab/blob/main/docs/source/raw-output/port-scan.txt

.. danger::
    I've noticed when my VirtualBox is behind a corporate firewall a virtual bridge is automatically created on the NAT interface.

Backup & Copy in kea-dhcp4.conf
-----------------------

.. code-block:: bash

    mv /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.bak

.. code-block:: bash
    
    sudo -i
    touch /etc/kea/kea-dhcp4.conf

Here is Part 1 DHCP Config. kea-dhcp4.conf_ It includes an initial config (}simple) to get us started with the service. Paste this raw text into the :code:`kea-dhcp4.conf` file.

You can open the file with vim and set the syntax as JSON.

.. code-block:: bash

    vim /etc/kea/kea-dhcp4.conf

Inside of vim. Highlight values for variables in a unique color. 

.. code-block:: bash

    :set syntax=json

.. tip::

    Change "interfaces" to match actual system NIC. :code:`nmcli con show` or :code:`ip address`

The :code:`Dhcp4` at the top of this JSON config, is the main JSON object. Here is a list of DHCP options_ for the JSON :code:`option array`. We're temporarily using the DNS provided from hypervisor layer (VirtualBox). We'll come back and change this.

.. _options: https://www.iana.org/assignments/bootp-dhcp-parameters/bootp-dhcp-parameters.xhtml

.. code-block:: json

        {
        "Dhcp4": { 
            "interfaces-config": {
                "interfaces": [ "enp0s3" ],
                "dhcp-socket-type": "raw"
            },
            "valid-lifetime": 3600,
            "renew-timer": 900,
            "rebind-timer": 1800,
        "lease-database": 
            { 
            "type": "memfile",
            "lfc-interval": 3600,
            "name": "/var/lib/kea/dhcp4.csv"
            },
        
            "subnet4": [
            {
            "subnet": "10.0.2.0/24",  
            "pools": [ { "pool": "10.0.2.101-10.0.2.200" } ],
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
                }
                ],
            "reservations": [
                        {
                        "hw-address": "08:00:27:84:b3:c8",
                        "ip-address": "10.0.2.7",
                        "hostname": "centos-client.example.com"
                        }
                ]
            }
            ]	

        }
    }


Start the service and check the status

:ref:`Start/Status <figure3>`

The kea-dhcp4.service should show :code:`Active: active (running)`.

.. code-block:: bash

    kea-dhcp4.service - Kea DHCPv4 Server
   Loaded: loaded (/usr/lib/systemd/system/kea-dhcp4.service; enabled; vendor preset: disabled)
   Active: active (running) since Sat 2023-02-25 13:26:55 CST; 1 day 19h ago
     Docs: man:kea-dhcp4(8)
 Main PID: 33802 (kea-dhcp4)
    Tasks: 1 (limit: 11016)
   Memory: 4.7M
   CGroup: /system.slice/kea-dhcp4.service
           └─33802 /usr/sbin/kea-dhcp4 -c /etc/kea/kea-dhcp4.conf

Disable DHCP VirtualBox
--------------------------

- VirtualBox Tools > Network > NAT Networks > uncheck :code:`Enable DHCP`
- select apply
- close all VMs and reboot VirtualBox

Connect CentOS 8 Client to DHCPv4
-------------------------------------------

.. warning:: 
    I ran into a problem here. I had a working version of the :code:`kea-dhcp4.conf` file. It had the wrong IPs in there from a training series I was following. I updated the config on the documentation site (here), but forgot to update the DHCP server and restart the services. Along the way I disabled SELinux and the firewall on the DHCP server.

- Althought we're using the DHCP server, we have a :code:`reservation` option listed in the config to assign "centos-client.example.com" the IP address :code:`10.0.2.7`
- Let's boot-up the CentOS 8 system and see if it connects to our DHCP server.

**Success!**

Centos client system grabs the correct IP from DHCP reservation. :code:`10.0.2.7/24`

.. code-block:: bash

    net 10.0.2.7/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3


Review: DHCP Leases & Reservations
-------------------------

.. code-block:: bash
    
    cat /var/lib/kea/kea-leases4.csv

.. code-block:: bash

    cat /var/lib/kea/dhcp4.csv

Output shows our Centos 8 client machine receiving the :code:`10.0.2.7` IP.

.. code-block:: bash

    address,hwaddr,client_id,valid_lifetime,expire,subnet_id,fqdn_fwd,fqdn_rev,hostname,state,user_context
    10.0.2.101,08:00:27:36:62:8e,01:08:00:27:36:62:8e,3600,1677528810,1,0,0,id1,0,
    10.0.2.7,08:00:27:84:b3:c8,01:08:00:27:84:b3:c8,3600,1677529050,1,0,0,centos-client.example.com,0,
    10.0.2.101,08:00:27:36:62:8e,01:08:00:27:36:62:8e,3600,1677529710,1,0,0,id1,0,
