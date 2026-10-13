# Project 01 — Fluorescent Neuronal Cell Segmentation

Automated segmentation of fluorescent neuronal cells using U-Net, developed through classical image-processing baselines, deep learning and evaluation across multiple fluorescence collections.

This project explores how segmentation performance changes with data augmentation, model ensembling and training across different fluorescence collections.

## Dataset

**Fluorescent Neuronal Cells v2 (FNC)** — University of Bologna

- Three fluorescence collections: green, red and yellow.
- 1,874 images in total, with 750 labelled images containing ground-truth segmentation masks.
- Dataset: https://amsacta.unibo.it/id/eprint/7347/

## Results

The final model combines V1, V2 and V4 predictions using a threshold of 0.45. Performance was evaluated on held-out test sets that were not used during model development.

| Test collection | Test images | Mean Dice | Median Dice |
| --------------- | ----------- | --------- | ----------- |
| Green           | 70          | 0.651     | 0.706       |
| Red             | 46          | 0.620     | 0.662       |
| Yellow          | 70          | 0.581     | 0.680       |

The classical thresholding and size-filtering baseline achieved a mean Dice score of 0.414 on the green test set.

The final model improved mean Dice by **0.237 points (23.7 percentage points)**, equivalent to a **57.2% relative improvement** over the classical baseline.

## Model Development

| Version | Description                                            | Green validation Dice |
| ------- | ------------------------------------------------------ | --------------------- |
| V1      | U-Net trained on green images without augmentation     | 0.612                 |
| V2      | U-Net trained on green images with augmentation        | 0.622                 |
| V3      | V1 and V2 ensemble, threshold 0.52                     | 0.636                 |
| V4      | U-Net trained across green, red and yellow collections | 0.608                 |
| Final   | V1, V2 and V4 ensemble, threshold 0.45                 | 0.645                 |

*Validation scores are reported separately from the final held-out test results.*

## Model Architecture

- **Architecture:** U-Net with four encoder levels, a bottleneck and four decoder levels with skip connections.
- **Parameters:** Approximately 31 million.
- **Input:** Single-channel fluorescence images, processed as 256 x 256 patches.
- **Output:** Binary neuronal segmentation mask.
- **Loss:** Equal-weighted binary cross-entropy and Dice loss.
- **Optimiser:** Adam, with a learning rate of 0.0001.

## Input Representation

The input channel was selected according to the dominant fluorescence signal in each collection:

- **Green:** green channel (index 1).
- **Red:** red channel (index 0).
- **Yellow:** average of the red and green channels.

## Repository Structure

project-01-Neuronal-Segmentation/
├── 01_data_exploration.ipynb
├── 02_unet_segmentation.ipynb
├── requirements.txt
└── README.md


## Reproducing the Environment

Python 3.9 or later required.

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Download the dataset from the University of Bologna repository linked above. Dataset files and trained model weights are not included in this repository.

## Limitations and Future Work

- Performance varies across images, with complete segmentation failures in some test cases.
- The yellow collection has the highest variability in Dice scores.
- Alternative loss weighting could be investigated to improve detection of sparse neuronal structures.
- RGB input has not yet been compared systematically with collection-specific channel extraction.
- Performance has not yet been compared directly with Cellpose under the same test conditions.
