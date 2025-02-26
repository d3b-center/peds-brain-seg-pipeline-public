# Pediatric Brain Tumor Processing Pipeline

This pipeline can be used to generate AI-predicted brain tumor segmentations for pediatric patients with multi-parametric MRIs. It was trained using the nnU-Net framework on a multi-institutional, heterogeneous dataset (see reference).

The overall pipeline includes:
1. Pre-processing
2. AI-powered skull stripping
3. AI-powered tumor segementation
4. image intensity normalization (on skull stripped images)
5. radiomic feature extraction

Images are pre-processed (using [CaPTk BraTS pipeline](https://cbica.github.io/CaPTk/preprocessing_brats.html)) and then input into trained deep learning models for skull-stripping (brain mask segmentation) and tumor segmentation. Based on 4 input image sequences per patient, the model will output a single prediction file with up to 4 tumor subregions:
1. Enhancing tumor
2. Non-enhancing tumor
3. Cyst
4. Edema

If you use this tool in your work, please cite the following reference accordingly:

1. Arastoo Vossough, Nastaran Khalili, Ariana M. Familiar, Deep Gandhi, Karthik Viswanathan, Wenxin Tu, Debanjan Haldar, Sina Bagheri, Hannah Anderson, Shuvanjan Haldar, Phillip B. Storm, Adam Resnick, Jeffrey B. Ware, Ali Nabavizadeh, Anahita Fathi Kazerooni, "Training and Comparison of nnU-Net and DeepMedic Methods for Autosegmentation of Pediatric Brain Tumors", American Journal of Neuroradiology, 2024. PubMed: 38724204. DOI: https://doi.org/10.3174/ajnr.A8293. https://arxiv.org/abs/2401.08404
2. Gandhi DB, Khalili N, Familiar AM, Gottipati A, Khalili N, Tu W, Haldar S, Anderson H, Viswanathan K, Storm PB, Ware JB, Resnick A, Vossough A, Nabavizadeh A, Fathi Kazerooni A. Automated pediatric brain tumor imaging assessment tool from CBTN: Enhancing suprasellar region inclusion and managing limited data with deep learning. Neurooncol Adv. 2024 Dec 12;6(1):vdae190. doi: 10.1093/noajnl/vdae190. PMID: 39717438; PMCID: PMC11664259.
   
### Acknowledgement 
Ariana Familiar, PhD, Center for Data-Driven Discovery in Biomedicine (D3b), Children's Hospital of Philadelphia

## STEP 1: Prepare the input files

The pipeline requires 4 images per subject (T2w-FLAIR, T1w, T1w post-contrast, T2w).

### Organization

Input files (raw data) must be located in an `input/` directory folder (called "input") and named with the following format: `[subID]_[imageID]...[.nii/.nii.gz]` where the imageID for each image type is:

| Image type      | imageID | nnUNet naming |
| ----------- | ----------- | ----------- |
| T2w-FLAIR      | FL       | 0000        |
| T1w   | T1        | 0001        |
| T1w post-contrast   | T1CE        | 0002        |
| T2w   | T2        | 0003        |

NOTE: the exact file format is required with an underscore: [subID]_[imageID]

For example:
```
input/
    sub001_FL.nii.gz
    sub001_T1.nii.gz
    sub001_T1CE.nii.gz
    sub001_T2.nii.gz
    sub002_FL.nii.gz
    ...
```



## STEP 2: Usage

1. [Install Docker](https://docs.docker.com/engine/install/)
2. Copy all files into a single directory and run: `docker compose up` from within that directory
    ```
    docker-compose.yml
    input/
        sub001_FL.nii.gz
        sub001_T1.nii.gz
        ...
    ```

It takes about an hour to fully process a single subject's data (depending on your machine specs). Model predictions will be stored in an `output/` folder with files named `[subID]_pred_brainTumorSeg.nii.gz` .

Output example:
```
docker-compose.yml
input/
    sub001_FL.nii.gz
    sub001_T1.nii.gz
    ...
output/
    sub001_pred_brainTumorSeg.nii.gz
    sub001_pred_brainMask.nii.gz
    sub001_0000_ss.nii.gz
    ...
preprocessed/
    sub001_0000.nii.gz
    sub001_0001.nii.gz
    ...
```

To only run a single step (e.g., pre-processing), use the corresponding docker compose file:

```
docker compose -f docker-compose-preproc.yml up
```

Preprocessing-only output example:
```
docker-compose-preproc.yml
input/
    sub001_FL.nii.gz
    sub001_T1.nii.gz
    ...
preprocessed/
    sub001_FL_to_SRI.nii.gz
    sub001_T1_to_SRI.nii.gz
    ...
```

NOTE: after pre-processing, file names will have [imageID]-type file naming, on input to nnUNet segmentation the files will be renamed according to the necessary numeric code for the deep learning model configuration.

## Usage & Citations
Note: Use of this software is available to academic and non-profit institutions for research purposes only subject to the terms of the 2-Clause BSD License (see License). For use or transfers of the software to commercial entities, please inquire with Dr. Anahita Fathi Kazerooni - fathikazea@chop.edu. 

If you use the model in your research study, please cite the following paper(s):
1. Arastoo Vossough, Nastaran Khalili, Ariana M. Familiar, Deep Gandhi, Karthik Viswanathan, Wenxin Tu, Debanjan Haldar, Sina Bagheri, Hannah Anderson, Shuvanjan Haldar, Phillip B. Storm, Adam Resnick, Jeffrey B. Ware, Ali Nabavizadeh, Anahita Fathi Kazerooni, "Training and Comparison of nnU-Net and DeepMedic Methods for Autosegmentation of Pediatric Brain Tumors", American Journal of Neuroradiology, 2024. PubMed: 38724204. DOI: https://doi.org/10.3174/ajnr.A8293. https://arxiv.org/abs/2401.08404
2. Gandhi DB, Khalili N, Familiar AM, Gottipati A, Khalili N, Tu W, Haldar S, Anderson H, Viswanathan K, Storm PB, Ware JB, Resnick A, Vossough A, Nabavizadeh A, Fathi Kazerooni A. Automated pediatric brain tumor imaging assessment tool from CBTN: Enhancing suprasellar region inclusion and managing limited data with deep learning. Neurooncol Adv. 2024 Dec 12;6(1):vdae190. doi: 10.1093/noajnl/vdae190. PMID: 39717438; PMCID: PMC11664259.
   


