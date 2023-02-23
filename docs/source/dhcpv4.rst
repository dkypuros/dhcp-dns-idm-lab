DHCP v4 KEA
================================

System Prep
---------------------
Let's get the system ready to provide DHCP v4 services.

Internet Access
++++++++++++++++++

Make sure system can get out to the internet.

Replace 'eth01' with your connection name.

.. code-block:: bash

    nmcli con show
    nmcli con mod eth01 ipv4.method auto

Install EPEL on Centos 8
++++++++++++++++++++++++++

.. code-block:: bash

    sudo yum install epel-release


Locate Official ISC Packages for Kea
++++++++++++++++++++++++++++++++++++++

`Using Official ISC Packages for Kea Overview <https://kb.isc.org/docs/isc-kea-packages>`_

While RHEL, CentOS, Fedora, Debian, Ubuntu, and Alpine all have a binaries package of some sort for Kea, they may be different than ISC's packages. To avoid confusion, we'll download from ISC.

All ISC binary packages for Kea are contained in our repositories on Cloudsmith. Find the section with the title **Setting Up Repos on Fedora/CentOS/RHEL**

This will install the DHCP Kea v4 repository on the system.

.. code-block:: bash

    curl -1sLf \
  'https://dl.cloudsmith.io/public/isc/kea-2-2/setup.rpm.sh' \
  | sudo -E bash

Install DHCP v4 Kea
======================

  With all of the prep work out of the way, no install **isc-kea**

.. code-block:: bash

    sudo yum install isc-kea

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


Locate Kea Service Configuration Files
----------------------------------------

Let's query information about an installed package.

The "q" option in the command below stands for "query" and is used to display information about an installed package. When used with the "rpm" command, it will display information about the specified package "isc-kea".

The "c" option stands for "list configuration files" and is used to display a list of configuration files included in the specified package. When used with the "rpm" command, it will display a list of configuration files included in the package.

When you run the "rpm -qc" command, it will display a list of configuration files included in the specified package

.. code-block:: bash

    rpm -qc isc-kea-2.2.0

The output show the location of the DHCP Configuration file is here: :code:`/etc/kea/kea-dhcp4.conf`

Let's stop the service and explore the file

.. code-block:: bash

    systemctl stop kea-dhcp4

.. tip:: 

   You can also test the configuration and review the INFO. .. :ref:`DHCP INFO <dhcpfigure2>`

.. code-block:: bash

    vim /etc/kea/kea-dhcp4.conf