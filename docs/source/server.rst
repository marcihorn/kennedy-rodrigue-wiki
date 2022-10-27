######
Server
######

.. _map_network_drive:

Map Network Directory ("shared" drive)
-----------------

For Mac users, go to ``Finder`` > ``Go`` > ``Connect to Server``, enter the following and press ``Connect``:

.. code::

    smb://cvlkrfs/shared


For Windows users, follow the instructions `here <https://atlas.utdallas.edu/TDClient/30/Portal/KB/ArticleDet?ID=51>`_ and use the following for ``Folder`` field:

.. code::

    \\cvlkrfs\shared

.. _login:

Login with Command Line
-----

For Max users, use the "Terminal" program built into Mac

For Windows users, install either `Putty <https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html>`_ or `MobaXterm <https://mobaxterm.mobatek.net/download-home-edition.html>`_

.. code:: bash

    ssh -XY <user>@<host>

User is your UTD Net ID and host can be one of the following:

* ``cvlkrcompute1.utdallas.edu`` or ``ponyo.utdallas.edu``
* ``cvlkrcompute2.utdallas.edu`` or ``totoro.utdallas.edu``

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

