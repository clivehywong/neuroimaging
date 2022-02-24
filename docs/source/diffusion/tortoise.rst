Preprocessing Diffusion Imaging Data with TORTOISE
###################################################

`Tortoise DTI <https://tortoise.nibib.nih.gov/>` is an intensive preprocessing pipeline.

The preprocessing required several software packages to prepare necessary files, including 
  - **antsBrainExtraction.sh from ANTs**: extract the brain mask (input: T1w, output: T1w_mask)
  - **acpcdetect from ART**: align T1w to AC-PC line (input: T1w, output: T1w_ACPC)
  - **flirt from FSL**: Apply transformation to T1w mask (input: T1w_mask, output: T1w_ACPC_mask)
  - **slicesdir from FSL**: visual check (input: multiple subject T1w_ACPC, T1w_ACPC_mask)
  - **fat_proc_imit2w_from_t1w from AFNI**: generate ACPC-aligned T2-weighted images (input: T1w_ACPC, T1w_ACPC_mask, output:T2w)
  - **dtifit from FSL**: Fit tensor without preprocessing, to check if diffusion gradients (bvec) are accurate
  - **fsleyes from FSL**: Visual check the principal axis (dtifit_V1)
  - **flipbvec.sh**: Flip the bvecs (if necessary)
  - **ImportNIFTI from Tortoise**: Convert diffusion-weighted images to Tortoise files
  - **DIFFPREP from Tortoise**: Preprocess the dw images.
  - **fsleyes from fsl**: Visual check the preprocessed images.
  - **EstimateTensorNLLS from Tortoise**: Tensor estimation
  
On simulated T2 image
**********************

A high-resolution T2-weighted image is always better. The problem is T2 images are
usually not collected, or with high slice thickness.

Taylor (2016 Hum Brain Mapp) suggested that a simulated T2 image could rectify the artifacts. 

On the workflow
***************

The first attempt was to align T1 to AC-PC before brain extraction. This step would end up in inaccurate brain extraction.

On Final QC
***********

See the link below to understand different aspects of artifacts.
https://tortoise.nibib.nih.gov/tortoise/v313/11-step-4quality-assessment

The Entire Script
*****************

The script below is the entire processing pipeline. The steps shall be run one step after another for the first subject.

.. code-block:: Bash

  sub=sub-12345

  # Brain Extraction
  echo "[[ Brain Extraction ]]"
  antsBE.sh ${f}_T1w.nii ${f}_T1w

  # AC-PC
  echo "[[ AC-PC ]]"
  acpcdetect -i ${sub}_T1w.nii -center-AC

  echo "[[ Create AC-PC aligned T1w and Binary Mask ]]"
  flirt -in ${sub}_T1w_BrainExtractionMask.nii.gz -out ${sub}_ACPC_mask -ref ${sub}_T1w_BrainExtractionMask.nii.gz -applyxfm -init ${sub}_T1w_FSL.mat -interp nearestneighbour
  flirt -in ${sub}_T1w.nii -out ${sub}_ACPC -ref ${sub}_T1w.nii -applyxfm -init ${sub}_T1w_FSL.mat

  # Imitate T2 from T1
  echo "[[ Create T2 ]]"
  fat_proc_imit2w_from_t1w -inset ${sub}_T1w_ACPC.nii.gz -mask ${sub}_T1w_ACPC_mask.nii.gz -prefix ${sub}_T1w_ACPC_T2w

  # DTIFIT
  echo "[[ Fit Tensor: dtifit/fsl ]]"
  dtifit -k ${sub}_dwi.nii -m ${sub}_dwi_brain_mask.nii -r ${sub}_dwi.bvec -b ${sub}_dwi.bval -o ${sub}_dtifit/dtifit

  # Check
  echo "Visual Check the Principal axis"
  echo "fsleyes ${sub}_dtifit"

  # Flip b-vectors
  echo "[[ Flip b-vectors ]]"
  echo " If the 
  dwi_flip_bvec.sh sub-14465_dwi.bvec

  # Import NIFTI
  echo "[[ Import NIFTI ]]"
  ImportNIFTI -i sub-14465_dwi.nii -b sub-14465_dwi.bval -v sub-14465_dwi.bvec -p vertical

  # "[[ Fit Tensor: EstimateTensorWLLS/Tortoise ]]"
  EstimateTensorWLLS -i sub-14465_dwi_proc/sub-14465_dwi.list -m sub-14465_dwi_brain_mask.nii

  # DIFFPREP
  echo "[[ DIFFPREP -i dwi -s T2 ]]"
  DIFFPREP -i ${sub}_dwi_proc/${sub}_dwi.list -s ${sub}_T1w_ACPC_T2w.nii.gz -e ANTSSyN

  # Estimate Tensor
  echo "[[ Estimate Tensors ]]"
  EstimateTensorNLLS -i ${sub}_dwi_proc/${sub}_dwi_DMC.list

  # Compute Tensor Maps
  echo "[[ Compute Tensor Maps ]]"
  ComputeAllTensorMaps.bash ${sub}_dwi_proc/${sub}_dwi_DMC_N1_DT.nii



acpcdetect
**********

The first step involved AC-PC detection. After running the script below, the file sub-00001
.. code-block:: Bash
  f=sub-00001
  acpcdetect -i ${f}_T1w.nii -center-AC
  
References  
**********

Taylor, P. A., Alhamud, A., van der Kouwe, A., Saleh, M. G., Laughton, B., & Meintjes, E. (2016). 
Assessing the performance of different DTI motion correction strategies in the presence of 
EPI distortion correction. Hum. Brain Mapp., 37(12), 4405–4424. doi: 10.1002/hbm.23318
