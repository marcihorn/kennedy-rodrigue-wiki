######
Parallel Script Templates
######

.. note::
   This documentation is under active development. Last updated: 08/09/2023

.. _slurm_script:

Slurm Script Template
-----------------

.. code:: bash

    #!/bin/bash

    # ------------------------------------------------------------------------------
    # author:       Neo Shin
    # date:         2023-08-09
    # function:     script_.sh --airc_id <airc_id> --sub <sub> --date <20230201> --ses <1|2|3>
    # description:  script to run through slurm
    # ------------------------------------------------------------------------------

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
    #SBATCH --time=2-00:00:00         # day-hours:minutes:seconds format

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
    fslmerge something something

    # ------------------------------------------------------------------------------
    # end
    # ------------------------------------------------------------------------------
    ensure_permissions ${out_paths[nii_dir}
    print_footer

Slurm Command Example:
-----------------

To run this script:

.. code:: bash

    sbatch /path/to/script/script_.sh --airc_id 3tb1111 --sub 0001 --date 20230101 --ses 3

.. _sungrid_script:

Sun Grid Engine Uber Script Template
-----------------

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
    
SGE Command Example:
-----------------

To run this uber script:

.. code:: bash

    bash /path/to/script/script_uber.sh --airc_id 3tb1111 --sub 0001 --date 20230101 --ses 3

or if you want to run individual scripts:

.. code:: bash

    qsub /path/to/script/Step1_motion.sh --airc_id 3tb1111 --sub 0001 --date 20230101 --ses 3
