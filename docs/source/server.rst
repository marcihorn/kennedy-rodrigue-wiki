######
Server
######

To access our server, make sure that you are connected to the UTD network:

* If you're on-campus, make sure that you are connected to the CometNet internet
* If you're working off-campus, make sure that you are connected to the UTD VPN by following the instructions `here <https://atlas.utdallas.edu/TDClient/30/Portal/Requests/ServiceDet?ID=167>`_

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

For Max users, we can use the ``Terminal`` app, which is already included in Macs

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

.. _parallel:

Parallel Jobs
--------

To run parallel jobs, we can use `Slurm (preferred) <https://slurm.schedmd.com/quickstart.html>`_ or `Sun Grid Engine (old) <http://star.mit.edu/cluster/docs/0.93.3/guides/sge.html>`_

To use ``Slurm``, first access the server via ``cvlkrcompute2.utdallas.edu`` or ``totoro.utdallas.edu``.

Basic Slurm commands:

Basic Slurm commands:

.. code:: bash
    
    sbatch # Submit a job
    squeue # Show see pending jobs
    scancel <jobid> or scancel -u <username> # Cancel a job

Script Template:

.. code:: bash

    # ------------------------------------------------------------------------------
    # slurm settings
    # ------------------------------------------------------------------------------
    #SBATCH --nodes=1
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=1
    #SBATCH- -mem=4G
    #SBATCH --partition=KRLab
    #SBATCH --output=jid-%A-%a_jname-%x.log
    #SBATCH --mail-type=FAIL

Command Example:

.. code:: bash

    sbatch /path/to/script/script.sh

To use ``Sun Grid Engine``, run the following on either servers:

.. code:: bash

    module load sge

Basic SGE commands:

.. code:: bash

    qsub # Submit a job
    qstat # Show statuses of jobs
    qdel # Cancel a job
    qhold # Place a hold on queued job to prevent it from running

Command Example:

.. code:: bash

    qsub path/to/script/Step1_motion.sh --airc_id 3tb1111 --sub 0001 --date 20230101 --ses 3

Script Template:

.. toctree::
   :maxdepth: 3

   server/docs/ParallelTemplates


