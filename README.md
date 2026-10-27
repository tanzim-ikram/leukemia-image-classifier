# ALL Leukemia CNN Classifier

A convolutional neural network for classifying microscopy images of blood cells into four Acute Lymphoblastic Leukemia (ALL)-related categories — **Benign, Early, Pre, and Pro** — using the [Kaggle `mehradaria/leukemia`](https://www.kaggle.com/datasets/mehradaria/leukemia) dataset.

> ⚠️ **Research/educational prototype only.** This model is trained on a single public dataset with no clinical validation and should not be used for diagnostic purposes. See [Limitations](#limitations) below.

## Overview

| | |
|---|---|
| **Task** | 4-class image classification (Benign / Early / Pre / Pro) |
| **Dataset** | Kaggle `mehradaria/leukemia`, `Original` image set |
| **Framework** | TensorFlow / Keras |
| **Architecture** | 6× Conv2D + MaxPooling blocks → Flatten → Dense(512) → Dropout(0.5) → Dense(4, softmax) |
| **Parameters** | ~1.28M |
| **Input size** | 224 × 224 × 3 |
| **Test accuracy** | ~91.5% |

## Dataset

The dataset is downloaded automatically via the `opendatasets` package, which wraps the Kaggle API. You'll need a Kaggle account and API token.

```
dataset_url = 'https://www.kaggle.com/datasets/mehradaria/leukemia'
```

Images are organized into 4 class folders (`Benign`, `Early`, `Pre`, `Pro`) and split **70% train / 15% validation / 15% test** by copying files into `train/`, `val/`, and `test/` subdirectories.

- Train: 2,277 images
- Validation: 486 images
- Test: 493 images

## Model Architecture

```
Conv2D(64)  → MaxPooling2D
Conv2D(64)  → MaxPooling2D
Conv2D(128) → MaxPooling2D
Conv2D(128) → MaxPooling2D
Conv2D(256) → MaxPooling2D
Conv2D(256) → MaxPooling2D
Flatten
Dense(512, relu)
Dropout(0.5)
Dense(4, softmax)
```

Trained for 25 epochs with Adam optimizer and categorical cross-entropy loss. Data augmentation (rotation, shift, shear, zoom, horizontal flip) is applied to the training set only; validation and test sets are rescaled only.

## Results

| Metric | Value |
|---|---|
| Validation accuracy | ~90.5% |
| Test accuracy | ~91.5% |

A confusion matrix is generated for the test set. **Note:** the precision/recall/F1 calculation in this notebook currently derives values from only the first two rows/columns of the 4×4 confusion matrix (a binary-classification pattern), which does not correctly summarize 4-class performance. Use `sklearn.metrics.classification_report` for correct per-class and macro/weighted metrics before citing these numbers anywhere.

## Requirements

```
tensorflow
numpy
pandas
matplotlib
seaborn
scikit-learn
opendatasets
patool
```

## Usage

1. Add your Kaggle API credentials (`KAGGLE_USERNAME`, `KAGGLE_KEY`) via Colab Secrets or environment variables.
2. Run the notebook top to bottom — it downloads the dataset, builds the train/val/test split, trains the CNN, and evaluates it.
3. Outputs include a training/validation loss curve, a confusion matrix heatmap, and evaluation metrics.

## Limitations

- **No fixed random seed** — results are not exactly reproducible across runs.
- **Train/val/test split is not shuffled** before slicing, which risks an unrepresentative or leaky split if filenames carry patient/batch ordering.
- **Precision/recall/F1 are computed incorrectly** for the 4-class setting (see Results above).
- The notebook's own comparison notes that training on **unsegmented** ("Original") images outperformed **segmented** (isolated-cell) images by a wide margin (~92% vs. ~60%). This is worth investigating further (e.g., with Grad-CAM) — it may indicate the model is partly relying on background or imaging artifacts rather than cell morphology.
- No cross-validation, class-balance analysis, or external test set is included.
- Not validated against any clinical ground truth beyond the dataset's own labels.

## License

Specify a license (e.g., MIT) before publishing.
