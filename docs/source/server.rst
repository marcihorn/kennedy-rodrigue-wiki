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

To run parallel jobs, we can use `SLURM (preferred) <https://slurm.schedmd.com/quickstart.html>`_ or `Sun Grid Engine (old) <http://star.mit.edu/cluster/docs/0.93.3/guides/sge.html>`_

To use ``SLURM``, first access the server via ``cvlkrcompute2.utdallas.edu`` (or ``totoro.utdallas.edu``) or via ``cortex.cvl.utdallas.edu``.

SLURM
+++++++

Basic SLURM commands:

.. code:: bash
    
    sbatch <script>          # Submit a job
    squeue                   # Show see pending jobs
    scancel <jobid>          # cancel specific job
    scancel -u <username>    # Cancel all jobs

Common SLURM settings:

.. code:: bash

    # ------------------------------------------------------------------------------
    # slurm settings
    # ------------------------------------------------------------------------------
    #SBATCH --nodes=1
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=1        # can be increased if needed
    #SBATCH- -mem=4G                 # can be increased if needed
    #SBATCH --partition=KRLab
    #SBATCH --output=jid-%A-%a_jname-%x.log
    #SBATCH --mail-type=FAIL
    #SBATCH --time=2-00:00:00        # day-hours:minutes:seconds format
    #SBATCH --export=NONE            # ALL or comma-separated environment variables

Command Example:

.. code:: bash

    sbatch /path/to/script.sh

Sun Grid Engine
+++++++


To use ``Sun Grid Engine``, run the following on ``cvlkrcompute1.utdallas.edu`` (or ``ponyo.utdallas.edu``):

.. code:: bash

    module load sge

Basic SGE commands:

.. code:: bash

    qsub <script>    # Submit a job
    qstat            # Show statuses of jobs
    qdel <jobid>     # Cancel a job
    qhold <jobid>    # Place a hold on queued job to prevent it from running

Common SGE settings:

.. code:: bash

    # ------------------------------------------------------------------------------
    # sge settings
    # ------------------------------------------------------------------------------
    #$ -V
    #$ -S /bin/bash
    #$ -o jid-$JOB_ID-$TASK_ID_jname-$JOB_NAME.log
    #$ -j y
    #$ -m a \
    #$ -M ${USER}@utdallas.edu

Command Example:

.. code:: bash

    qsub /path/to/script.sh --airc_id 3tb1111 --sub 0001 --date 20230101 --ses 3

`Parallel Script Template <https://kennedy-rodrigue-wiki.readthedocs.io/en/latest/server/docs/ScriptTemplates.html#parallel-script-template>`_
