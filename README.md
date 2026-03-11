# Mining Cognitive Collapse: Predicting Latent Software Vulnerabilities in LLMs via Linguistic Features

**Status:** Double-Blind Peer Review (ECML PKDD 2026)

This repository contains the complete replication package, including the 1195-sample dataset, the stateful generative pipeline, and the predictive machine learning scripts for our study on "Cognitive Collapse" in Large Language Models (LLMs). 

Our framework evaluates four 2026 frontier models - **GPT-OSS 120B**, **Llama 3.3 70B**, **Moonshot Kimi K2 0905**, and **Qwen 3 32B** - extracting a 12-dimensional linguistic feature matrix during a 6-step multi-turn Socratic workflow to predict deterministic C memory vulnerabilities strictly from pre-execution natural language metadata.

---

## Dataset Overview
Located in the `LLM_Results/` and `content/` directories, the dataset consists of **1195 validated multi-turn interactions**. 

For every generative iteration, the dataset captures:
* **The 12-Dimensional Linguistic Feature Matrix ($X_i$):** Readability scores (Flesch-Kincaid Grade, Gunning Fog), verbosity (Word Count), lexical diversity, semantic decoupling (`Sum_Alignment_Score`), structural ratios (`Comment_Ratio`), and guardrail integrity flags (`Constraint_Memory`, `Hint_Code_Leakage`).
* **Ground Truth Execution State ($y_i$):** Deterministic execution outcomes derived from dynamic instrumentation, flagging successful compilations as safe ($y=0$) or vulnerable to memory leaks/UBSan violations ($y=1$).

---

## Replication Guide

This repository is designed for sequential, one-click reproducibility using Google Colab or a local Jupyter environment.

### Phase 1: Problem Bank Generation
**Run:** `01_Phase1_Problem_Bank_Generation.ipynb`

This executes the initial prompt engineering loop. Queries the target teacher LLMs (via API) to dynamically generate novel C programming problems with mandated constraints (e.g., forced "EXIT" options). Packages 300 unique problems into standardized batches.

### Phase 2: The Automated Audit Pipeline
**Run:** `02_Phase2_Automated_Audit_Pipeline.ipynb`

This feeds the standardized problems back into the four audit models. Operates a strict, 6-step stateful generative workflow requiring the models to sequentially generate C11 code, conceptual explanations, Socratic scaffolding hints, learning objectives, and a mandated JSON test suite.

### Phase 3: Technical Ground Truth Evaluation
**Run:** `03_Automated_Technical_Evaluation.ipynb`

This executes the rigorous runtime auditing pipeline on the generated C code to establish the unquestionable mathematical ground truth for our predictive classifier. Compiles code via `gcc` (-Wall -Werror -std=c11) and executes successfully compiled binaries through `valgrind` and `UBSan` to detect "Definitely Lost" heap memory and undefined behaviors.

### Phase 4: Predictive Modeling & Knowledge Discovery
**Run:** `04_PKDD_Knowledge_Discovery.ipynb`

This contains the core Knowledge Discovery (KDD) contributions of this paper. 
* **Predictive Modeling:** Trains and evaluates the Random Forest classifier using 5-Fold Stratified Cross-Validation to predict execution failures based on linguistic inputs (generates ROC-AUC and Confusion Matrices).
* **Explainable AI (XAI):** Implements the `TreeExplainer` SHAP algorithm to mathematically isolate the "Syntactic Camouflage" phenomenon and generate the feature attribution summary plots.

---

## Environment Requirements
To execute the notebooks locally, ensure your environment has the following installed:
* **Python 3.10+**
* **ML/Data Libraries:** `scikit-learn`, `shap`, `pandas`, `numpy`, `matplotlib`, `seaborn`, `tqdm`
* **Static Parsing:** `lizard` (v1.20.0), `cppcheck` (v.2.7-1), `clang-format`
* **Dynamic Auditing (Linux/WSL required):** `gcc` (>= 11.4.0), `valgrind` (>= 3.18.1) 

---

## License
This dataset and associated codebase are released under an anonymized MIT License for double-blind peer review.
