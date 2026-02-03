Exp.ipynb - used for rough work and checking of dataset. also contains code for mapping taste description to taste classes
data.ipynb - code to generate embeddings
eda.ipynb - code for eda of chemtastesDB and the embeddings generated via chemtastesDB such as Mol2Vec, ChemBERT, rdkit, maccs, etc
Model.ipynb, modelv2.ipynb - code for training the models from scratch
taste_mapping.jsom - mapping of taste description to taste classes for inspection, verification


Metric                  Non-PCA        PCA       Diff
--------------------------------------------------
macro_AUROC              0.9288     0.9291    +0.0003
macro_F1                 0.7994     0.7706    -0.0288
exact_match_accuracy     0.7007     0.6696    -0.0311
  Best AUROC: 0.9249

Metric                  Non-PCA        PCA       Diff
--------------------------------------------------
macro_AUROC              0.9288     0.9291    +0.0003
macro_F1                 0.7994     0.7706    -0.0288
exact_match_accuracy     0.7007     0.6696    -0.0311