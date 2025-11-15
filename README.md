**MIAS Mammography Dataset – Preprocessing Pipeline for YOLOv8**

This repository contains a complete preprocessing pipeline for preparing the Mini-MIAS mammography dataset for training object detection models, specifically YOLOv8.
All steps—from downloading the dataset to generating YOLO annotations and applying image enhancement—are implemented through a set of Jupyter notebooks.

**1. Dataset Overview**

The MIAS (Mammographic Image Analysis Society) dataset contains 322 digital mammography images in PGM (1024×1024) format.
Each image is accompanied by metadata stored in Info.txt, including:

- Image reference ID (mdbxxx)

- Background tissue type: Fatty (F), Fatty-Glandular (G), Dense (D)

- Abnormality type: CIRC, SPIC, MISC, ARCH, ASYM, CALC, NORM

- Severity: Benign (B) or Malignant (M)

- Coordinates of the abnormality center

- Approximate radius (in pixels) of a circle enclosing the abnormality

In this project, only the following cases were used:

- Circumscribed masses (CIRC)

- Normal images (NORM)

All other abnormality types were excluded.

**2. Dataset Organization and Download**

The MIAS dataset was downloaded from the University of Essex server.
Google Drive was mounted in Google Colab and the dataset was extracted into:
```
miniMIAS_Dataset/
├── Info.txt
└── pgm/
    └── (Original PGM images)

```
The notebook download_MIAS_dataset.ipynb handles this process automatically.

**3. Metadata Processing (Info.txt → DataFrame)**

To facilitate data management, the metadata file Info.txt was converted into a structured Pandas DataFrame.
Unreliable rows (e.g., invalid coordinates) were removed.

Output file:
```
miniMIAS_Dataset/
└── Filtered_info.csv

```
Notebook: DataFrame.ipynb

**4. Image Format Conversion (PGM → JPG)**

Since YOLOv8 does not support PGM files, all images were converted to JPG and stored in:
```
MIAS/
└── Images/
    └── (JPG images)
```
**5. Generating YOLO Annotations**

The MIAS dataset describes tumors with center coordinates and a radius.
These circular annotations were converted into YOLO bounding boxes, normalized in the format:

class  x_center  y_center  width  height


- Benign tumors (B) → class 0

- Malignant tumors (M) → class 1

```

Mias_info_with_yolo.csv

```
Notebook: bbox_generation.ipynb

**6. Image Enhancement**
6.1 Median Filtering

Applied to:

- Reduce impulse noise

- Preserve edges and fine structures (e.g., microcalcifications)

6.2 Contrast Enhancement using CLAHE

Various tile grid sizes (4×4, 8×8) and clip limits (5–40) were tested.
Best performing settings:

- TileGridSize = (4,4), ClipLimit = 25–35

- TileGridSize = (8,8), ClipLimit = 15–25

Final enhanced images saved in:
```
MIAS/
└── CLAHE/

```
Notebook: different_CLAHEs.ipynb

**7. Grayscale-to-RGB Conversion**

YOLOv8 requires 3-channel RGB images.
All CLAHE-enhanced grayscale images were converted to RGB by duplicating channels.

Output:
```
MIAS/
└── CLAHE_RGB/

```
Notebook: grayscale_to_RGB.ipynb

**8. Final YOLOv8 Dataset Preparation**

The final dataset includes:

- Only Normal and Circumscribed Mass cases

- YOLO annotations for each image

- Enhanced RGB images

- Train/Validation/Test split

Output directory:
```
Yolo_dataset_clahe/
├── train/
├── valid/
├── test/
└── data.yaml
```

Notebook: dataset.ipynb

**9. Notes on Class Distribution**

The MIAS dataset is very small, and imbalance exists:

Category	Count
Normal images	207
Circumscribed masses	22
Benign	4
Malignant	18

Due to the small number of positive samples, YOLOv8 performance is expected to be limited even with augmentation.

**10. Random Train/Val/Test Experiment**

A random split resulted in:

- Train: 161 images

- Validation: 45 images

- Test: 24 images

However, positive samples were poorly distributed:

- Only 17 tumor samples in training

- Only 1 tumor sample in testing

YOLOv8n was trained for:

- 220 epochs

- Image size: 640

- Batch size: 16

- Pretrained COCO weights

This experiment demonstrated that imbalanced data leads to unstable detection results, confirming the need for a more structured split or additional datasets.

**11. Repository Structure**
notebooks/
    download_MIAS_dataset.ipynb
    DataFrame.ipynb
    bbox_generation.ipynb
    different_CLAHEs.ipynb
    grayscale_to_RGB.ipynb
    dataset.ipynb

miniMIAS_Dataset/
MIAS/
Yolo_dataset_clahe/

**12. License**

This project is for research and educational purposes.
The MIAS dataset is owned and distributed by the Mammographic Image Analysis Society.
