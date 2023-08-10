######
fMRI Data Processing
######

.. note::
   This documentation is under active development. Last updated: 08/09/2023

.. _fmri_prep:

Preparing the data for fMRI preprocessing
-----------------

Copying Behavioral Files to Server
+++++++++++++

If the subject’s fMRI behavioral files have not yet been copied to our server, they need to be copied into ``two separate locations``. 

1. Copy raw files to ``incoming/fmri_task/raw``. This path contains all raw behavioral files of ``every participant in this study``.

.. code::

    /cvl/kenrod/incoming/fmri_task/raw

2. Copy files to ``study-jlbs/Wave3/MRI/FMRI/behavioral`` directory. This path contains all behavioral files of ``every participant in wave 3``.

.. code::

    /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/behavioral

.. note::
    The fMRI files in behavioral directory must not contain duplicates or bad runs. Each task should have 3 runs.
    As of 08/09/2023, there are a total of 65 participants with functional data, with 63 DJs and 60 n-backs

.. _convert_for_SPM:

Converting the behavioral data for SPM
+++++++++++++

During each functional task, the scanner outputs information about the stimulus onset asynchrony (SOA) for each condition in each run. We must convert the output so that SPM can use it for the first-level model. There is a script that converts this file according to specific parameters: 
    
* For the DJ task, it pulls and converts information about the control, easy, medium, and hard conditions, but it ignores the fixations and the cues.
    
* For the n-back task, it pulls and converts information about the 0-back, 2-back, 3-back, and 4-back conditions. 

To convert the behavioral files to files that are usable by SPM: 

.. code::
    
    bash /cvl/kenrod/study-jlbs/MRI/FMRI/scripts/wrap_mk_soa_fix.sh <airc_id>

This should output the ‘.soa’ files into the soa folder. This is located in /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/soa.

Copying the brain data
+++++++++++++

Each subject's MPRAGE and task images should be copied over from incoming/nii to the study-jlbs/Wave3/MRI/FMRI/data directory:

.. code::
    
    bash /cvl/kenrod/software/scripts/nxs190061/study-jlbs/fmri/copy_fmri_T1.sh --airc_id <airc_id>

.. _fmri_preprocessing:

fMRI Preprocessing
-----------------
For motion correction, we will be using fsl and ASHS (link TBA)

.. code::
    
    bash /cvl/kenrod/software/scripts/nxs190061/study-jlbs/fmri/prepro/s01_uber.sh --airc_id <airc_id> --DJ 1 --Nback 1

For fMRI data processing, there are a few preprocessing steps to make sure that the scans are ready for further analysis. Each preprocessing step has been outlined below, as described in the `SPM12 program features <https://usermanual.wiki/Pdf/manual.87736313/help>`_. These steps include:

* **Coregister: Estimate and Reslice:**
* **Segmentation:**
* **Smoothing:**


STOP HERE, IM STILL WORKING ON THIS
