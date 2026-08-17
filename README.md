# CFB-GBM
[![Dataset on TCIA](https://img.shields.io/badge/Dataset-TCIA-1C7BB8?style=flat-square&logo=databricks&logoColor=white)](https://www.cancerimagingarchive.net/collection/cfb-gbm/)
[![Model on HuggingFace](https://img.shields.io/badge/%F0%9F%A4%97%20GTV%20Model%20Segmentation-SegCFB--GBM-FFD21E?style=flat-square)](https://huggingface.co/AlexLECLERCQ/SegCFB-GBM)


This repository contains some of the scripts use to create the **CFB-GBM** dataset —
**C**entre **F**rançois **B**aclesse – **G**lio**b**lasto**m**a — a longitudinal
brain MRI/CT cohort of glioblastoma patients.

## Overview

The scripts and folders below cover the distinct steps used to build the
CFB-GBM dataset and its segmentation model. They do not follow a strict
order: each one handles a specific task in the dataset construction.

- **DICOM preprocessing and registration**
  ([`01_Preprocessing_data.ipynb`](01_Preprocessing_data.ipynb),
  [`02_CTtoMRI.ipynb`](02_CTtoMRI.ipynb),
  [`02_MRItoMRI.ipynb`](02_MRItoMRI.ipynb)):
  convert the raw DICOM acquisitions into resampled NIfTI volumes. The
  preprocessing notebook handles DICOM sorting, conversion to NIfTI and
  renaming; the registration notebooks perform CT→MRI and inter-timepoint
  MRI→MRI registration inside 3D Slicer.
- **Skull stripping**
  ([`skull_striping/3. brain_extraction.ipynb`](skull_striping/3.%20brain_extraction.ipynb)):
  extracts the brain from the skull using ANTsPyNet.
- **Brain mask generation**
  ([`brain_mask_generation.ipynb`](brain_mask_generation.ipynb)):
  generates the brain masks from the skull-stripped volumes.
- **Tumor segmentation** ([`segmentation/`](segmentation)):
  - [`parse_brats2021_for_nnUNetv2.ipynb`](segmentation/parse_brats2021_for_nnUNetv2.ipynb)
    adapts the raw BraTS 2021 (Task 1) dataset to the nnU-Net v2 format.
  - [`generate_cfb-gbm_dataset_for_nnUNetv2.ipynb`](segmentation/generate_cfb-gbm_dataset_for_nnUNetv2.ipynb)
    adapts the CFB-GBM dataset to a format usable by nnU-Net v2.
  - [`README.md`](segmentation/README.md) contains the command-line
    instructions (pretraining, finetuning and inference).
- **RANO response assessment** ([`script_rano.ipynb`](script_rano.ipynb)):
  generates the RANO criteria CSV.


## Requirements & setup

The core Python environment is managed with [Poetry](https://python-poetry.org/),
targeting Python 3.10–3.11:

```bash
pip install poetry>=2.0
```

```bash
poetry install
```

Main dependencies: `torch` (CUDA 12.8 wheels), `nnunetv2`, `radiomics`
(PyRadiomics). See [`pyproject.toml`](pyproject.toml) for the full list.

Two stages rely on tools that are **not** installable via Poetry/pip and must
be set up separately:

- **Registration** (`02_CTtoMRI.ipynb`, `02_MRItoMRI.ipynb`) must be run inside
  [3D Slicer](https://www.slicer.org/)'s embedded Python environment
- **Skull stripping** (`skull_striping/3. brain_extraction.ipynb`) requires
  [`antspynet`](https://github.com/ANTsX/ANTsPyNet) and
  [`antspyx`](https://github.com/ANTsX/ANTsPy), which are not currently
  pinned in `pyproject.toml`.


## Citation

If you use this model, please cite the associated dataset paper and the CFB-GBM v2.0 dataset.

```bibtex
@article{leclercq_cfbgbm_v2,
  title   = {CFB-GBM v2.0: An Augmented Longitudinal Dataset for Multi-Modal Glioblastoma Segmentation, Radiomics, and RANO Progression Tracking},
  author  = {Leclercq, Alexandre G. and Moreau, Noémie N. and Audebert, Hugo and Nassar, Andros and Cochin, Thomas and Leleu, Thomas and Le Henaff, Loïc and Desmonts, Alexis and Poirier, Yoann and Dubru,  Aurélie and Guillemette, Laura and Lecoeur, Pascal and Lemasson, Kévin and Jaudet, Cyril and Bougleux, Sébastien and Hérault, Romain and Brunaud, Carole and Valable, Samuel and Stefan, Dinu and Raboutet, Charlotte and Batalla, Alain and Lacroix, Joëlle and Rouzier, Roman and Corroyer-Dulmont, Aurélien},
  journal = {Machine Learning for Biomedical Imaging (MELBA)},
  note    = {To appear}
}

@misc{TCIA,
  title = {Pre and Post Treatment {{MRI}} and Radiotherapy Plans of Patients with Glioblastoma: The {{CFB-GBM}} Cohort ({{CFB-GBM}})},
  shorttitle = {Pre and Post Treatment {{MRI}} and Radiotherapy Plans of Patients with Glioblastoma},
  author  = {Moreau, Noémie N., Leclercq, Alexandre G. and Desmonts, Alexis and Poirier, Yoann and Dubru, Aurélie and Guillemette, Laura and Lecoeur, Pascal and Lemasson, Kévin and Jaudet, Cyril and Brunaud, Carole and Valable, Samuel and Geffrelot, Julien and Stefan, Dinu and Leleu, Thomas and Raboutet, Charlotte and Le Henaff, Loïc and Batalla, Alain and Lacroix, Joëlle and Rouzier, Roman and Corroyer-Dulmont, Aurélien},
  year = {2025},
  publisher = {The Cancer Imaging Archive},
  doi = {10.7937/V9PN-2F72},
  url = {https://www.cancerimagingarchive.net/collection/cfb-gbm/},
  version = {3}
}
```

## License

This repository is released under the
[GNU General Public License v3.0 (GPL-3.0)](https://www.gnu.org/licenses/gpl-3.0.en.html)
license.
