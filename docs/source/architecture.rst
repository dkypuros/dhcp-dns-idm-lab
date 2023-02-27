
.. _Section1:

Architectural Overview
============================

Main LAN environment (Travel Router)
------------------------------------------

* Office LAN: 192.168.0.0/24
* Host IP via DHCP: 192.168.0.102

Each system

.. _networkinfo:

Network Services (systems)
---------------------------------------------
These are the **primary systems** used to build the environment.

**Network Info**

* Domain: example.com
* IP Network: 10.0.2.0/24
* Gateway: 10.0.2.1

**DHCP Server**

* dhcp1.example.com
* 10.0.2.4

**DNS Server** 

* ns1.example.com
* 10.0.2.5

**Directory Server (IdM)**

* id1.example.com
* 10.0.2.X (TBD)

.. _testworkstation:

Test Workstation (systems)
---------------------------------------------
These are test systems we'll use for DHCP, DNS and IdM.

**CentOS**

* centos-client.example.com
* 10.0.2.X