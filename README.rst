Clive's Notes on Neuroimaging Data Analysis
===========================================

Functional MRI
--------------
1. fmriprep
  1.1. Project Folder Structures
  1.2. BIDS Folder Structure
  1.3. Preprocessing Options
  1.4. Using the scripts in the SKL server
2. FSL/FEAT
  2.1. Incorporating fmriprep nuisance Regressors
  2.2. First-level BOLD design matrix and contrasts
  2.3. Running First-level
  2.4. After first-level: the FSL standard-space folder
  2.5. Group-level analysis
    2.5.1. Simple t-test
    2.5.2. Two way anova: One within- one between-
    2.5.3. Linear regression
    2.5.4. Linear regression: two groups

Diffusion MRI
-------------
1. Basic preprocessing: FSL
  1.1. Eddy + dti_fit
  1.2. FDT
  1.3. Bedpostx
2. Advanced preprocessing: TORTOISE DTI
  2.1. T1 image ac-pc alignment
  2.2. T2-to-T1 alignment
  2.3. DIFFPREP
