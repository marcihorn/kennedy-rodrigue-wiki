######
fMRI Data Processing
######

.. note::
   This documentation is under active development. Last updated: 4/22/2023

.. _copying_behavioral:

Copying Behavioral Files to Server
-----------------

If the subject’s fMRI behavioral files have not yet been copied to our server, they need to be copied into ``two separate locations``. 

1. Copy files to ``incoming/fmri_task/raw``. This path contains all raw behavioral files of ``every participant in this study``.

.. code::

    /cvl/kenrod/incoming/fmri_task/raw

|

2. Copy files to ``study-jlbs/Wave3/MRI/FMRI/behavioral`` directory. This path contains all behavioral files of ``every participant in wave 3``.

.. code::

    /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/behavioral

.. note::
    The fMRI files in behavioral directory must not contain duplicates or partial runs. Each task should have 3 runs.
    As of 4/22/2023, there are a total of 68 participants with functional data, with 66 DJs and 62 n-backs

.. copying structural:

Copying Structural Files
-----------------

.. code::
   rsync -ur /cvl/kenrod/incoming/nii/<airc_id_subject_id>/DJ1 /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/data/<airc_id>/DJ1_SENSE

   rsync -ur /cvl/kenrod/incoming/nii/<airc_id_subject_id>/DJ2 /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/data/<airc_id>/DJ2_SENSE
   rsync -ur /cvl/kenrod/incoming/nii/<airc_id_subject_id>/DJ3 /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/data/<airc_id>/DJ3_SENSE
   rsync -ur /cvl/kenrod/incoming/nii/<airc_id_subject_id>/Nback1 /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/data/<airc_id>/Nback1_SENSE
   rsync -ur /cvl/kenrod/incoming/nii/<airc_id_subject_id>/Nback2 /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/data/<airc_id>/Nback2_SENSE
   rsync -ur /cvl/kenrod/incoming/nii/<airc_id_subject_id>/Nback3 /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/data/<airc_id>/Nback3_SENSE

   rsync -ur /cvl/kenrod/incoming/nii/<airc_id_subject_id>/MPRAGE2100_SAG_SENSE.nii /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/data/<airc_id>/
   gunzip MPRAGE2100_SAG_SENSE.nii
   
loop over task, run, and subject
copy MPRAGE (T1w) also into shared/KK_KR_JLBS/Wave2/MRI/FMRI/data/<airc_id>/MPRAGE2100_SAG_SENSE.nii



.. _convert_for_SPM:

Converting the behavioral data for SPM
-----------------

During each functional task, the scanner outputs information about the stimulus onset asynchrony (SOA) for each condition in each run. These are one of the four files found for each subject in both the ‘incoming’ and ‘behavioral’ folders.  We must convert the output so that SPM can use it for the first-level model. There is a script that converts this file according to specific parameters: 
    
* For the DJ task, it pulls and converts information about the control, easy, medium, and hard conditions, but it ignores the fixations and the cues.
    
* For the n-back task, it pulls and converts information about the 0-back, 1-back, 2-back, 3-back, and 4-back conditions. 

To convert the behavioral files to files that are usable by SPM: 

.. code::
    
    bash /cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/scripts/wrap_mk_soa_fix.sh <airc_id>

This should output the ‘.soa’ files into the soa folder. This is located in ``/cvl/kenrod/study-jlbs/Wave3/MRI/FMRI/soa``.


.. _fmri_preprocessing:

Preprocessing
-----------------
For fMRI data processing, there are a few preprocessing steps to make sure that the scans are ready for further analysis. Each preprocessing step has been outlined below, as described in the `SPM12 program features <https://usermanual.wiki/Pdf/manual.87736313/help>`_. These steps include:

* **Realignment: Estimate & Reslice:** This routine realigns a time-series of images acquired from the same subject using a least squares approach and a 6 parameter (rigid body) spatial transformation. The first image in the list specified by the user is used as a reference to which all subsequent scans are realigned. The reference scan does not have to be the first chronologically and it may be wise to choose a “representative scan” in this role. The aim is primarily to remove movement artifact in fMRI (or more generally longitudinal studies). The headers are modified for each of the input images such that they reflect the relative orientations of the data. The details of the transformation are displayed in the results windows plots of translation and rotation. A set of realignment parameters is saved for each session, named rp_*.txt. These can be modelled as confounds within the general linear model. After realignment, the images are resliced such that they match the first image selected voxel-for-voxel. The resliced images are named the same as the originals, except that they are prefixed by ’r’.

* **Coregister: Estimate and Reslice:** Within-subject registration using a rigid-body model and image reslicing. The registration method used here is based on work by Collignon et al (1995). The original interpolation method described in this paper has been changed in order to give a smoother cost function. The images are also smoothed slightly, as is the histogram. This is all in order to make the cost function as smooth as possible, to give faster convergence and less chance of local minima. At the end of coregistration, the voxel-to-voxel affine transformation matrix is displayed, along with the histograms for the images in the original orientations, and the final orientations. The registered images are displayed at the bottom. Registration parameters are stored in the headers of the "source" and the "other" images. These images are also resliced to match the source image voxel-for-voxel. The resliced images are named the same as the originals except that they are prefixed by ’r’.

* **Segmentation:** This function can be used for bias correcting, spatially normalizing or segmenting your data. Note that this module needs the images to be roughly aligned with the tissue probability maps before you begin. If strange results are obtained, then this is usually because the images were poorly aligned beforehand. This step segments the structural to tissue classes, and writes out a warped brain in template space. There is a deformation utility to apply the deformation field from the new segment to all the functional volumes.

* **Smoothing:** This step is done to normalize and average noise in the image, and to adjust for individual differences in subjects’ brain anatomy. 

STOP HERE, IM STILL WORKING ON THIS

Preprocessing is done through SPM12. To launch SPM12, we use Matlab, which is run through the server.

.. code::
   module load matlab
   matlab

Inside Matlab, type the following code to start SPM's fMRI program:

.. code::
   spm fmri

.. note:: 
   Make sure you are in the right directory inside Matlab. There should be a bunch of wrapper scripts in this directory.

   .. code:: 
      cd /cvl/kenrod/study-jlbs/Wave3/MRI/scripts

Then use the wrapper script:
   
.. code:: 
   wrap_prepro(‘/raid/data/shared/KK_KR_JLBS/MRI/nii/<airc_id>’)

.. note::
   Wrapper scripts are different from other scripts in that they identify the specific parameters and specs files that need to be used for the command. If the wrapper preprocessing script does not work for any reason, you can still preprocess subjects “manually”: 


	Change directories so that you are in the ‘prepro’ folder. (cd /raid/data/shared/software/spm8batching/prepro). At the command line, type: prepro8
	You will see a box pop up that tells you to select the necessary specs file. On the left side of the box, use single-clicks to click on the ‘..’ until you are in the /raid/data/shared/ directory. 
•	Use single-clicks on each folder to navigate to the /KK_KR_JLBS/MRI/params folder.  Make sure to only use single-clicks in these pop-up boxes. 
	Once the params folder is selected, use a single-click to select the specs file named ‘prepro_specs.m’ in the right side of the box. Click on the ‘Done’ button at the bottom of the box. 
	Another box will pop up to ask you to select the subject directories. Use single-clicks on the ‘..’ in the left side of the box to navigate to the /raid/data/shared/ directory again and use single-clicks to get to the /KK_KR_JLBS/MRI/nii folder. 
•	Once you are in the nii folder, select the subject’s you would like to preprocess from the list on the right side of the box. Click on ‘Done’ at the bottom of the box. 
o	Preprocessing takes a few minutes, so you can let it run in the background while you are doing other things. If it is still preprocessing, Matlab will display ‘Busy’ in the bottom left-hand corner of the window. That will disappear once the job is done (and the command window will say ‘Done’. 
o	If you need to leave your computer, you can suspend the session so that NX will still run in the background. To do this, click the ‘x’ in the top right hand corner to close the main NX window (do not close the Matlab window!). You will see a popup that asks if you’d like to terminate or suspend the session. Click on suspend if you would like to keep NX running in the background. If you are completely done with preprocessing, you can select ‘terminate’ to close out of the session. 

5.	Movement Artifacts 
•	Movement artifacts and intensity shifts must be addressed after we run the preprocessing script. 
•	Art_batch: goes through EPI scans to see if there is too much movement/intensity shift. It outputs this into regression parameters – they’re then put as covariates in the model 
o	art_batch(‘/raid/data/shared/KK_KR_JLBS/MRI/nii/3tbxxxx’)
•	
How to determine our outliers:
Movement in 15% of volumes in each run = outlier
DJ = 30 volumes and N-back = 40 volumes

