
# Medical Abstract Classification with BERT (Transfer Learning)

## Overview

This project fine-tunes a BERT-based model for **medical abstract sentence classification**.
Each sentence from a medical abstract is classified into one of five rhetorical roles:

* BACKGROUND
* OBJECTIVE
* METHODS
* RESULTS
* CONCLUSIONS

The goal is to automatically structure medical literature, which is valuable for clinical research, literature review, and evidence synthesis.

---

## Dataset

**Dataset:** PubMed 200k RCT (medical abstract classification)

* ~2.2M labeled sentences from randomized controlled trial abstracts
* 5-class classification task
* Real-world biomedical NLP benchmark

Each sample contains:

* `text`: sentence from an abstract
* `labels`: rhetorical role

---

##  Exploratory Data Analysis (EDA)

### Class Distribution

| Class       | Percentage |
| ----------- | ---------- |
| RESULTS     | 34.64%     |
| METHODS     | 32.67%     |
| CONCLUSIONS | 15.36%     |
| BACKGROUND  | 8.89%      |
| OBJECTIVE   | 8.44%      |

### Key Observations

* Moderate class imbalance
* Imbalance ratio (max/min) ≈ **4.11**
* METHODS & RESULTS dominate the dataset
* OBJECTIVE and BACKGROUND are minority classes

### Sentence Length Analysis

Average sentence lengths:

* RESULTS → 29.59 words
* OBJECTIVE → 27.13 words
* METHODS → 24.81 words
* CONCLUSIONS → 23.04 words
* BACKGROUND → 22.97 words

Insight:

* Structural variation exists across sections
* METHODS and RESULTS tend to be longer and more descriptive

---

##  Model

**Base Model:** BERT Base (uncased)

### Approach

* Transfer learning using pretrained BERT
* Fine-tuned for 5-class classification
* Manual PyTorch training loop (no Trainer API used)
* Class-weighted CrossEntropyLoss to handle imbalance

---

##  Training Setup

* Framework: PyTorch
* Optimizer: AdamW
* Learning Rate: 2e-5
* Batch Size: 16
* Epochs: 2
* Max Length: 128 tokens
* Training Subset: 200k samples (due to compute constraints)
* Hardware: Google Colab GPU

### Why Class Weights?

To reduce bias toward frequent classes and improve minority-class recall.

---

##  Evaluation Metrics

### Test Performance

* **Accuracy:** 0.8579
* **Precision (Weighted):** 0.8649
* **Recall (Weighted):** 0.8579
* **F1-score (Weighted):** 0.8594
* **Macro F1:** 0.7979

---

##  Per-Class Performance

| Class       | F1-score |
| ----------- | -------- |
| METHODS     | 0.9309   |
| RESULTS     | 0.9067   |
| CONCLUSIONS | 0.8022   |
| BACKGROUND  | 0.6761   |
| OBJECTIVE   | 0.6735   |

### Insights

* Strong performance on METHODS and RESULTS
* OBJECTIVE and BACKGROUND are more challenging
* Semantic overlap between sections affects classification
* Class-weighted loss helped maintain minority-class performance

---

##  Confusion Matrix

![WhatsApp Image 2026-02-13 at 15 18 24](https://github.com/user-attachments/assets/76588af6-2d79-48c3-aab1-fc12b95e2e64)


Key pattern:

* OBJECTIVE often confused with BACKGROUND
* CONCLUSIONS sometimes confused with RESULTS

These reflect real rhetorical similarities in abstracts.

---

##  Inference Pipeline

A custom `predict_text()` function:

Input:

* Raw sentence

Output:

* Predicted class
* Confidence score

Example:

> “A randomized controlled trial was conducted…”
> → METHODS (0.96 confidence)

## Findings

### Edge Case Analysis

To better understand model behavior beyond aggregate metrics, we evaluated several challenging and ambiguous sentences. This helps assess how well the model generalizes to realistic and borderline cases.

---

### Very Confident and Correct Predictions

#### 1. Background Sentence  
**Confidence:** 0.85  

The model correctly classified a general epidemiological statement as BACKGROUND.  
Such broad contextual sentences are common in the introductory portion of medical abstracts, and the model appears to have learned these structural cues effectively.

---

#### 2. Methods Sentences  

- RCT description → **0.9675**
- Long structured methods sentence → **0.9102**

The model performs exceptionally well on METHODS sentences.  
Structured phrases such as "randomized controlled trial" and detailed procedural descriptions provide strong signals.  
High confidence scores indicate that the model has successfully learned methodological language patterns.

---

#### 3. Clear Results  

“Significant differences were observed.” → **0.8998**

Despite being short, this sentence contains classic statistical outcome phrasing.  
The high confidence suggests that the model effectively identifies characteristic results-oriented language.

---

#### 4. Strong Conclusion  

“These findings indicate…” → **0.9902**

This sentence was classified with near-perfect confidence.  
Phrases that summarize findings or imply broader implications are strong indicators of the CONCLUSIONS section, and the model captures this reliably.

---

### Moderate Confidence Cases (Ambiguity Analysis)

#### Objective Case  

“We sought to investigate…”  
**Prediction:** OBJECTIVE  
**Confidence:** 0.569  

Although the confidence is lower, the prediction is correct.  
The OBJECTIVE class is both a minority class and semantically close to BACKGROUND.  
The reduced confidence reflects the subtle boundary between contextual setup and stated research aims.  
Importantly, the model still predicts correctly, suggesting that class-weighted training helped preserve minority-class performance.

---

#### Mixed Results and Conclusion Sentence  

Confidence: **0.5194**

This sentence combines outcome reporting (“reduced symptoms”) with interpretive language (“benefits remain uncertain”).  
Such overlap naturally blurs the boundary between RESULTS and CONCLUSIONS.  

The moderate confidence is reasonable and reflects the inherent ambiguity of the sentence. Rather than overconfidently misclassifying, the model demonstrates cautious behavior in a linguistically complex case.

---

##  Applications

* Medical literature structuring
* Automated systematic reviews
* Clinical research support tools
* Biomedical information retrieval

---

##  Repository Structure

```
├── BoundedByLLMs_MedicalAbstract.ipynb
├── bert_medical_classifier/
├── README.md
```

---

##  Conclusion

This project demonstrates effective transfer learning for biomedical NLP.
Despite dataset imbalance and subtle semantic differences between classes, the model achieves strong performance using:

* BERT fine-tuning
* Class-weighted loss
* Proper evaluation metrics

The system shows practical potential for real-world medical text processing.

---

## Contributors
- [Priyanka S](https://github.com/pris25123)
- [Padarthi Neha Sai](https://github.com/pnehasai)



