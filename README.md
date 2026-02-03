# Molecule Multi-Taste Prediction

Predicting multiple taste properties (Sweet, Bitter, Umami) of molecules using deep learning and molecular embeddings.


## Files

| File | Description |
|------|-------------|
| `data.ipynb` | Generate molecular embeddings (Mol2Vec, ChemBERTa, MACCS, RDKit) |
| `eda.ipynb` | Exploratory data analysis of ChemTastesDB and embeddings |
| `model.ipynb` | Initial model training experiments |
| `modelv2.ipynb` | Final model with PCA comparison and multi-branch architecture |
| `exp.ipynb` | Rough work, taste description → class mapping |
| `taste_mapping.json` | Taste description to class mapping |

## Usage

1. Generate embeddings: Run `data.ipynb`
2. Train model: Run `modelv2.ipynb`
3. eda.ipynb for EDA
4. Inference: Use `predict_taste()` function with Mol2Vec and ChemBERTa embeddings


## Dataset

- **Source**: ChemTastesDB
- **Labels**: Sweet, Bitter, Umami (multi-label, a molecule can have multiple tastes)
- **Split**: 70% train, 15% validation, 15% test

## Model Architecture

### Base Model (Embeddings Only)

A feed-forward neural network with shared layers and separate output heads for each taste:

```
Input (1068d: Mol2Vec 300d + ChemBERTa 768d)
    ↓
Shared Layers: 512 → 256 → 128 (BatchNorm + ReLU + Dropout)
    ↓
Output Heads: 3 separate heads (128 → 32 → 1) for Sweet, Bitter, Umami
```

- **Loss**: Focal Loss with class weights (handles imbalanced Umami class)
- **Optimizer**: AdamW with weight decay
- **Early stopping**: Based on validation AUROC

### PCA Comparison on chembert+mol2vec model


| Metric | Non-PCA | PCA (95% variance) | Difference |
|--------|---------|-------------------|------------|
| Macro AUROC | 0.9288 | 0.9291 | +0.0003 |
| Macro F1 | 0.7994 | 0.7706 | -0.0288 |
| Exact Match | 0.7007 | 0.6696 | -0.0311 |


### Multi-Branch Architecture (Proposed)

To incorporate fingerprints alongside embeddings, we designed a two-branch architecture:

```
Branch A: Embeddings (1068d)          Branch B: Fingerprints (~200d)
[Mol2Vec + ChemBERTa]                 [MACCS + RDKit descriptors]
        ↓                                      ↓
   512 → 256                              256 → 128
        ↓                                      ↓
        └──────────── Concatenate ─────────────┘
                          ↓
                    Fusion: 384 → 128
                          ↓
              Output Heads (3 × 128 → 32 → 1)
```

**Key design choices**:
- Separate branches for continuous (embeddings) vs binary/mixed (fingerprints) features
- StandardScaler for embeddings, RobustScaler for RDKit, no scaling for binary MACCS
- Removed highly correlated features (>0.95) and low variance features before training
- Focal loss to handle class imbalance