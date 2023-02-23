DHCP Appendix
===============

Reference Commands
---------------------------------------------

Figure 1: 

DHCP client runs on port 68, and the server runs on 67

.. code-block:: bash

    ss -tulnw

.. _dhcpfigure2:

Figure 2: 

:code:`kea-dhcp4 -t /etc/kea/kea-dhcp4.conf`` is a command that runs the KEA DHCPv4 server in a "test" mode with a specified configuration file. Running kea-dhcp4 -t /etc/kea/kea-dhcp4.conf checks the configuration file for errors and reports any issues and INFO.

.. code-block:: bash

    kea-dhcp4 -t /etc/kea/kea-dhcp4.conf