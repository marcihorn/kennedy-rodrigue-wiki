.. _conversion:

Conversion
==========

Load the following modules:

   .. code:: bash

      module use /cvl/kenrod/server/modulefiles
      module load convert-mri_study-pams/0.1.0

For more details or updates, check the `github page. <https://github.com/epongpipat/convert-mri_study-pams>`_

.. _data_entry:

Data Entry
--------

#. Enter BP measurements to the Cognitive Data Sheet, and file subject's receipts into the receipt bin.

   .. code:: bash
   
      /cvl/kenrod/study-pams/Demographic_and_Task_Data/Cognitive_Data_MCI.xlsx

#. Copy physiological measures to the server and rename the files using the following template:

   .. code:: bash
   
      /cvl/kenrod/study-pams/sourcedata/physio/KENROD_PAMS_20230101_0001_1/sub-0001_ses-01.puls
      /cvl/kenrod/study-pams/sourcedata/physio/KENROD_PAMS_20230101_0001_1/sub-0001_ses-01.resp

#. Copy the exam card to the server using the following template:

   .. code:: bash
   
      /cvl/kenrod/study-pams/sourcedata/exam_cards/KENROD_PAMS_20230101_0001_1.pdf

.. _convert:

Convert
--------

#. After receiving the BHIC Data Transfer email, run the following script to unzip data:

   .. code:: bash

      dcm_unzip.sh --data_ref abcdefgh-12345678 --sub 0001 --ses 1 --date 20230101

#. After the files have been unzipped, convert the DICOM images to NIFTI with the following script:

   .. code:: bash

      dcm2niix_wrapper.sh --sub 0001 --ses 1 --date 20230101

.. _qc_parameters:

QC Parameters
--------

#. QC the data and check for any images that have bad quality or were cut off.
      
   * Create a ``scanner_reconstruction`` directory for the reconstructed files (acq. 11,13,15,18,19 for ref.).

   * Create a ``bad`` directory within the subject directory for bad scans if necessary.

#. After QC, run the QC uber script:
   
   .. code:: bash

      qc_uber.sh --sub 0001 --ses 1 --date 20230101


#. Combine the QC logs together with the following script:
   
   .. code:: bash

      qc_combine_all.sub.sh

#. Then run the following script locally (via RStudio)

   .. code:: bash

      #use render in RStudio
      qc_report.qmd
   
#. Last, copy the QC report file to the study-pams directory

   .. code:: bash

      qc_copy_report.sh
