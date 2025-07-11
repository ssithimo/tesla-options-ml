This folder contains the training and testing data for the tesla-options-ml project.

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
