# Fact-Checking System Implementation Summary

## Overview

I've implemented a two-stage fact-checking system for the COMP90042 project that consists of:

1. **Evidence Retrieval (Task 1)**: A two-stage retrieval pipeline using TF-IDF and transformer embeddings
2. **Claim Verification (Task 2)**: A transformer-based classification model that takes claims and evidence to predict labels

## Architecture Details

### Evidence Retrieval

I implemented a two-stage retrieval approach to efficiently find relevant evidence for each claim:

1. **First Stage (TF-IDF)**: 
   - Uses TF-IDF vectorization to quickly filter the full evidence corpus (1.2M passages)
   - Selects the top-k candidates (default: `TFIDF_TOP_K=500`) based on cosine similarity
   - Configuration: `TFIDF_MAX_FEATURES=20000` for vocabulary size

2. **Second Stage (Re-ranking)**:
   - Uses transformer embeddings from `sentence-transformers/all-MiniLM-L6-v2`
   - Re-ranks the TF-IDF candidates to select final top-k evidence passages (default: `EVIDENCE_FINAL_TOP_K=4`)
   - Employs mean pooling to create fixed-length vector representations
   - Transformer configuration: `TRANSFORMER_BATCH_SIZE=32`, `TRANSFORMER_MAX_LENGTH=512`

3. **Text Preprocessing Experiments**:
   - Lemmatization (`LEMMATIZE=True/False`): Tests showed better performance with lemmatization
   - Stop word removal (`REMOVE_STOP_WORDS=True/False`): Generally worse results with stop words removed
   - Combined with different `TFIDF_TOP_K` and `EVIDENCE_FINAL_TOP_K` values to find optimal settings

4. **Experimentation Framework**:
   - Implemented flexible experimentation with multiple K values (`EXPERIMENT_WITH_MULTIPLE_K=True/False`)
   - When enabled, tests `EVIDENCE_TOP_K_VALUES=[3, 4, 5, 6]` to find optimal evidence count
   - Progress tracking with `PROGRESS_REPORT_FREQUENCY=50` for monitoring long-running experiments

### Claim Verification

The claim verification component uses a DistilBERT model to classify claims as SUPPORTS, REFUTES, NOT_ENOUGH_INFO, or DISPUTED:

1. **Model Architecture**:
   - Base model: `distilbert-base-uncased`
   - Added custom attention mechanism for evidence weighting
   - Classification head on top of the CLS token representation
   - Configuration: `VERIFICATION_BATCH_SIZE=16`, `VERIFICATION_MAX_LENGTH=512`, `VERIFICATION_NUM_LABELS=4`
   - Custom `ClaimVerificationModel` class that inherits from `nn.Module` with:
     - Transformer encoder (`self.transformer`)
     - Dropout layer (`self.dropout`) with rate 0.1
     - Classification layer (`self.classifier`)
     - Evidence attention mechanism (`self.evidence_attention`)

2. **Training Approach**:
   - Two-phase training option: first on ground truth evidence, then with mixed evidence
   - Mixed evidence strategy: `USE_MIXED_EVIDENCE=False/True` with `MIX_RATIO=0.5` when enabled
   - Configurable class weighting to handle imbalanced data (`USE_CLASS_WEIGHTS=True/False`)
   - Evaluation based on F1-score instead of accuracy to better handle class imbalance
   - Experimented with different learning rates and batch sizes

3. **Evidence Handling**:
   - Multiple evidence processing with attention weighting
   - Weighted average of evidence-specific predictions based on attention scores
   - Fall back to NOT_ENOUGH_INFO when no evidence is found

## Experiments & Findings

### Evidence Retrieval Performance

- Best F-score on development set: 0.1734 with `TFIDF_TOP_K=500` and `EVIDENCE_FINAL_TOP_K=4`
- Experiments with different values:
  - `TFIDF_TOP_K`: [300, 500, 700, 1000]
  - `EVIDENCE_FINAL_TOP_K`: [3, 4, 5, 6]
- **Embedding Fine-tuning**: Attempted to fine-tune the transformer model to generate better embeddings for our specific task, but this didn't significantly improve performance

### Text Preprocessing Impact

- Lemmatization improved retrieval F-score by approximately 0.02
- Stop word removal decreased performance, likely because important function words helped capture relationship between claims and evidence

### Classification Performance

- Initial model showed strong bias toward predicting NOT_ENOUGH_INFO
- Class imbalance in training data: SUPPORTS (520), NOT_ENOUGH_INFO (390), REFUTES (200), DISPUTED (120)
- Improved by:
  1. Switching to F1-score for model selection instead of accuracy
  2. Enabling class weights to balance the loss function
  3. Adjusting the learning rate to 1e-5 (from 2e-5)

## Current Limitations & Next Steps

1. Evidence retrieval could be further improved with:
   - Better transformer models (e.g., MPNet instead of MiniLM)
   - Query expansion techniques
   - Ensembling multiple retrieval approaches

2. Classification model could be enhanced with:
   - Focal Loss implementation to better handle class imbalance
   - More training epochs (currently using only 3)
   - Different evidence aggregation strategies
   - Larger transformer models like BERT-base or RoBERTa

## Implementation Note

The code implements both tasks with flexible configuration, making it easy to experiment with different settings. Key hyperparameters can be modified at the top of the implementation file.
