DHCP Kea - Quick Setup
================================
System Prep: Let's get the system ready to provide DHCP v4 services.

Internet Access
------------------

Make sure system can get out to the internet. Your hypervisor of choice (e.g. VirtualBox) should be setup to temporarily provide networking services such as DHCP while we setup all of the services.

.. warning::
    Make sure you don't toggle the underlying systems's NIC to a different NIC/network. It will cause problems with VMs NAT/internet access.

Replace 'eth01' with your connection name.

.. code-block:: bash

    nmcli con show
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

    systemctl enable kea-dhcp4

Second

.. code-block:: bash

    systemctl start kea-dhcp4

Third

.. code-block:: bash

    systemctl status kea-dhcp4


Review and Test DHCP Service Config Files
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

.. tip:: 

   You can also test the configuration and review the output :ref:`info <figure1>` and check out the ports :ref:`ports <figure2>`

Just as a pre-caution let's :ref:`test <figure2>` the configuration file.