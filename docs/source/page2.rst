Creating VMs on a Hypervisor
=======================================

VirtualBox on RHEL 9
---------------------
I'll need to come back to these steps. I didn't document this installation, and I believe I ran into a few GOTCHA's.

Port forwarding
++++++++++++++++++

Once installed, setup "port forwarding". Click on the hamburger menu next to "tools". Select Network > NAT network. Double-click the "NATNetwork". The default network should be **10.0.2.0/24** for VirtualBox. Choose "Port forwarding" next to general options > ipv4. Click on the icon to "add" a port forwarding "rule". See example below on how to add a rule:

.. tip:: 

   There are other ways to tunnel into the VMs, but this approach is consistant across OS environments.

* **Name:** ns1 
* **Protocol:** TCP
* **Host IP:** 127.0.0.1
* **Host Port:** 2222
* **Guest IP:** 10.0.2.5
* **Guest Port:** 22

From the main "RHEL 9" system terminal, SSH into the VM, and enter your password.

.. code-block:: bash

   ssh student@127.0.0.1 -p 2222
