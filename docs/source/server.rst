######
Server
######

To access our server, make sure that you are connected to the UTD network:

* If you're on-campus, make sure that you are connected to the CometNet internet
* If you're working off-campus, make sure that you are connected to the UTD VPN by following the instructions `here <https://atlas.utdallas.edu/TDClient/30/Portal/Requests/ServiceDet?ID=167>`_)

.. _map_network_drive:

Map Network Directory
-----------------

To map the ``kenrod/`` drive (formerly, the ``shared/`` drive) to your local computer:

For Mac users, go to ``Finder`` > ``Go`` > ``Connect to Server``, enter the following and press ``Connect``:

.. code::

    smb://smb.cvl.utdallas.edu/kenrod


For Windows users, follow the instructions `here <https://atlas.utdallas.edu/TDClient/30/Portal/KB/ArticleDet?ID=51>`_ and enter the following for ``Folder`` field:

.. code::

    \\smb.cvl.utdallas.edu\kenrod

.. _login:

Login with Command Line
-----

For Max users, use can use the ``Terminal`` app, which is already included in Macs

For Windows users, install either `Putty <https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html>`_ or `MobaXterm <https://mobaxterm.mobatek.net/download-home-edition.html>`_

You can login by typing the following:

.. code:: bash

    ssh -Y <user>@<hostname>

User is your UTD Net ID and hostname can be one of the following:

* ``cvlkrcompute1.utdallas.edu`` or ``ponyo.utdallas.edu``
* ``cvlkrcompute2.utdallas.edu`` or ``totoro.utdallas.edu``
* ``cortex.cvl.utdallas.edu``

.. _software:

Software
--------

To see available software, run:

.. code:: bash

    module avail

To load available software, run:

.. code:: bash

    module load <software>

To unload software, run:

.. code:: bash

    module unload <software>

