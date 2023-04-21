######
fMRI Data Processing
######

Test

.. note::
   This documentation is under active development. Last updated: 4/21/2023

.. _copying_behavioral:

Copying Behavioral Files to Server
-----------------

If the subject’s fMRI behavioral files have not yet been copied to our server, they need to be copied into ``two separate locations``. 

1. Copy raw files to ``incoming/fmri_task/raw``. This path contains all raw behavioral files of ``every participant in this study``.

.. code::

    /cvl/kenrod/incoming/fmri_task/raw

|

2. Copy files to ``study-jlbs/Wave3/MRI/FMRI/behavioral`` directory. This path contains all behavioral files of ``every participant in wave 3``.

.. code::

    /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/behavioral

.. note::
    The fMRI files in behavioral directory must not contain duplicates or partial runs. Each task should have 3 runs.
    As of 4/21/2023, there are a total of 68 participants with functional data, with 66 DJs and 62 n-backs

.. _convert_for_SPM:

Converting the behavioral data for SPM
-----------------

During each functional task, the scanner outputs information about the stimulus onset asynchrony (SOA) for each condition in each run. These are one of the four files found for each subject in both the ‘incoming’ and ‘behavioral’ folders.  We must convert the output so that SPM can use it for the first-level model. There is a script that converts this file according to specific parameters: 
    
* For the DJ task, it pulls and converts information about the control, easy, medium, and hard conditions, but it ignores the fixations and the cues.
    
* For the n-back task, it pulls and converts information about the 0-back, 2-back, 3-back, and 4-back conditions. 

To convert the behavioral files to files that are usable by SPM: 

.. code::
    
    bash /cvl/kenrod/KK_KR_JLBS/MRI/FMRI/scripts/wrap_mk_soa_fix.sh <airc_id>

This should output the ‘.soa’ files into the soa folder. This is located in /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/soa.


.. _fmri_preprocessing:

Preprocessing
-----------------
For fMRI data processing, there are a few preprocessing steps to make sure that the scans are ready for further analysis. Each preprocessing step has been outlined below, as described in the SPM8 program features. These steps include: 

* **Realignment: Estimate & Reslice:** “This routine realigns a time-series of images acquired from the same subject using a least squares approach and a six parameter (rigid body) spatial transformation. The first image in the list specified by the user is used as a reference to which all subsequent scans are realigned. The reference scan does not have to be the first chronologically and it may be wise to choose a “representative scan” in this role. The aim is primarily to remove movement artifact in fMRI (or more generally longitudinal studies). The headers are modified for each of the input images such that they reflect the relative orientations of the data. The details of the transformation are displayed in the results window s plots of translation and rotation. A set of realignment parameters is saved for each session, and after the realignment, the images are resliced such that they match the first image selected voxel-for-voxel.”

* **Coregister: Estimate and Reslice:** “The registration method used here is based on the work by Collignon et al. The original interpolation method described in this paper has been changed in order to give a smoother cost function. The images are also smoothed slightly, as is the histogram. This is all in order to make the cost function as smooth as possible, to give faster convergence and less chance of local minima. At the end of coregistration, the voxel-to-voxel affine transformation matrix is displayed, along with the histograms for the images in the original orientations, and the final orientations. The registered images are displayed at the bottom.”	

* **Segmentation:** “This function can be used for bias correcting, spatially normalizing or segmenting your data. Note that this module needs the images to be roughly aligned with the tissue probability maps before you begin. If strange results are obtained, then this is usually because the images were poorly aligned beforehand. This step segments the structural to tissue classes, and writes out a warped brain in template space. There is a deformation utility to apply the deformation field from the new segment to all the functional volumes.”

* **Smoothing:** This step is done to normalize and average noise in the image, and to adjust for individual differences in subjects’ brain anatomy. 

STOP HERE, IM STILL WORKING ON THIS
