Appendix
===================

General
----------------

.. _generalfigure1:

Figure 1: 

Cockpit Install

.. code-block:: bash

    sudo dnf install cockpit -y

.. code-block:: bash

    systemctl start cockpit
    systemctl status cockpit

Test in Browswer

.. code-block:: bash

    localhost:9090



DNS BIND 9
-----------------

.. _dnsfigure1:

Figure 1: 

DNS example

.. code-blaock:: bash

    hello world test


DHCP Kea [Appendix]
---------------------------------------------

.. _figure1:

Figure 1: 

DHCP client runs on port 68, and the server runs on 67

.. code-block:: bash

    ss -tulnw


.. _figure2:

Figure 2: 

:code:`kea-dhcp4 -t /etc/kea/kea-dhcp4.conf`` is a command that runs the KEA DHCPv4 server in a "test" mode with a specified configuration file. Running kea-dhcp4 -t /etc/kea/kea-dhcp4.conf checks the configuration file for errors and reports any issues and INFO.

.. code-block:: bash

    kea-dhcp4 -t /etc/kea/kea-dhcp4.conf


.. _figure3:

Figure 3: 

Enable, Start, Stop dhcp version 4 service

.. code-block:: bash

    systemctl enable kea-dhcp4

.. code-block:: bash

    systemctl start kea-dhcp4

.. code-block:: bash

    systemctl stop kea-dhcp4

.. code-block:: bash

    systemctl restart kea-dhcp4

.. _figure4:

Figure 4: 

View DHCP4 service journal

.. code-block:: bash

    journalctl -u kea-dhcp4.service

.. _figure5:

Figure 5: 

.. code-block:: bash

    journalctl -u kea-dhcp4.service

.. _figure6:

Figure 6:

.. code-block:: bash

    cd /var/lib/kea
    ls
    cat kea-leases4.csv

.. _figure7:

Figure 7:

**Kernel Errors with VirtualBox**

.. warning::

   If you encounter Kernel driver not installed (rc= -1908) or "If your system is using EFI Secure Boot you may need to sign the kernel modules (vboxdrv, vboxnetflt, vboxnetadp, vboxpci) before you can load them." make sure do disable secure boot, and make sure to  :code:`sudo dnf install make time perl gcc dkms kernel-devel kernel-headers` and also :code:`/sbin/vboxconfig`

Trying

.. code-block:: bash

   dnf -y install @development-tools
   dnf -y install kernel-headers kernel-devel dkms elfutils-libelf-devel qt5-qtx11extras


https://tecadmin.net/install-oracle-virtualbox-on-fedora/


.. _figure8:

Figure 8:

Release the DHCP address on the VM.

.. code-block:: bash

    dhclient -r