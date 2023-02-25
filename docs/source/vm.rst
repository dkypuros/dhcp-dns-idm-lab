Creating VMs on a Hypervisor
=======================================

Install VirtualBox on host
---------------------------------------------
Install Fedora 37 on a host system.

**Basic install of VirtualBox:**
Next install VirtualBox on top using the following URL VirtualBox_. Select Fedora 36 (highest version so far.) Install the rpm.

.. code-block:: bash

   cd ~/Downloads
   sudo rpm -i VirtualBox-7.0-7.0.6_155176_fedora36-1.x86_64.rpm

.. tip::
   If you run into Kernel issues, see diag tips here :ref:`Kernel Help <figure7>`

Prep VirtualBox Networking
---------------------------------
 Select "Tools" large horizontal bar > the hamburger menu > Network.

 Then the right pane should show a network view. Select the NAT network tab, and create. It should autocreate "**NatNetwork**" with :code:`10.0.2.0/24`



VirtualBox DHCP VM Details
---------------------------------

This is the file (ISO)

.. code-block:: bash

   CentOS-Stream-8-x86_64-20230119-dvd1.iso

This is the location I'm storing these large ISO's

.. code-block:: bash

   /run/media/marz/software/linux/CentOS-Stream-8-x86_64-20230119-dvd1.iso

**DHCP VM Info:**

- name: dhcp1
- VM location: /run/media/marz/userdata
- ISO location: /run/media/marz/software/linux/CentOS-Stream-8-x86_64-20230119-dvd1.iso
- 2 vCPUs
- 2048 MB of RAM
- 25 GB Storage [VDI/Pre-allocate Full Size]

Unattended Installation = No

Next **Click Start** or the big green arrow pointing right, to spin up the VM.

CentOS 8 Installation - DHCP System
--------------------------------------

Once you are inside the "CentOS 8" installation screen, use these settings:

**VM Network Info:**

- for network settings: choose NAT Network / NatNetwork. These should match previous step.

**VM User Info:**

- for users on the sytem: create user/pass of student/student and root/redhat. This is only for lab testing purposes.

**Complete Setup**

- Wait for install to complete, and agree to license etc. Login with student account, and make sure everything works. 

- next power-off the vm from inside the GUI.

.. tip::
   It takes ~30 min or so to do a manual install of CentOS 8. So to save time, we're going to clone the rest of the machines.

Rapid System Builds (Cloning) 
-----------------------------

To clone a virtual machine (VM) in Oracle VirtualBox, you can follow these steps:

#. Open VirtualBox and select the **DHCP1** VM from the left pane.
#. Click the "Clone" button in the toolbar or right-click on the VM and select "Clone".
#. In the "Clone Virtual Machine" window, enter **ns**  for the VM name and change the path location to store it to :code:`/run/media/marz/userdata`. That is my location to another large disk. You location may vary. Keep all other settings as default.
#. Choose "Full clone".
#. Click "Next" and review the settings one more time.
#. Click "Clone" to start the cloning process. It took my system ~2 min to clone. 

Start and login to the new **ns1** VM

.. code-block:: bash
   hostnamectl set-hostname ns1

Flap the NIC via the GUI. 

- Power button drop down (Top Right Corner) > Wired Connected > Wired settings
- Connected "Off" then "On"
- Check IP. Mine shows :code:`10.0.2.4` via DHCP.
- Finally shut the system down.

Repeat the steps
------------------
Use these same steps to build **IdM** with hostname **id1** and the **Test Workstation** with hostname **centos-client**. At the end you should hve 4 systems built in VirtualBox.

**Ending IP Addresses**

- dhcp1: :code:`10.0.2.4`
- ns1: :code:`10.0.2.6`
- id1: :code:`10.0.2.5`
- centos-client: :code:`10.0.2.7`

Final Snapshop Step
----------------------
Let's snapshot each of these VMs, to create a sort of "un-do button" for any further configuration oops down the road. This will give us a quick way to start over without needed to clone or re-install an OS.

- click the VM > hamburger menu next to the VM name > Snapshots > Take > "Initial Install"

Let's turn on all of the systems to make sure the IPs are in good working order. Spin up :ref:`Cockpit in browswer <generalfigure1>` to monitor research usage (System/Overview Dashboard)

Port forwarding for SSH
---------------------------

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

Click yes to continue and add the ssh key to "list of known hosts."

Repeat the Steps for all Systems
----------------------------------
Repeat the steps and make sure there is a rule for all of the systems:

- dhcp1
- ns1
- id1
- centos-client

Change the "host port" by an increment of 1, so you can ssh to all of the systems at the same time. The Guest port can remain the same, because the "guest port" is the port on the virtual machine that will receive the forwarded traffic from the "host port." It doesn't need to remain unique.



#URLs

.. _VirtualBox: https://www.virtualbox.org/wiki/Linux_Downloads