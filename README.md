# tesla-options-ml
Predicting Tesla options prices with ML models and Black-Scholes comparisons.

## Project Overview

This project applies machine learning techniques to predict the prices of European options on Tesla (TSLA) stock. The goal is to explore how machine learning models can complement or improve traditional pricing methods like the Black-Scholes formula.

Specifically, the project includes:
- A regression task to predict actual option prices based on key features like asset price, strike price, time to maturity, and interest rate.
- A classification task to identify whether the Black-Scholes formula over- or underestimates option prices.

The models are designed to generalize well to unseen data, improving pricing accuracy and providing insights beyond classical models.

## Dataset

- Training data included 5000 options with the following data [data source]:
    - C, Value: option value
    - S: option's asset value
    - K: option strike price
    - tau: time to expiration in years
    - r: risk-free interest rate
    - BS: Black-Scholes predicted option value with labels Over (C_pred - C >= 0) and Under (C_pred - C <= 0)
      
- Testing data included 500 options with the following data [data source]:
    - S: option's asset value
    - K: option strike price
    - tau: time to expiration in years
    - r: risk-free interest rate

## Methodology

### 1. Data Preprocessing and Validation

The dataset underwent standard preprocessing, including handling missing/null values and feature scaling where necessary. Initially, a simple train-test split was used to validate a linear regression model. However, the final evaluation employed **10-fold cross-validation**, which offers more robust and stable performance metrics by averaging results across 10 different training and validation splits.

For the **classification task**, a **stratified and shuffled 10-fold cross-validation** was used to ensure balanced representation of the binary target label ("Over" vs "Under") in each fold.

---

### 2. Evaluation Metrics

- **Regression Task (Predicting Option Value `C`):**
  - Evaluated using **mean cross-validated R-squared** and **mean squared error (MSE)**
  - In cases of marginally similar R-squared, the model with the lower MSE was preferred

- **Classification Task (Predicting Over/Under Label):**
  - Evaluated using **mean cross-validated accuracy**, reflecting the model’s ability to correctly classify if the Black-Scholes formula over- or under-estimated the true option value

---

### 3. Model Selection and Hyperparameter Tuning

A variety of models were explored for both regression and classification:

- **Regression Models:**
  - Linear Regression (baseline)
  - Ridge & Lasso Regression (regularized)
  - K-Nearest Neighbors (KNN)
  - Decision Tree
  - Random Forest
  - Support Vector Machine (SVM)

- **Classification Models:**
  - Logistic Regression (baseline)
  - Linear Discriminant Analysis (LDA)
  - K-Nearest Neighbors (KNN)
  - Decision Tree
  - Random Forest
  - Support Vector Machine (SVM)

Baseline models like linear/logistic regression were chosen for interpretability. Regularized models (ridge/lasso) helped prevent overfitting. Non-linear models like decision trees and SVMs captured more complex relationships.

Hyperparameter tuning was performed using **grid search cross-validation** for models like KNN, decision trees, random forests, and SVM.

Although neural networks were considered, they were ultimately excluded due to:
- Dataset simplicity not warranting deep learning
- High complexity and tuning overhead (e.g., layers, neurons, epochs)

Simpler models were found to be more effective and appropriate for the problem scale.

## Results

### 1. Regression Results

- **Baseline – Linear Regression**  
  - R²: 92.44%  
  - MSE: 1,178   

- **Best – Random Forest**  
  - R²: **99.68%**  
  - MSE: **50.75**

---

### 2. Classification Results

- **Baseline – Logistic Regression**  
  - Accuracy: 87.94%  

- **Best – Random Forest**  
  - Accuracy: **93.52%**  
  - ROC-AUC: **0.9736**

---

## Conclusion

The **random forest algorithm** delivered the strongest performance for both tasks:

- **Regression**: R-squared of **99.68%** and MSE of **50.75**, indicating highly accurate prediction of Tesla’s European option prices.
- **Classification**: Accuracy of **93.52%** and ROC-AUC of **0.9736**, effectively classifying options as over- or under- predicted by the Black-Scholes formula.

## Future Work

- Incorporate implied volatility as a feature
- Test deep learning models
- Extend to American options and other stocks

Feel free to reach out if you have questions or suggestions!
