# GDCA-DTI

## Geometry-guided Directional Cross-modal Attention for Drug–Target Interaction Prediction

Official implementation of **GDCA-DTI**, a geometry-guided directional cross-modal attention framework for drug–target interaction (DTI) prediction.

---

# Overview

Drug–target interaction (DTI) prediction is an essential task in computational drug discovery and plays an important role in candidate drug prioritization.

Despite recent advances in deep learning, accurate DTI prediction remains challenging due to:

- heterogeneous molecular representations between drugs and proteins;
- sparse interaction information;
- difficulty in modeling asymmetric drug–protein interaction patterns;
- incomplete or uncertain molecular structural information.

Existing approaches often combine heterogeneous molecular features before interaction modeling or employ symmetric interaction mechanisms, which may limit the effective utilization of complementary molecular information.

To address these challenges, we propose:

**GDCA-DTI: Geometry-guided Directional Cross-modal Attention for Drug–Target Interaction Prediction**

GDCA-DTI preserves complementary molecular views before interaction learning and explicitly models directional information exchange between drug and protein modalities.

The framework integrates:

- atom-level drug topology;
- motif-level drug semantics;
- protein sequence representations;
- protein structural geometry.

---

# Method Overview

GDCA-DTI contains four major components:

1. Multi-perspective molecular representation
2. Common-space projection
3. Directional cross-modal interaction learning
4. Geometry-guided interaction aggregation


The overall pipeline is:

```
Drug Input
    |
    |
--------------------------------
|                              |
Atom-level Representation   Motif-level Representation
|                              |
--------------------------------
                |
        Drug Molecular Features


Protein Input
    |
    |
--------------------------------
|                              |
Sequence Representation    Structure Representation
|                              |
--------------------------------
                |
        Protein Molecular Features


                |
      Common-space Projection
                |
                |
----------------------------------------
|                                      |
Drug ← Protein Attention       Protein ← Drug Attention
|                                      |
----------------------------------------
                |
 Geometry-guided Interaction Learning
                |
 Interaction-aware Aggregation
                |
 Drug–Target Interaction Prediction
```

---

# Multi-perspective Molecular Representation

## Drug Representation

GDCA-DTI models drugs from two complementary perspectives.

### Atom-level Representation

Atom-level representations capture:

- molecular topology;
- atomic properties;
- local chemical environments.

These features describe detailed molecular graph information.

---

### Motif-level Representation

Motif-level representations capture:

- functional substructures;
- chemical fragments;
- higher-level molecular patterns.

The combination of atom-level and motif-level information enables hierarchical drug representation learning.

---

# Protein Representation

Protein information is modeled from both sequence and structural perspectives.

## Sequence-level Representation

Protein sequence representations are extracted using pretrained protein language models.

These representations provide semantic information from amino acid sequences.

---

## Structure-level Representation

Structural representations are derived from molecular geometry, including:

- residue distance information;
- contact relationships;
- geometric constraints.

These structural features provide additional guidance for interaction modeling.

---

# Directional Cross-modal Attention

Unlike conventional symmetric feature fusion, GDCA-DTI explicitly models asymmetric interaction directions between drugs and proteins.

Two directional pathways are constructed:

\[
Drug \leftarrow Protein
\]


and


\[
Protein \leftarrow Drug
\]


Here:

\[
X \leftarrow Y
\]

indicates that the representation of **X** is updated by aggregating information from **Y**.

Specifically:

- X provides the query representation;
- Y provides key and value representations.


This directional design enables GDCA-DTI to capture asymmetric information exchange between drug and protein modalities.

---

# Geometry-guided Interaction Learning

Structural information is incorporated into interaction learning through geometry-guided constraints.

The framework utilizes:

- molecular distance information;
- protein structural geometry;
- contact-based structural relationships.

Geometry-derived priors are introduced into structural interaction learning to provide additional guidance for cross-modal interaction modeling.

---

# Repository Structure

```
GDCA-DTI/
│
├── dataset/
│   ├── BindingDB/
│   └── BiosNap/
│
├── drug/
│   ├── features.py
│   ├── model_atom_level.py
│   ├── model_motif_level.py
│   ├── mol_graph.py
│   └── utils.py
│
├── Protein/
│   ├── extract_protein_features.py
│   ├── protein_feature_extractor.py
│   └── protein_3d_extractor.py
│
├── dataset_loader.py
├── drug_encoder.py
├── interaction_model.py
├── trainer.py
├── train_dti2_kfold.py
├── map_motif_names.py
└── README.md
```

---

# Installation

## Requirements

Recommended environment:

```
Python >= 3.9

PyTorch >= 2.0

CUDA >= 11.7
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Main dependencies include:

```
torch
numpy
scikit-learn
rdkit
transformers
biopython
tqdm
```

---

# Dataset Preparation

GDCA-DTI is evaluated on:

- BindingDB
- BioSNAP


Each dataset should contain:

```
drug_id

protein_id

drug structure information

protein sequence information

interaction label
```

Recommended directory:

```
dataset/

├── BindingDB

└── BiosNap
```

---

# Feature Preparation

## Drug Feature Extraction

Drug representations include:

- atom-level molecular features;
- motif-level molecular features;
- molecular graph information.


---

## Protein Sequence Feature Extraction

Protein sequence features are extracted using pretrained protein language models.

The extracted representations contain:

- residue-level embeddings;
- protein semantic features.


---

## Protein Structure Feature Extraction

Structural information is processed into:

- geometric features;
- distance matrices;
- contact representations.

---

# Training

## Warm-start

```bash
python train_dti2_kfold.py \
--dataset BindingDB \
--split_type warm_start
```

---

## Drug Cold-start

```bash
python train_dti2_kfold.py \
--dataset BindingDB \
--split_type cold_start_drug
```

---

## Protein Cold-start

```bash
python train_dti2_kfold.py \
--dataset BindingDB \
--split_type cold_start_protein
```

---

# Evaluation

GDCA-DTI evaluates performance using:

- AUROC;
- AUPRC;
- Accuracy;
- Precision;
- Recall;
- F1-score.


Evaluation scenarios include:

| Setting | Description |
|---|---|
| Warm-start | Known drugs and proteins appear in training and testing |
| Drug cold-start | Test drugs are completely unseen during training |
| Protein cold-start | Test proteins are completely unseen during training |

---

# Ablation Studies

We perform comprehensive ablation studies to investigate the contribution of molecular representations, structural information, and directional geometry modeling.

| Variant | Description |
|---|---|
| 1D-only | Retain only semantic interaction learning based on motif-level drug representations and protein sequence representations. |
| 3D-only | Retain only structural interaction learning based on atom-level drug representations and protein structural representations. |
| w/o Atom | Remove atom-level drug representations. |
| w/o Motif | Remove motif-level drug representations. |
| w/o Sequence (w/o ESM) | Remove ESM-based protein sequence representations. |
| w/o Contact Graph | Remove contact graph structural encoding. |
| w/o 3D | Remove structural geometric information. |
| w/o Residue Exposure Prior | Remove residue exposure geometric prior in directional interaction learning. |
| w/o Functional-Group Geometry Prior | Remove functional-group geometry prior in directional interaction learning. |
| Shared Geometry Prior | Replace direction-specific geometry priors with shared parameters. |

---

# Robustness Analysis

GDCA-DTI is further evaluated under incomplete and uncertain molecular information.

## Structural Missingness Analysis

Structural information is progressively removed to evaluate robustness under incomplete structural inputs.

---

## Input Perturbation Analysis

Molecular representations are perturbed using:

- Gaussian noise;
- masking;
- feature dropout;
- structural corruption.

These experiments evaluate the robustness of GDCA-DTI under uncertain molecular representations.

---

# Citation

If you use GDCA-DTI in your research, please cite:

```bibtex
@article{GDCA_DTI,
  title={Geometry-guided Directional Cross-modal Attention for Drug--Target Interaction Prediction},
  journal={Bioinformatics},
  year={2026}
}
```

---

# Contact

For questions, discussions, or bug reports, please open an issue in this repository.