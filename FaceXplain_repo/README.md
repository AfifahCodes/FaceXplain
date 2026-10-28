# FaceXplain: code and results

Code, split files and result tables for the paper
**"FaceXplain: A Landmark- and Occlusion-Validated Explainability Framework for Autism Spectrum Disorder Facial Image Classification"**
(under review; author names withheld for double-blind review). A permanent archive with a DOI will be added after acceptance.

> **Intended use.** This repository supports a methodological study of explanation methods.
> It is **not** a screening or diagnostic tool. The public image labels are not clinically verified,
> and the paper shows that non-facial cues contribute to the models' accuracy.

## What is (and is not) in this repository
| Included | Not included |
|---|---|
| Three Colab notebooks (outputs cleared) | The image dataset (children's faces; obtain it from the original source below) |
| The fixed train/validation/test split and the list of removed near-duplicates | Trained model weights (large; see the Zenodo record) |
| All result tables, per-image test predictions and run logs | Figures that show faces (example heatmaps, LIME overlays, masking examples) |
| Figures without faces (ROC/confusion, curves, averaged heatmaps) | |

## Dataset
The experiments use the ASD facial-image dataset on Mendeley Data (DOI [10.17632/f9dycfvwbt](https://data.mendeley.com/datasets/f9dycfvwbt); the record states that it was created from Kaggle data; 2,940 images of autistic and non-autistic children in train/valid/test folders). The record gives the licence as CC BY 4.0.
Expected folder layout, read from `DATA_DIR` by the notebooks:

```
THESIS DATA/
  train/  autistic/  non_autistic/
  valid/  autistic/  non_autistic/
  test/   autistic/  non_autistic/
```
`results/FaceXplain_clean_rerun/final_split.csv` lists the exact images used in each split after
removing 11 near-duplicate training images (64-bit difference hash, Hamming distance <= 5;
see `cross_split_duplicates.csv`). Paths inside the CSV files are the original Colab paths.

## How to reproduce (Google Colab, T4 GPU)
1. Put the dataset in Google Drive and set `DATA_DIR` and `OUT_DIR` in the first cell of each notebook.
2. Run the notebooks in this order (Runtime -> Run all):
   1. `notebooks/FaceXplain_clean_rerun.ipynb`: duplicate check, fixed split, 7 CNN baselines (VGG16 with 3 seeds). About 1 to 1.5 h.
   2. `notebooks/FaceXplain_original_sections.ipynb`: refined VGG16 (3 seeds), Grad-CAM / Grad-CAM++, LIME, MediaPipe landmarks, ViT-B/16, ViT occlusion test. Needs the split, `runs/` and baseline predictions from step 1.
   3. `notebooks/FaceXplain_masking_test.ipynb`: face-only / background-only test of the three unrefined VGG16 models (about 5 min). Needs the weights from step 1.
3. `results/FaceXplain_clean_rerun/` mirrors the `OUT_DIR` layout the notebooks produce (without `weights/`).

Seeds are fixed (42, 123, 2024; split seed 42), but GPU kernels are not fully deterministic, so re-runs can differ slightly from the reported tables.

## Main results (test set, n = 300)
| Model | Accuracy | ROC-AUC |
|---|---|---|
| VGG16, unrefined (3 seeds) | 86.6 +/- 0.8 % | 0.928 +/- 0.002 |
| VGG16, refined (3 seeds) | 86.2 +/- 0.5 % | 0.933 +/- 0.005 |
| ViT-B/16 (1 run) | 90.3 % | 0.966 |

Refined vs unrefined AUC: DeLong p = 0.77, 0.64, 0.22 (per seed). Eye-region activation (Grad-CAM / Grad-CAM++): 0.024 / 0.026, against a uniform-attention baseline of 0.092.
Occlusion, ROC-AUC (original / face only / background only): VGG16 0.928 / 0.835 / 0.712; ViT-B/16 0.966 / 0.952 / 0.870.

## Files
- `clean_results.xlsx`: baseline comparison and per-seed results.
- `masking_test_results.csv`, `original_sections/vit_occlusion.csv`: occlusion tests.
- `original_sections/`: refined VGG16 results, eye-region scores, LIME, landmark measurements and statistics, ViT results, DeLong p-values, per-image predictions.
- `test_predictions_*.csv`: per-image test probabilities of every baseline model.

## Citation and licence
Code is released under the MIT licence (see `LICENSE`). Citation details will be added after acceptance.
