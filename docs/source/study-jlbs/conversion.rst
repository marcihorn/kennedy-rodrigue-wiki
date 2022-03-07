#################################################
Study: Joint Longitudinal Behavioral Study (JLBS)
#################################################

.. _conversion:

Conversion
==========

.. _download:

Download
--------

1. Download the appropriate files from UT Southwestern's AIRC website (`https://swlxaircweb.swmed.edu/ <https://swlxaircweb.swmed.edu/>`_) to ``/raid/data/shared/incoming``
   
   Make the zip file name completely lowercase and should match one of the following. 
   ``<airc_id>_<sub>_w<1|2|3>/``
   
   However, it be the following if there is no study ID such as pilot or test participants, it can be:
   ``<airc_id>/`` or ``<airc_id>_w<1|2|3>/``


.. _convert:

Convert
-------

2. Load appropriate modules and set parameters for subsequent code

.. code:: bash

   module load sge
   module load python/3.8.6
   #pip install --user git+https://github.com/epongpipat/pyHelperKennedyRodrigue.git
   root_dir='/raid/data'
   airc_id=""
   sub=""
   ses=""

3. Unzip files to dcm and rename to lowercase

.. code:: bash

   code_dir="${root_dir}/shared/software/scripts/eep170030/mri/convert"
   qsub -V ${code_dir}/unzip_rename.sh \
   --airc_id ${airc_id} \
   --sub ${sub} \
   --ses ${ses}

4. Sort DICOM files into appropriate directories by series name

.. code:: bash

   qsub -V ${code_dir}/sort_save_dcm.sh ${airc_id} ${sub} ${ses}

5. Convert files from dicom (.dcm) to nifti (.nii)

.. code:: bash

   code_dir="${root_dir}/shared/software/scripts/eep170030/mri/convert"
   qsub ${code_dir}/dcm2nii_wrapper.sh \
   --airc_id ${airc_id} \
   --sub ${sub} \
   --ses ${ses}

.. _qc:

QC Parameters
-------------

6. Convert the bids json files to csv

.. code:: bash

   code_dir="${root_dir}/shared/software/scripts/eep170030/mri/qc_mri/json"
   python ${code_dir}/json_to_csv.py
   Rscript ${code_dir}/combine_csv.R
