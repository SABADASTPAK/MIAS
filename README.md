# MIAS Preprocessing for YOLO Detection

This repository contains a preprocessing pipeline for the MIAS mammogram dataset to inspect which preprocessing operations might improve the performance of AI models.

## 📁 Contents

- `mias_preprocessing.ipynb` — Google Colab notebook that:
  - Loads RGB images from the MIAS dataset
  - Reads YOLO-formatted bounding box annotations
  - Applies various preprocessing techniques like:
    - CLAHE (Contrast Limited Adaptive Histogram Equalization)
    - Gaussian and Non-Local Means denoising
    - Image sharpening (Laplacian, unsharp masking)
    - Intensity normalization
  - Visualizes bounding boxes before and after preprocessing

## ✅ Key Features

- Bounding boxes remain aligned with images — no spatial transforms
- Helps determine which preprocessing improves model performance

## 🖼 Sample Visualizations

Before and after images are shown side-by-side with bounding boxes for qualitative comparison.

## 📦 Requirements

- Python 3.7+
- OpenCV (`cv2`)
- Matplotlib
- Numpy

pip install opencv-python matplotlib numpy
