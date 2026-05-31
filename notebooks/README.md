This folder contains a single Jupyter notebook that consolidates the entire workflow for the Tesla options pricing project.

Note: if the notebook doesn't open in github, read the printed pdf version [here](https://github.com/ssithimo/tesla-options-ml/edit/main/notebooks/tesla-options-pricing-modeling.pdf)
## Contents

- `options_pricing_modeling.ipynb`:  
  Contains all code for:
  - Data loading and cleaning  
  - Exploratory data analysis (EDA)  
  - Feature engineering  
  - Regression modeling to predict actual option prices  
  - Classification modeling to predict over/under Black-Scholes performance  
  - Model evaluation and selection
 
## How to run

### Clone the repository:

```bash
git clone https://github.com/ssithimo/tesla-options-ml.git
cd tesla-options-ml
```

### Install dependencies:

```bash
pip install -r requirements.txt
```

### Run the notebook:
```bash
jupyter notebook notebooks/options_pricing_modeling.ipynb
```
