######
Script Templates
######

.. note::
   This documentation is under active development. Last updated: 08/09/2023

.. _parallel_script:

Parallel Script Template
-----------------

.. code:: bash

    #!/bin/bash

    # ------------------------------------------------------------------------------
    # author:       Neo Shin
    # date:         2023-08-09
    # function:     script.sh --airc_id <airc_id> --sub <sub> --date <20230201> --ses <1|2|3>
    # description:  script to run through slurm or SGE
    # ------------------------------------------------------------------------------

    # ------------------------------------------------------------------------------
    # SLURM or SGE settings here
    # ------------------------------------------------------------------------------
    # SLURM settings
    #SBATCH --nodes=1
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=1        # can be increased if needed
    #SBATCH- -mem=4G                 # can be increased if needed
    #SBATCH --partition=KRLab
    #SBATCH --output=jid-%A-%a_jname-%x.log
    #SBATCH --mail-type=FAIL
    #SBATCH --time=2-00:00:00        # day-hours:minutes:seconds format

    # SGE settings
    #$ -V
    #$ -S /bin/bash
    #$ -o jid-$JOB_ID-$TASK_ID_jname-$JOB_NAME.log
    #$ -j y
    #$ -m a \
    #$ -M ${USER}@utdallas.edu

    # ------------------------------------------------------------------------------
    # modules
    # ------------------------------------------------------------------------------
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
    
    declare -A in_paths
    in_paths[nii_dir]="${root_dir}/to/the/input/directory"
    in_paths[code_dir]=`dirname $0`
    
    declare -A out_paths
    out_paths[nii_dir]="${root_dir}/to/the/output/directory"

    # ------------------------------------------------------------------------------
    # check paths
    # ------------------------------------------------------------------------------
    # Stops script if the input directories do not exist
    check_in_paths ${in_paths[@]} 

    # Stop script if the output directory exists and overwrite was not enabled
    if [[ -d ${out_paths[nii_dir]} ]] && [ ! -z "$(ls -A ${out_paths[nii_dir]})" ] && [[ ${overwrite} -eq 0 ]]; then 
        echo "error: non-empty directory exists and overwrite set to 0 (out_paths: ${out_paths})"
        exit 1;
    fi

    # ------------------------------------------------------------------------------
    # main
    # ------------------------------------------------------------------------------
    #main code here

    # ------------------------------------------------------------------------------
    # end
    # ------------------------------------------------------------------------------
    ensure_permissions ${out_paths[nii_dir]}
    print_footer

Command Example:
-----------------

To run this script:

.. code:: bash

    # SLURM
    sbatch /path/to/script/script.sh --airc_id 3tb1111 --sub 0001 --date 20230101 --ses 3

    # SGE
    module load sge
    qsub /path/to/script/script.sh --airc_id 3tb1111 --sub 0001 --date 20230101 --ses 3

