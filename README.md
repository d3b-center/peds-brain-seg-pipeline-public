# Pediatric Brain Tumor Processing Pipeline

This pipeline can be used to generate AI-predicted brain tumor segmentations for pediatric patients with multi-parametric MRIs. It was trained using the nnU-Net framework on a multi-institutional, heterogeneous dataset (see reference).

Images are pre-processed (using [CaPTk BraTS pipeline](https://cbica.github.io/CaPTk/preprocessing_brats.html)) and then input into a trained deep learning model. Based on 4 input image sequences per patient, the model will output a single prediction file with up to 4 tumor subregions:
1. Enhancing tumor
2. Non-enhancing tumor
3. Cyst
4. Edema

If you use this tool in your work, please cite the following reference accordingly:

1. Arastoo Vossough, Nastaran Khalili, Ariana M. Familiar, Deep Gandhi, Karthik Viswanathan, Wenxin Tu, Debanjan Haldar, Sina Bagheri, Hannah Anderson, Shuvanjan Haldar, Phillip B. Storm, Adam Resnick, Jeffrey B. Ware, Ali Nabavizadeh, Anahita Fathi Kazerooni, "Training and Comparison of nnU-Net and DeepMedic Methods for Autosegmentation of Pediatric Brain Tumors", https://arxiv.org/abs/2401.08404

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
        ...
    preprocessed/
        sub001_0000.nii.gz
        sub001_0001.nii.gz
        ...
    ```

To only the pre-processing step, use the following docker compose file:

```
docker compose -f docker-compose-preproc.yml up
```

Preprocessing output example:
    ```
    Dockerfile
    run.py
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
1. Arastoo Vossough, Nastaran Khalili, Ariana M. Familiar, Deep Gandhi, Karthik Viswanathan, Wenxin Tu, Debanjan Haldar, Sina Bagheri, Hannah Anderson, Shuvanjan Haldar, Phillip B. Storm, Adam Resnick, Jeffrey B. Ware, Ali Nabavizadeh, Anahita Fathi Kazerooni, "Training and Comparison of nnU-Net and DeepMedic Methods for Autosegmentation of Pediatric Brain Tumors", https://arxiv.org/abs/2401.08404
