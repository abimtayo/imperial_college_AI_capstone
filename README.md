Black Box Optimisation Project

Introduction
The project tests the ability to predict the maximum values of functions in the face of uncertainty. Initial input and output datasets are provided for each function and one is required to find a statistical model best able to simulate each function.

The model is used to generate new input points weekly. These are submitted to a program that generates outputs for each function. These values are added to the datasets. The model’s predictions are expected to improve as the dataset grows.
Hyperparameters, that is configuration values for the model, can be tuned to help the model improve its predictions.

DATA
The data used in the projects comes from a series of input and output files, one set for each function.

An initial set of inputs and outputs for each function is provided at the beginning of the project. These are actual values observed from the data for each function. 

New input points are suggested by the model each week and these are feed to a program that generates the outputs for each function. The dataset size increases weekly due to this.

MODEL
The selected models used to simulate the functions are models from the Gaussian Process family of models. For functions where the outputs are relatively smooth a Radial Basis Function (RBF) surrogate was used. For functions where the outputs are more complex from marginally smooth to highly irregular, a Matern surrogate is used. 
HYPERPARAMETER OPTIMSATION

The following hyperparameters were used:

- xi: acquisition function parameter for Expected Improvement (EI)

- kappa: acquisition function parameter for Upper Confidence Bound (UCB)

- nu: smoothness parameter for Matern surrogate optimisation.
  
- length_scale_lower: lower length scale bound for Guassian Process surrogate optimisation
  
- length_scale_upper: upper length scale bound for Guassian Process surrogate opotimisation
  
- noise_level: initial noise level for Guassian Process surrogate optimisation
  
- noise_level_lower: lower noise level bound for Guassian Process surrogate optimisation
  
- noise_level_upper: upper noise level bound for Guassian Process surrogate optimisation

RESULTS
The results were mixed based on the functions. Some of the functions, in particular functions 5 and 8, proved relatively straightforward to simulate. Whereas others, such as function 4 and 6, proved more difficult to simulate.

A lot of time was spent hyperparameters to mixed effect and given more time further experimentation in this area would likely have yielded better results.
