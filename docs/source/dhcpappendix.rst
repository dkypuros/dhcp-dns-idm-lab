DHCP Appendix
===============

Reference Commands
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