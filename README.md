# 🦴 Unsupervised Bone Structure Segmentation from X-ray Images

An unsupervised approach to bone structure extraction from musculoskeletal X-ray images **without requiring ground truth labels or manual annotations**. This project implements and compares multiple classical computer vision and machine learning techniques for medical image segmentation.

## 📋 Overview

Medical image segmentation typically requires extensive labeled datasets, which are expensive and time-consuming to create. This project demonstrates that meaningful bone structure extraction can be achieved using **unsupervised methods** that rely solely on image intensity patterns and statistical properties.

### Key Features

- **No Ground Truth Required**: All methods work without labeled training data or manual masks
- **Multiple Segmentation Approaches**: Implements 4 different unsupervised techniques
- **Ensemble Voting System**: Combines multiple methods for robust segmentation
- **Comprehensive Analysis**: Includes coverage metrics and morphological analysis
- **MURA Dataset Compatible**: Tested on Stanford's MURA musculoskeletal radiograph dataset

## 🔬 Methods Implemented

### 1. Multi-Otsu Thresholding
Automatically determines optimal threshold values by maximizing inter-class variance across multiple intensity classes. Separates the image into background, soft tissue, and bone regions.

```
Image → Gaussian Blur → Multi-Otsu (3 classes) → Select Brightest Class → Morphological Cleanup
```

### 2. K-Means Clustering
Groups pixels into clusters based on intensity similarity using unsupervised machine learning. The brightest cluster is identified as bone tissue.

```
Image → Pixel Flattening → K-Means (k=3) → Cluster Assignment → Select Bone Cluster → Refinement
```

### 3. Adaptive Intensity Segmentation
Uses percentile-based thresholding within the detected body region, adapting to local intensity variations in the X-ray.

```
Image → Foreground Detection → Percentile Analysis → Adaptive Threshold → Mask Generation
```

### 4. Ensemble Voting
Combines all methods through a democratic voting system. A pixel is classified as bone only if a majority of methods agree.

```
[Multi-Otsu, K-Means, Adaptive] → Vote Map → Threshold (≥2 votes) → Final Mask
```

## 📊 Pipeline Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        INPUT: X-ray Image                           │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    PREPROCESSING                                     │
│  • CLAHE Enhancement (Contrast Limited Adaptive Histogram Equal.)   │
│  • Foreground/Background Separation                                  │
│  • Noise Reduction (Gaussian Blur)                                   │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
            ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
            │ Multi-Otsu  │ │  K-Means    │ │  Adaptive   │
            │ Threshold   │ │  Clustering │ │  Intensity  │
            └─────────────┘ └─────────────┘ └─────────────┘
                    │               │               │
                    └───────────────┼───────────────┘
                                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      ENSEMBLE VOTING                                 │
│              Pixel classified as bone if ≥2 methods agree           │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      POST-PROCESSING                                 │
│  • Morphological Operations (Opening/Closing)                        │
│  • Connected Component Analysis                                      │
│  • Small Region Removal                                              │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   OUTPUT: Binary Bone Mask                          │
└─────────────────────────────────────────────────────────────────────┘
```

## 🚀 Getting Started

### Prerequisites

```bash
Python 3.8+
```

### Installation

1. Clone the repository:
```bash
git clone https://github.com/Farid145678/Bone-Image-Segmentation.git
cd Bone-Image-Segmentation
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

### Dependencies

```
opencv-python>=4.5.0
numpy>=1.21.0
matplotlib>=3.4.0
scikit-learn>=1.0.0
scikit-image>=0.18.0
```

## 💻 Usage

### Basic Usage

```python
from bone_segmentation import extract_bone_structure

# Load your X-ray image
import cv2
img = cv2.imread('path/to/xray.png', cv2.IMREAD_GRAYSCALE)

# Extract bone structure using ensemble method
mask = extract_bone_structure(img, method='ensemble', visualize=True)
```

### Available Methods

```python
# Individual methods
mask_otsu = extract_bone_structure(img, method='otsu')
mask_kmeans = extract_bone_structure(img, method='kmeans')
mask_adaptive = extract_bone_structure(img, method='adaptive')

# Ensemble (recommended)
mask_ensemble = extract_bone_structure(img, method='ensemble')
```

### Running the Notebook

```bash
jupyter notebook bone_segmentation_final.ipynb
```

## 📈 Results & Analysis

### Method Comparison

| Method | Pros | Cons |
|--------|------|------|
| Multi-Otsu | Fast, deterministic, good for high-contrast images | Sensitive to noise, may miss subtle bone regions |
| K-Means | Adapts to intensity distribution, handles varying contrast | Non-deterministic, computationally heavier |
| Adaptive | Robust to local intensity variations | May over-segment in noisy regions |
| Ensemble | Most robust, reduces individual method errors | Slower (runs all methods) |

## 📁 Project Structure

```
Bone-Image-Segmentation/
├── bone_segmentation_final.ipynb  # Main notebook with all implementations
├── README.md                       # Project documentation
├── requirements.txt                # Python dependencies
├── .gitignore                      # Git ignore rules
├── data/                           # Dataset directory (not tracked)
│   └── MURA-v1.1/                  # MURA dataset
│       ├── train/
│       └── valid/
└── models/                         # Saved models (if any)
```

## 📚 Dataset

This project uses the [MURA (MUsculoskeletal RAdiographs)](https://stanfordmlgroup.github.io/competitions/mura/) dataset from Stanford ML Group.

**Note**: The dataset is not included in this repository due to size constraints. Please download it separately from the official source.

### Supported Body Parts
- Elbow
- Finger
- Forearm
- Hand
- Humerus
- Shoulder
- Wrist

## 🔮 Future Improvements

- [ ] Add deep learning-based unsupervised methods (autoencoders, GANs)
- [ ] Implement region-growing algorithms
- [ ] Add support for DICOM format
- [ ] Create batch processing pipeline
- [ ] Add quantitative evaluation metrics (when ground truth available)
- [ ] GPU acceleration for K-Means clustering

## 📖 References

1. Otsu, N. (1979). A threshold selection method from gray-level histograms. *IEEE Transactions on Systems, Man, and Cybernetics*.
2. Rajpurkar, P., et al. (2017). MURA: Large Dataset for Abnormality Detection in Musculoskeletal Radiographs. *arXiv preprint*.
3. Gonzalez, R. C., & Woods, R. E. (2018). *Digital Image Processing* (4th ed.). Pearson.

## 👨‍💻 Author

**Farid Gahramanov**
- GitHub: [@Farid145678](https://github.com/Farid145678)
