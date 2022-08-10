.. _conversion:

Conversion
==========

.. _download:

Download
--------

#. Download the appropriate files from UT Southwestern's AIRC website (`https://swlxaircweb.swmed.edu/ <https://swlxaircweb.swmed.edu/>`_) to ``/raid/data/shared/incoming``
   
   Make the zip file name completely lowercase and should match the following:
   ``<airc_id>_<sub>_w<1|2|3>/``
   
   However, if there is no study ID such as the participant being a pilot, it can be:
   ``<airc_id>/`` or ``<airc_id>_w<1|2|3>/``


.. _convert:

Convert
-------

.. note::

   Install the following package if you have not run this before:

   .. code:: bash

      module load python/3.8.6
      pip install --user git+https://github.com/epongpipat/pyHelperKennedyRodrigue.git


#. Load appropriate modules and set parameters for subsequent code

   .. code:: bash

      module load sge
      module load python/3.8.6
      root_dir='/raid/data'
      airc_id=""
      sub=""
      ses=""

#. Convert MRI

   .. code:: bash

      code_dir="${root_dir}/shared/software/scripts/eep170030/mri/convert"
      qsub -V ${code_dir}/convert_uber.sh \
      --airc_id ${airc_id} \
      --sub ${sub} \
      --ses ${ses}

   The code performs the following:
   
   1. unzip files and rename the output directory to lowercase
   2. sort the DICOM files into their appropriate directories by series name
   3. convert files from DICOM to NIfTI using dcm2niix

   check conversion and move bad files (e.g., incomplete acquisitions) into a ``bad/`` directory

.. _qc:

QC Parameters
-------------

#. Convert the bids json files to csv

   .. code:: bash

      code_dir="${root_dir}/shared/software/scripts/eep170030/mri/qc_mri/json"
      python ${code_dir}/json_to_csv.py \
      --airc_id ${airc_id} \
      --sub ${sub}

   .. code:: bash

      Rscript ${code_dir}/combine_csv.R


#. Convert fslhd to csv
   
   .. code:: bash

      code_dir="${root_dir}/shared/software/scripts/Janes_scripts"
      bash ${code_dir}/fslhd2csv.sh \
      --airc_id ${airc_id} \
      --sub ${sub}

   .. code:: bash

      code_dir="${root_dir}/shared/software/scripts/eep170030/mri/qc_mri/fslhd"
      Rscript ${code_dir}/combine_csv.R

#. Combine all information

   .. code:: bash

      code_dir="${root_dir}/shared/software/scripts/eep170030/mri/qc_mri"
      Rscript ${code_dir}/combine_all.R

#.  Create QC report

   .. note::

      The following code needs to be run locally. The server does not currently have a compatible pandoc version

   .. code:: bash

      code_dir="${root_dir}/shared/software/scripts/eep170030/mri/qc_mri/rmd"
      Rscript ${code_dir}/render-rmds.R


   Six (6) QC html reports will be updated/saved in ``${root_dir}/shared/incoming/qc/derivatives/sub-differences/rmd-html``. Please review and any potential outliers in either qualitative or quantitive values, and bring up any potential concerns to the team.

.. _copy:

Copy Files
----------

#. Copy and rename files

   .. note:: 

      may need to install the following if running for the first time:

      .. code:: bash

         Rscript -e 'devtools::install_github("epongpipat/rHelperKennedyRodrigue")'

   .. code:: bash

      module load fsl

   
   Create an ``original/`` directory and move files inside the original directory

   .. code:: bash

      mkdir ${root_dir}/shared/incoming/nii/${airc_id}_${sub}/original/
      mv ${root_dir}/shared/incoming/nii/${airc_id}_${sub}/* ${root_dir}/shared/incoming/nii/${airc_id}_${sub}/original/

   rename files to match prior waves

   .. code:: bash
      
      code_dir="${root_dir}/shared/software/scripts/eep170030/mri/modality_specific_conversion"
      Rscript ${code_dir}/rename_all_mri_types.R \
      --airc_id ${airc_id} \
      --sub ${sub}