# 📈 Tesla European Options Pricing — ML vs. Black-Scholes

> *Black-Scholes has been the industry standard for options pricing since 1973. This project asks whether machine learning can do better, and by how much.*

---

## 🧩 Business Context

The Black-Scholes formula is the foundation of modern options pricing. Every derivatives desk, quant fund, and retail trading platform uses it as a baseline. But Black-Scholes makes simplifying assumptions — constant volatility, no dividends, log-normally distributed returns — that real markets routinely violate. When those assumptions break down, the formula systematically over- or underprices options.

This project evaluates whether machine learning models can predict Tesla European call option prices more accurately than Black-Scholes, and whether they can reliably classify when Black-Scholes gets the direction of its error wrong.

Two tasks were evaluated:

1. **Regression**
  
   — predict the actual option price from market inputs

2. **Classification**
  
   — predict whether Black-Scholes over- or underestimates the true option price

---

## 📊 Dataset

  - 5,000 training options and 500 test options
  - European call options on Tesla (TSLA) stock

**Features:**

  - S: underlying asset price
  - K: strike price
  - tau: time to expiration (years)
  - r: risk-free interest rate
  - BS: Black-Scholes predicted price (used as a feature and benchmark)

**Classification label:**

  - Over: Black-Scholes prediction >= actual price
  - Under: Black-Scholes prediction < actual price

---

## 🔍 Methodology

**Validation approach:**

  - 10-fold cross-validation for regression and stratified shuffled
  - 10-fold cross-validation for classification, ensuring balanced representation of Over/Under labels across all folds.

**Regression evaluation:** 
    
  - mean cross-validated R² and MSE. In cases of similar R², lower MSE was the tiebreaker.

**Classification evaluation:** 
    
  - mean cross-validated accuracy and ROC-AUC.

**Models evaluated:**

- Regression:

  - Linear Regression (baseline), Ridge, Lasso, KNN, Decision Tree, Random Forest, SVM

- Classification

  - Logistic Regression (baseline), LDA, KNN, Decision Tree, Random Forest, SVM

Neural networks were considered but excluded. The dataset's relatively simple feature structure did not warrant deep learning complexity, and simpler models were found to be more effective at this problem scale.

Hyperparameter tuning was performed via grid search cross-validation for KNN, Decision Tree, Random Forest, and SVM.

---

## 📈 Results

### Regression: Predicting Option Price

| Model | R² | MSE |
|-------|-----|-----|
| OLS Linear Regression | 92.44% | 1,178.30 |
| Ridge Regression (α=0.0202) | 92.44% | 1,178.30 |
| Lasso Regression (α=0.0116) | 92.44% | 1,178.25 |
| SVM | 91.56% | 1,316.66 |
| KNN (k=3) | 98.42% | 246.23 |
| Decision Tree | 99.10% | 139.22 |
| **Random Forest** | **99.68%** | **50.51** |

Random Forest reduced MSE by 95.7% relative to the OLS baseline and outperformed every other model on both R² and MSE. Ridge and Lasso produced identical results to OLS, confirming that regularization adds no value when the baseline model is already underfitting due to nonlinearity rather than overfitting. SVM performed worst of all models despite its complexity.

### Classification: Predicting Black-Scholes Direction Error

| Model | Accuracy | ROC-AUC |
|-------|----------|---------|
| LDA | 86.92% | — |
| Logistic Regression | 87.94% | — |
| SVM | 89.26% | — |
| Decision Tree | 91.52% | — |
| KNN | 91.58% | — |
| **Random Forest** | **93.52%** | **0.9737** |

Random Forest was the clear winner, outperforming the logistic regression baseline by 5.58 percentage points and achieving ROC-AUC of 0.9737 — meaning it correctly ranks over/under options in 97.37% of cases.

Linear family (OLS, Ridge, Lasso, linear SVM)
  
  - All converge around 91-92% R²
  - Structurally limited by linearity assumption
  - Can't capture multiplicative interactions between S, K, tau, r

Nonlinear tree-based family (KNN, Decision Tree, Random Forest)

  - 98-99% R² range
  - Recursive partitioning naturally captures option pricing nonlinearities
  - Random Forest's ensemble averaging reduces variance further

Winner: Random Forest at 99.68% R²

---

## ✅ Model Selection: Random Forest

Random Forest was the best performing model for both tasks. 

Three factors drove the selection:

1. **Regression dominance**

    — 99.68% R² vs 92.44% for the linear baseline is not a marginal improvement. The 95.7% MSE reduction (from 1,178 to 50.75) confirms the model captures nonlinear relationships between strike price, time to maturity, and option value that linear models structurally cannot represent.

2. **Classification reliability**

    — 93.52% accuracy and ROC-AUC of 0.9736 on a binary task means the model is a credible screening tool for identifying when Black-Scholes is likely to be directionally wrong, which has direct application in trading strategy and risk management.

3. **Interpretability tradeoff**

   — the report explicitly weighed model interpretability against accuracy. Random Forest was selected over simpler models because the performance gap was large enough to justify the added complexity, and over neural networks because the dataset scale did not warrant deep learning overhead.

---

## 🔍 Key Findings

**SVM underperformed despite nonlinear kernel options**

  - The SVM grid search included linear, polynomial, and RBF kernels across C values of 0.1, 1, and 10 and gamma values of 0.1, 0.01, and 0.001. Despite having the opportunity to fit nonlinear decision boundaries, SVM achieved the worst regression performance of all models at 91.56% R² and MSE of 1,316.66 — worse than the OLS baseline. This suggests that the kernel-based similarity structure SVM relies on does not capture the interaction effects between strike price, time to maturity, and asset price as effectively as the recursive partitioning approach used by tree-based models. For tabular financial data with multiplicative feature interactions, ensemble tree methods appear better suited than kernel methods.

**Ridge and Lasso matched OLS exactly**

  - Both regularized models produced identical R² (92.44%) and nearly identical MSE to OLS regardless of the regularization parameter. When regularization produces no improvement over the baseline, it signals the problem is underfitting due to nonlinearity rather than overfitting due to large coefficients. The linear model family as a whole was structurally incapable of capturing the option pricing relationships in this dataset. The dramatic jump from linear models at 92.44% R² to tree-based models at 99.10% to 99.68% confirms that nonlinearity is the dominant characteristic of this pricing problem.

**Machine learning meaningfully outperforms Black-Scholes**

  - The linear regression baseline at 92.44% R² already beats what Black-Scholes can offer on its own in terms of pricing accuracy. Random Forest at 99.68% R² represents a further substantial improvement, suggesting that even with simple market features, ML models capture option pricing dynamics that closed-form formulas miss.

**Black-Scholes direction errors are predictable**

  - 93.52% classification accuracy means the model correctly identifies whether Black-Scholes will over- or underprice an option in roughly 9 out of 10 cases. That predictability has practical implications: a trader who knows Black-Scholes is likely to underprice an option can adjust their position sizing accordingly.

**Model complexity should match problem scale**

  - Neural networks were evaluated and excluded because simpler ensemble methods achieved near-perfect performance without the tuning overhead. This is a recurring theme across projects: model complexity is a tool, not a goal.

---

## 🚀 Future Work

- Incorporate implied volatility as a feature, which would more directly capture market expectations beyond historical inputs
- Extend to American options where early exercise premium adds additional pricing complexity
- Test on other high-volatility stocks to assess whether findings generalize beyond TSLA
- Evaluate deep learning architectures on larger option datasets where complexity overhead is more justified

---

## 🔧 Tools
Python, scikit-learn, Random Forest, Ridge/Lasso Regression, KNN, SVM, Decision Tree, GridSearchCV, 10-Fold Cross-Validation, Pandas, NumPy, Matplotlib
