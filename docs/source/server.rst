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

.. code:: bash
    
    sbatch # Submit a job
    srun # Submit a job by specifying resource requirements, such as processor count
    squeue # Show statuses of jobs
    scancel # Cancel a job

Script Template:

.. code:: bash

    TBA

To use ``Sun Grid Engine``, run the following on either servers:

.. code:: bash

    module load sge

Basic SGE commands:

.. code:: bash

    qsub # Submit a job
    qstat # Show statuses of jobs
    qdel # Cancel a job
    qhold # Place a hold on queued job to prevent it from running

Script Template:

.. code:: bash

    #!/bin/bash

    # ------------------------------------------------------------------------------
    # author:       Neo Shin
    # date:         2023-08-09
    # function:     script_uber.sh --airc_id <airc_id> --sub <sub> --date <20230201> --ses <1|2|3>
    # description:  script to run 3 jobs sequentially
    # ------------------------------------------------------------------------------

    # ------------------------------------------------------------------------------
    # modules
    # ------------------------------------------------------------------------------
    module load sge
    module load bashHelperKennedyRodrigue
    source bashHelperKennedyRodrigueFunctions.sh

    # ------------------------------------------------------------------------------
    # args/hdr
    # ------------------------------------------------------------------------------
    parse_args "$@"
    req_arg_list=(airc_id sub date ses)
    check_req_args ${req_arg_list[@]}

    print_header

    # ------------------------------------------------------------------------------
    # paths
    # ------------------------------------------------------------------------------
    root_dir=`get_root_dir kenrod`
    code_dir=`dirname $0`
    in_dir="${root_dir}/to/the/input/directory"
    out_dir="${root_dir}/to/the/output/directory"

    # ------------------------------------------------------------------------------
    # check paths
    # ------------------------------------------------------------------------------
    # Stops script if the input directory does not exist
    if [[ ! -d ${in_dir} ]]; then 
        echo "error: file does not exist (in_dir: ${in_dir})"
        exit 1;
    fi

    # Stop script if the output directory exists and overwrite was not enabled
    if [[ -d ${out_dir} ]] && [ ! -z "$(ls -A ${out_dir})" ] && [[ ${overwrite} -eq 0 ]]; then 
        echo "error: non-empty directory exists and overwrite set to 0 (out_dir: ${out_dir})"
        exit 1;
    fi

    # ------------------------------------------------------------------------------
    # main
    # ------------------------------------------------------------------------------
    qsub \
    -V \ # All environment variables active within qsub will be exported to context of the job
    -N Step-1_${airc_id} \ # Name of the job
    ${code_dir}/Step1_motion.sh ${opts} # the script that will be submitted as a job
    
    qsub \
    -hold_jid Step-1_${airc_id} \ # Hold this job until Step-1_${airc_id} job finishes
    -V \
    -N Step-2_${airc_id} \
    ${code_dir}/Step2_extraction.sh ${opts}
    
    qsub \
    -hold_jid Step-2_${airc_id} \ # Hold this job until Step-2_${airc_id} job finishes
    -V \
    -N convert-mri_step-3_airc-id-${airc_id} \
    ${code_dir}/Step3_ect.sh ${opts}
    
    # ------------------------------------------------------------------------------
    # print footer
    # ------------------------------------------------------------------------------
    print_footer #Will need to double check, I don't think print_footer works on an uber script, but has to be inside the job scripts

Command Example:

To run an uber script:

.. code:: bash

    bash script_uber.sh --airc_id 3tb1111 --sub 0001 --date 20230101 --ses 3

or if you want to run individually:

.. code:: bash

    qsub Step1_motion.sh --airc_id 3tb1111 --sub 0001 --date 20230101 --ses 3
