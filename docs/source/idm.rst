IdM - Foundational Setup
=============================================

DS maintains user accounts and other objects / resources. Common LDAP services are FreeIPA, 389-DS, Apache Directory, and OpenLDAP. These are all based on Lightweight Directory Access Protocol. 

Default ports are 389 and 636 (secure LDAP). IPA stands-for Identity Policy and Audit (IPA).

Networking on id1 Server
---------------------------

**Set Networking**

If not already done.

.. code-block:: bash

    nmcli


.. code-block:: bash

    nmcli con mod enp0s3 ipv4.address 10.0.2.6/24 ipv4.gateway 10.0.2.1 ipv4.dns 10.0.2.5 ipv4.method manual

.. code-block:: bash

    nmcli con mod enp0s3 ipv4.dns-search "example.com"

**hostname**

.. code-block:: bash

    hostnamectl set-hostname id1.example.com

**hosts file**

Normally for clients you would leave this as is, but for servers add a static statement as a new line using vim. Add :code:`10.0.2.6 <tab> id1.example.com <tab> id1`

.. code-block:: bash

    vim /etc/hosts

.. code-block:: bash

    sudo -i
    reboot

**Verify changes**

.. code-block:: bash

    nmcli
    ip route

**Final Network test**

.. code-block:: bash

    ping ns1
    ping id1
    ping google.com

Install IdM
-----------------

.. code-block:: bash

    sudo -i

**Enable IdM Stream**

This will enable 389-ds, httpd, idm, pki-core, pki-deps. :code:`DL1` is the version.

.. code-block:: bash

    dnf module enable idm:DL1 -y


.. code-block:: bash

    dnf install ipa-server -y


.. code-block:: bash

    reboot


IdM Server Setup
--------------------

**Setup process**

- Stand-alone CA
- NTP client 
- DS
- Kerberos (Key Disti)
- Apache httpd
- KDC to enable PKINIT

.. code-block:: bash

    ipa-server-install

Quesions / Answers

- BIND = no
- server = id1.example.com
- domain = example.com
- realm = EXAMPLE.COM
- Direcetory Mangager pass = redhatredhat
- IPA admin = redhatredhat
- ntp = no

**Copy DNS records**

.. code-block:: bash

    cd /tmp
    ls -l

**Look for a file like**

:code:`ipa.system.records._5vphkpi.db`

Grab the rules, and add them to DNS/BIND9

.. code-block:: bash

    cat /tmp/ipa.system.records._5vphkpi.db

Add records on the DNS/BIND9 server. Add to the bottom of the file. Count the records to be sure.

.. code-block:: bash

    vim /etc/named/zones/db.example.com

On DNS/BIND9 Server

.. code-block:: bash

    sudo -i
    systemctl restart named

**Shortcut - Disable Firealld - id1**

This is only a test environment..

.. code-block:: bash

    systemctl disable firewalld


**Backup CA Cert - id1**

.. code-block:: bash

    cd /root
    ls


.. code-block:: bash

    mkdir ~/backup


.. code-block:: bash

    cp *.p12 ~/backup


.. code-block:: bash

    reboot


.. code-block:: bash

    X

