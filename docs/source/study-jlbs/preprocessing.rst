#################################################
Study: Joint Longitudinal Behavioral Study (JLBS)
#################################################

.. _preprocessing:

Preprocessing
==========

.. _T1w:

T1w
---

0. Setup

   .. code:: bash

      root_dir='/raid/data'
      code_dir="${root_dir}/shared/software/scripts/mri/preprocess"

1. Convert freesurfer ROIs from surfaces to volumes

   .. code:: bash

      bash ${code_dir}/s01_freesurfer_roi_from_surface_to_volume.sh

   
   ROIs: ``brainmask.mgz``, ``aparc+aseg.mgz``, ``aparc.a2009s+aseg.mgz``, ``wmparc.mgz``

2. Warp freesurfer volumetric ROIs from freesurfer-space to native T1w space

   .. code:: bash

      bash ${code_dir}/s02_warp_freesurfer_roi_to_T1w_native.sh

3. Preprocess T1w

   .. code:: bash

      bash ${code_dir}/s03_preprocess_T1w.sh

   
   The code performs the following:
   a. brain extraction (using ANTs, FSL, and Freesurfer)
   b. combines brain mask
   c. warps the brain to MNI152 space
   d. creates tissue segments (FSL's fast)