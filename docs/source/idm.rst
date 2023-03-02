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

.. warning::

    Less than the minimum 1.2GB of RAM. I changed VirtualBox to "8192 MB"

.. code-block:: bash

    ipa-server-install

Quesions / Answers

- BIND = no
- server = id1.example.com
- domain = example.com
- realm = EXAMPLE.COM
- Direcetory Mangager pass = redhatredhat
- IPA admin = redhatredhat
- NetBIOS domain name = EXAMPLE
- ntp = no

**Example Output**

.. code-block:: bash

    The IPA Master Server will be configured with:
    Hostname:       id1.example.com
    IP address(es): 10.0.2.6
    Domain name:    example.com
    Realm name:     EXAMPLE.COM

    The CA will be configured with:
    Subject DN:   CN=Certificate Authority,O=EXAMPLE.COM
    Subject base: O=EXAMPLE.COM
    Chaining:     self-signed

    Continue to configure the system with these values? [no]: yes

    The following operations may take some minutes to complete.
    Please wait until the prompt is returned.

    Disabled p11-kit-proxy
    Synchronizing time
    No SRV records of NTP servers found and no NTP server or pool address was provided.
    Using default chrony configuration.
    Attempting to sync time with chronyc.
    Time synchronization was successful.
    Configuring directory server (dirsrv). Estimated time: 30 seconds
    [1/42]: creating directory server instance
    Validate installation settings ...
    Create file system structures ...
    Perform SELinux labeling ...
    Create database backend: dc=example,dc=com ...
    Perform post-installation tasks ...
    [2/42]: tune ldbm plugin
    [3/42]: adding default schema
    [4/42]: enabling memberof plugin
    [5/42]: enabling winsync plugin
    [6/42]: configure password logging
    [7/42]: configuring replication version plugin
    [8/42]: enabling IPA enrollment plugin
    [9/42]: configuring uniqueness plugin
    [10/42]: configuring uuid plugin
    [11/42]: configuring modrdn plugin
    [12/42]: configuring DNS plugin
    [13/42]: enabling entryUSN plugin
    [14/42]: configuring lockout plugin
    [15/42]: configuring graceperiod plugin
    [16/42]: configuring topology plugin
    [17/42]: creating indices


**Example Output - Bottom portion**

.. code-block:: bash

    Could not remove /tmp/tmpci4ntaae.ipabkp
    The ipa-client-install command was successful

    Invalid IP address fe80::a00:27ff:fe36:628e for id1.example.com.: cannot use link-local IP address fe80::a00:27ff:fe36:628e
    Invalid IP address fe80::a00:27ff:fe36:628e for id1.example.com.: cannot use link-local IP address fe80::a00:27ff:fe36:628e
    Please add records in this file to your DNS system: /tmp/ipa.system.records.ikxludtr.db
    ==============================================================================
    Setup complete

    Next steps:
        1. You must make sure these network ports are open:
            TCP Ports:
            * 80, 443: HTTP/HTTPS
            * 389, 636: LDAP/LDAPS
            * 88, 464: kerberos
            UDP Ports:
            * 88, 464: kerberos
            * 123: ntp

        2. You can now obtain a kerberos ticket using the command: 'kinit admin'
        This ticket will allow you to use the IPA tools (e.g., ipa user-add)
        and the web user interface.

    Be sure to back up the CA certificates stored in /root/cacert.p12
    These files are required to create replicas. The password for these
    files is the Directory Manager password
    The ipa-server-install command was successful



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


**Copy DNS records**

.. code-block:: bash

    cd /tmp
    ls -l

**Look for a file like**

:code:`ipa.system.records._5vphkpi.db`

Grab the rules, and add them to DNS/BIND9

.. code-block:: bash

    _kerberos-master._tcp.example.com. 3600 IN SRV 0 100 88 id1.example.com.
    _kerberos-master._udp.example.com. 3600 IN SRV 0 100 88 id1.example.com.
    _kerberos._tcp.example.com. 3600 IN SRV 0 100 88 id1.example.com.
    _kerberos._udp.example.com. 3600 IN SRV 0 100 88 id1.example.com.
    _kerberos.example.com. 3600 IN TXT "EXAMPLE.COM"
    _kerberos.example.com. 3600 IN URI 0 100 "krb5srv:m:tcp:id1.example.com."
    _kerberos.example.com. 3600 IN URI 0 100 "krb5srv:m:udp:id1.example.com."
    _kpasswd._tcp.example.com. 3600 IN SRV 0 100 464 id1.example.com.
    _kpasswd._udp.example.com. 3600 IN SRV 0 100 464 id1.example.com.
    _kpasswd.example.com. 3600 IN URI 0 100 "krb5srv:m:tcp:id1.example.com."
    _kpasswd.example.com. 3600 IN URI 0 100 "krb5srv:m:udp:id1.example.com."
    _ldap._tcp.example.com. 3600 IN SRV 0 100 389 id1.example.com.
    ipa-ca.example.com. 3600 IN A 10.0.2.6

.. code-block:: bash

    cat /tmp/ipa.system.records._5vphkpi.db

Add records on the DNS/BIND9 server. Add to the bottom of the file. Count the records to be sure.

.. code-block:: bash

    vim /etc/named/zones/db.example.com

On DNS/BIND9 Server

.. code-block:: bash

    sudo -i
    systemctl restart named

With so many changes, a reboot was actually required.

.. code-block:: bash

    sudo -i
    reboot

Test the IdM Server
----------------------------------

.. code-block:: bash

    ping ns1
    ping google.com

.. code-block:: bash

    systemctl status ipa

.. code-block:: bash

    systemctl status krb5kdc

.. code-block:: bash

    kinit admin

.. code-block:: bash

    systemctl status firewalld

.. code-block:: bash

    ipa user-add

.. code-block:: bash

    ipa user-add --password --homedir /home/david.kypuros  --shell=/bin/bash

.. code-block:: bash

    ipa user-add m.bolton --first=Michael --last=Bolton --email=m.bolton@example.com --homedir=/home/m.bolton 

.. code-block:: bash

    ipa user-del demo_user

.. code-block:: bash

    ipa user-find --login=m.bolton

.. code-block:: bash

    Browser ==> 10.0.2.6

.. code-block:: bash

    https://id1.example.com

.. code-block:: bash

    X

.. code-block:: bash

    X

