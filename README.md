
# Alzheimer's Disease Modeling Project

## Overview
This project investigates clinical, cognitive, lifestyle, and demographic predictors of Alzheimer’s disease (AD) diagnosis.  
The workflow is implemented in R and follows three main stages:

1. **Data Cleaning**: preparing and standardizing the dataset  
2. **Exploratory Data Analysis**: summarizing and visualizing variable distributions  
3. **Modeling and Validation**: applying statistical and machine learning models to predict AD diagnosis  

The goal is to identify which features contribute most strongly to the risk and diagnosis of Alzheimer’s disease.

---

## Repository Structure
```
├── AD_dataset_cleaning.qmd                  # Data import, cleaning, and preprocessing
├── AD_dataset_exploration.qmd               # Descriptive statistics and visualization
├── AD_dataset_analysis.qmd                  # Predictive modeling and validation
├── MSB1015_final_assignment_maaike_kessen   # Full script          
├── README.md                                # Project documentation
```

---

## 1. Data Cleaning

### Objectives
- Remove redundant or sensitive columns 
- Standardize missing data and invalid entries  
- Convert categorical variables to factors  
- Detect and correct implausible numeric values  
- Export a consistent, analysis-ready dataset  

### Key Steps
- Handled missing values using `na.strings` in `read.csv()`  
- Replaced out-of-range numeric values 
- Converted categorical columns such as `Gender`, `Diagnosis`, and `MemoryComplaints` into factors  
- Applied median imputation for missing numeric data  
- Exported the cleaned dataset:

```r
write.csv(df, "cleaned_alzheimers_dataset.csv", row.names = FALSE)
```

---

## 2. Exploratory Data Analysis 

### Purpose
To explore trends, distributions, and relationships between variables, and to identify key predictors of Alzheimer’s disease.

### Methods and Visualizations
- **Boxplots** for numeric variables to inspect distributions and detect outliers  
- **Barplots** for categorical variable frequencies  
- **Symptom prevalence charts** comparing AD vs. non-AD groups  
- **Cognitive and functional score plots** (MMSE, ADL, FunctionalAssessment) with clinical cut-off lines  
- **Principal Component Analysis (PCA)** to visualize overall data structure and variance  

### Variable Grouping (Blocks)

| Block     | Variables                                                                                                                     |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Cognitive | MMSE, ADL, FunctionalAssessment                                                                                               |
| Clinical  | Age, Gender, Hypertension, Diabetes, Depression                                                                               |
| Lifestyle | PhysicalActivity, DietQuality, SleepQuality, AlcoholConsumption                                                               |
| Symptoms  | MemoryComplaints, BehavioralProblems, Confusion, Disorientation, PersonalityChanges, Forgetfulness, DifficultyCompletingTasks |

---

## 3. Modeling and Validation

### Logistic Regression
A logistic regression model was trained using 5-fold cross-validation to classify patients as AD or non-AD.

**Steps:**
- 70/30 train-test split using `caret::createDataPartition()`  
- Fitted logistic regression with `caret::train()`  
- Evaluated accuracy, sensitivity, specificity, and balanced accuracy  
- Visualized variable importance and confusion matrix  
- Generated ROC curve and computed AUC  

---

### Risk-Only vs. Full Model Comparison
Two logistic regression models were compared:

- **Full Model:** includes all predictors  
- **Risk-Only Model:** excludes cognitive and symptom variables  

**Evaluation:**
- ROC curves and AUC values plotted for both models  
- Full model achieved higher AUC, confirming that cognitive and behavioral measures are the strongest predictors of AD  

---

### Multiblock AUC Analysis
Each variable block (Cognitive, Clinical, Lifestyle, Symptoms) was evaluated separately using logistic regression.  
AUC was calculated for each block and compared.

**Results:**
- Cognitive block achieved the highest AUC  
- Clinical and lifestyle variables provided moderate predictive power  
- Combining multiple blocks improved classification performance  

---

### Odds Ratios and Confidence Intervals
Logistic regression coefficients were exponentiated to compute odds ratios (OR) with 95% confidence intervals, formatted in a `gt` table for interpretation.

| Interpretation | Meaning                    |
| --------------- | -------------------------- |
| OR > 1          | Variable increases AD risk |
| OR < 1          | Variable decreases AD risk |
| p < 0.05        | Statistically significant  |

---

### Random Forest
A Random Forest model (`randomForest` package) was used to capture nonlinear effects and rank variable importance.

**Configuration:**
- 500 trees (`ntree = 500`)  
- Variable importance measured by *Mean Decrease in Gini Index*  

**Output:**
- Importance plot showing that MMSE, ADL, and FunctionalAssessment are top predictors, as well as BehavioralProblems and MemoryComplaints.  

---

### Elastic Net Regularization
An Elastic Net model (`glmnet` package) was fitted to handle correlated predictors and perform feature selection.

**Steps:**
- Created model matrix with `model.matrix()`  
- Performed cross-validation (`cv.glmnet`) with α = 0.5  
- Extracted non-zero coefficients and plotted their effects  

**Interpretation:**  
Positive coefficients increase AD likelihood; negative coefficients decrease it.

---

## 4. Key Findings

- Cognitive scores are the strongest predictors of Alzheimer’s diagnosis  
- Symptoms such as MemoryComplaints and BehavioralProblems contribute significantly  
- Demographic and lifestyle variables alone are insufficient for high predictive accuracy  
- The combined full model achieved the best classification performance (AUC ≈ 0.88)  

---

## Requirements

Ensure the following R packages are installed before running the scripts:

```r
libs <- c("ggplot2", "caret", "tidyverse", "dplyr",
          "pROC", "broom", "gt", "randomForest", "glmnet")
install.packages(setdiff(libs, rownames(installed.packages())))
```

---

## How to Run the Project

1. **Run `AD_dataset_cleaning.qmd`**
   - Cleans and standardizes the raw dataset  
   - Exports `cleaned_alzheimers_dataset.csv`

2. **Run `AD_dataset_exploration.qmd`**
   - Loads the cleaned dataset  
   - Performs exploratory plots and descriptive analysis  

3. **Run `AD_dataset_analysis`**
   - Builds logistic, Random Forest, and Elastic Net models  
   - Evaluates predictive performance using AUC and confusion matrices  
   - Outputs key visualizations and model summaries  

---

## Data Description
The dataset includes demographic, lifestyle, clinical, and cognitive variables collected from patients.  
The dependent variable (`Diagnosis`) is binary, indicating Alzheimer’s disease (AD) or no Alzheimer’s (No AD).

Missing data were handled through median imputation.  
Numeric features were scaled and centered where appropriate for PCA and regression analyses.

---

## Author
Maaike Kessen  
Master’s Programme in Systems Biology and Bioinformatics  
Maastricht University

---

## License
This project is for academic and educational use only. 
Redistribution of patient-level data is not permitted.

