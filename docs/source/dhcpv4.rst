DHCP Kea - Quick Setup
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


Perform: DHCP Config File Test
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

   You can also test the configuration and review the output :ref:`info <figure1>` and check out the ports :ref:`ports <figure2>`


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

- Use instructions :ref:`here <figure1>`to test DHCP ports.
- My output from running relevant commands SS_

.. _SS: https://github.com/dkypuros/dhcp-dns-idm-lab/blob/main/docs/source/raw-output/port-scan.txt

.. danger::
    I've noticed when my VirtualBox is behind a corporate firewall a virtual bridge is automatically created on the NAT interface.

Review: DHCP Leases
-------------------------

.. warning::
    I might need to come back to this step. I looked at my dhcp config file, and I didn't see any selection of a location to save the CSV / leases info yet. I need to update my config and review this later.

Steps I originally used: :ref:`Steps <figure6>`

More info I reviewed here: See_

.. _See: https://kea.readthedocs.io/en/kea-1.6.2/arm/dhcp4-srv.html
