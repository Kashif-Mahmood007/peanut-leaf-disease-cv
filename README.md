# Peanut leaf disease classification with transfer learning under leakage-controlled cross-validation

Code, data splits and results for the study

> Mahmood, K., Abbas, Q., Habiba, U., Ilyas, M., Alghamdi, T., Almoamari, H. & Ramzan, M.
> **Deep learning-based peanut leaf disease classification using transfer learning under leakage-controlled cross-validation.**
> *Scientific Reports* (under review).

| | |
|---|---|
| **Trained models** | Hugging Face: [Kashif-Mahmood007/peanut-leaf-disease-classifiers](https://huggingface.co/Kashif-Mahmood007/peanut-leaf-disease-classifiers) |
| **Dataset** | Sasmal *et al.*, groundnut leaf dataset, Mendeley Data, version 2: [https://doi.org/10.17632/x6x5jkk873.2](https://doi.org/10.17632/x6x5jkk873.2) (CC BY 4.0) |
| **Archived version of this repository** | Zenodo (DOI added on release) |

## Overview

Four ImageNet-pretrained backbones (ResNet-50, EfficientNet-B0, EfficientNet-B4 and ConvNeXt-Base) are trained with two
training recipes (P and B) to classify close-up field photographs of peanut (groundnut) leaves into five classes:
*Alternaria* leaf spot, healthy, leaf spot (early and late), rosette and rust (1,720 images).

The public dataset contains exact copies and many near-identical photographs. They are grouped with MD5 checksums,
perceptual hashes (pHash, dHash) and DINOv2 image embeddings, and all models are evaluated with **stratified group
five-fold cross-validation**, so that no test image has a near-identical copy in its training or validation data.
The repository also contains an ablation of recipe P, a comparison with a naive split that ignores the duplicates,
robustness tests under nine simulated image degradations, and Grad-CAM visualisations.

## Main results

Test accuracy, mean ± SD over the five folds; pooled = correct predictions over all 1,720 out-of-fold test images.

| Backbone | Recipe | Accuracy (%) | Macro-F1 (%) | Pooled correct |
|---|---|---|---|---|
| ResNet-50 | P | 97.90 ± 0.65 | 97.83 ± 0.62 | 1,684 / 1,720 |
| ResNet-50 | B | 97.95 ± 0.35 | 98.17 ± 0.52 | 1,685 / 1,720 |
| EfficientNet-B0 | P | 97.45 ± 0.72 | 97.72 ± 0.89 | 1,676 / 1,720 |
| EfficientNet-B0 | B | 95.41 ± 0.77 | 95.27 ± 1.78 | 1,641 / 1,720 |
| EfficientNet-B4 | P | 98.67 ± 0.50 | 98.57 ± 0.97 | 1,697 / 1,720 |
| EfficientNet-B4 | B | 94.92 ± 0.99 | 90.77 ± 4.00 | 1,632 / 1,720 |
| ConvNeXt-Base | P | 98.11 ± 0.99 | 98.10 ± 0.59 | 1,688 / 1,720 |
| **ConvNeXt-Base** | **B** | **99.31 ± 0.42** | **99.16 ± 0.52** | **1,708 / 1,720** |

* ConvNeXt-Base (recipe B) was significantly more accurate than every other configuration (McNemar exact test,
  Holm-adjusted p ≤ 0.035).
* Removing data augmentation was the only ablation that significantly reduced accuracy (−1.98 points; adjusted p = 0.0003).
* A naive split that ignores near-duplicates put a near-identical copy of 52.9% of the test images into training and
  inflated ResNet-50 accuracy by 1.22 percentage points.

All results come from one dataset collected in a single region; independent field validation is still required.

## Repository contents

| Path | Content |
|---|---|
| `notebooks/` | The six Google Colab notebooks that produced every result, with the outputs of the reported runs (run in order, see below) |
| `data_splits/` | `cv_splits.csv` (group-aware 5-fold split: role of every image in every fold), `cv_splits_naive.csv` (naive split), `cv_splits_meta.json` (grouping rules and settings), `index_original.csv` (image index with checksums, EXIF fields and duplicate groups) |
| `audit/` | Duplicate audit: linked pairs, threshold sensitivity, contact sheets (Supplementary Figs. S1–S4) |
| `results/` | One folder per configuration and fold (exported from the Drive folder `04_results/`): `config.json`, `metrics.json`, `log.csv` (training curve) and `predictions.csv` (per-image predictions and class probabilities) |
| `tables/` | Tables of the paper (CSV) |
| `figures/` | Figures of the paper (PNG and PDF) |

The images themselves are not included; download them from Mendeley Data (link above).

## Reproducing the results

The notebooks run on Google Colab (T4 GPU) and keep all data and results in one Google Drive folder
(`MyDrive/Peanut_Revision`). Run each with **Runtime → Run all**; long notebooks can be interrupted and resumed.

| Notebook | What it does | Time (T4) |
|---|---|---|
| `01_Data_Preparation` | Download check, integrity and EXIF audit, 448-px image cache, duplicate audit, group-aware and naive splits | ≈ 20–40 min |
| `02_Train_Backbones_CV` | 4 backbones × 2 recipes × 5 folds, robustness tests for recipe P | ≈ 4 h 15 min |
| `03_Ablation_and_Leakage` | Ablation of recipe P (9 variants) and naive-split experiment | ≈ 4 h |
| `04_Analysis_Figures_Statistics` | Tables, statistics (McNemar, Holm, bootstrap CIs), figures, Grad-CAM | ≈ 15 min |
| `05_Retrain_ConvNeXtB_Weights` | Re-trains ConvNeXt-Base (recipe B) with weight saving | ≈ 45 min |
| `06_Publish_Models_HuggingFace` | Checks the saved weights against the recorded predictions and uploads them | ≈ 10 min |

Every fold uses a fixed seed (the same for all configurations), so all comparisons are paired. GPU training with
mixed precision is not guaranteed to be bit-for-bit repeatable across hardware and library versions; the re-run in
Notebook 05 reproduced all 1,720 predictions of the reported ConvNeXt-Base run.

### Using the trained models

```python
from huggingface_hub import snapshot_download
import sys
root = snapshot_download("Kashif-Mahmood007/peanut-leaf-disease-classifiers")
sys.path.insert(0, root)
from inference import load_model, predict_proba, CLASSES

net, cfg = load_model("convnext_base__B", fold=0, root=root)
prob = predict_proba(net, cfg, ["leaf.jpg"])
print(CLASSES[int(prob[0].argmax())], float(prob[0].max()))
```

Weights are available for the four recipe-P models and ConvNeXt-Base with recipe B (five fold models each). To
reproduce the paper's numbers, evaluate a fold model only on its own test fold (`data_splits/cv_splits.csv`).

## Requirements

Python ≥ 3.10 with the packages in `requirements.txt` (Google Colab provides all of them except `imagehash`, which
Notebook 01 installs). The reported experiments used Python 3.13, PyTorch 2.11.0, torchvision 0.26.0, NumPy 2.1.3,
pandas 2.2.3, scikit-learn 1.6.1, SciPy 1.16.3 and ImageHash 4.3.2 on an NVIDIA Tesla T4.

## Licence

Code: MIT (see `LICENSE`). Split files, results, tables and figures: CC BY 4.0. The dataset is licensed CC BY 4.0
by its authors; please cite it when using the images.

## Citation

Please cite the article (details will be added after publication) and the archived version of this repository
(see `CITATION.cff`), as well as the dataset:

Sasmal, B. *et al.* A novel groundnut leaf dataset for detection and classification of groundnut leaf diseases.
*Data Brief* **55**, 110763 (2024).
