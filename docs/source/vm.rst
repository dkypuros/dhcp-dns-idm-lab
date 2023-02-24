Creating VMs on a Hypervisor
=======================================

VirtualBox on Fedora 37
---------------------
Install Fedora 37 on a host system. Next install VirtualBox on top using the following URL VirtualBox_. Select Fedora 36 (highest version so far.) Install the rpm.

.. code-block:: bash

   cd ~/Downloads
   sudo rpm -i VirtualBox-7.0-7.0.6_155176_fedora36-1.x86_64.rpm

Install 1st VM - Centos 8 Stream
--------------------------------------

This is the file (ISO)

.. code-block:: bash

   CentOS-Stream-8-x86_64-20230119-dvd1.iso

This is the location I'm storing these large ISO's

.. code-block:: bash

   /run/media/marz/software/linux/CentOS-Stream-8-x86_64-20230119-dvd1.iso

**VM Info:**

- name: dhcp1
- VM location: /run/media/marz/userdata
- ISO location: /run/media/marz/software/linux/CentOS-Stream-8-x86_64-20230119-dvd1.iso
- 2 vCPUs
- 2048 MB of RAM
- 8GB Storage [VDI/Pre-allocate Full Size]

Unattended Installation = No

Next **Click Start** or the big green arrow pointing right, to spin up the VM.

.. warning::

   If you encounter Kernel driver not installed (rc= -1908) or "If your system is using EFI Secure Boot you may need to sign the kernel modules (vboxdrv, vboxnetflt, vboxnetadp, vboxpci) before you can load them." make sure do disable secure boot, and make sure to  :code:`sudo dnf install make time perl gcc dkms kernel-devel kernel-headers` and also :code:`/sbin/vboxconfig`

Trying

.. code-block:: bash

   dnf -y install @development-tools
   dnf -y install kernel-headers kernel-devel dkms elfutils-libelf-devel qt5-qtx11extras


https://tecadmin.net/install-oracle-virtualbox-on-fedora/

Port forwarding
-------------------

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

#URLs

.. _VirtualBox: https://www.virtualbox.org/wiki/Linux_Downloads