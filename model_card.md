Model Card — Bayesian Optimisation Process
------------------------------------------

#Model Description
------------------

This model implements a Bayesian optimisation (BO) process for maximising eight black-box objective functions. The approach uses a probabilistic surrogate model to estimate the objective function and its uncertainty, then uses acquisition functions to identify promising input configurations for the next objective-function evaluation.
The process is designed for optimisation where evaluating the underlying objective function is potentially expensive and therefore the number of evaluations should be minimised.

##Inputs
-------
The optimisation process operates on eight functions:

Function	Number of Parameters
function_1	2
function_2	2
function_3	3
function_4	4
function_5	4
function_6	5
function_7	6
function_8	8

All input variables are constrained to the range [0, 1].
The model receives:
•	Initial input observations 
•	Initial output observations 
•	Additional weekly values (inputs are suggestions from the model, outputs are the resulting observed outputs)
•	Function-specific hyperparameters 
The initial and newly supplied observations are combined before fitting the surrogate model.

##Outputs
-------
For each function, the optimisation process produces a proposed next input vector.
The proposed point is accompanied by:
•	The acquisition method used (Expected Improvement and Upper Confident Bound)
•	The acquisition-function score.
•	The Gaussian Process predicted mean at the proposed point.
•	A neural-network prediction used for comparison.
•	The current highest observed function value.
 
#Model Architecture
------------------

The optimisation pipeline consists of the following stages:
1.	Load initial observations.
2.	Load and append weekly observations.
3.	Fit a Gaussian Process surrogate model.
4.	Generate random candidate points within the search space.
5.	Evaluate Expected Improvement (EI) and Upper Confidence Bound (UCB) for those candidates.
6.	Identify the best candidate according to each acquisition function.
7.	Compare the two candidate points using their predicted Gaussian Process mean.
8.	Select the candidate with the higher predicted mean as the next suggested point.
The process is repeated independently for each of the eight functions.
 
##Surrogate Model
---------------
The principal surrogate model is a Gaussian Process surrogate model  

Two kernel types are used:

###Matern Kernel
-------------
Where selected in the hyperparameter file, the model uses: Constant Kernel × Matern Kernel + White Noise Kernel
The Matern kernel uses a function-specific nu hyperparameter.
The length scale is defined separately for each input dimension and is constrained by lower and upper bounds supplied in the hyperparameter file.

###RBF Kernel
-----------
Where selected, the model uses:
RBF Kernel + White Noise Kernel
The RBF kernel also uses dimension-specific length scales subject to supplied lower and upper bounds.
 
##GP Hyperparameter Optimisation
-------------------------------------
The GP hyperparameters are optimised using a custom L-BFGS-B optimisation function.
The implementation uses:
•	One midpoint starting point.
•	Ten random starting points.
•	A fixed random seed of 42 for these starting points.
•	Maximum 1,000 iterations per optimisation.
•	Function tolerance of 1e-10.
•	Gradient tolerance of 1e-6.
 
##Candidate Generation and Selection
----------------------------------
For each function, the code generates random candidate points uniformly within the search space. EI and UCB are evaluated for all points. The highest-EI point and highest-UCB point are retained.
 
##Neural Network Comparison Model
---------------------------------
A basic two-layer feed-forward neural network is included to provide predictions for the sake of comparison.
The architecture consists of:
•	Input linear layer.
•	ReLU activation.
•	Output linear layer.
The network uses:
•	Adam optimiser.
•	Learning rate of 1e-4.
•	500 training iterations.
•	One hidden layer containing 10 units.
 
#Performance
--------------------------------------------
##Performance Measurement
--------------------------
The supplied implementation does not currently perform a formal independent performance evaluation.
It reports the following quantities:
•	Best observed objective value 
•	GP prediction at the proposed point.
•	EI acquisition score.
•	UCB acquisition score.
•	Selected acquisition method.
•	Neural-network prediction.
The code also produces a diagnostic plot for each function.
 
##Surrogate Diagnostic
----------------------
The diagnostic plot displays:
•	Actual observed outputs.
•	GP predictive mean.
•	GP predictive mean ± one standard deviation.
•	Signed directional distance from the currently observed optimum.

This provides a visual assessment of how the GP surrogate represents the observed data and its uncertainty.

#Limitations
-------------------------------------------------------------
1. No independent validation set
The GP is trained using all available observations. There is no explicit train/test split or cross-validation.
Consequently, the diagnostic plots cannot establish how accurately the GP predicts previously unseen points.
2. No formal convergence analysis
The implementation does not currently calculate:
•	Evaluation efficiency.
•	Distance to the global optimum.
•	Number of evaluations to convergence.
This limits the ability to quantify optimisation performance.
3. Kernel assumptions
The GP’s performance depends on the suitability of the selected kernel.
The RBF kernel assumes a relatively smooth underlying function, while the Matern kernel can accommodate less-smooth behaviour depending on its nu value.
An inappropriate kernel can result in poor predictions or poorly calibrated uncertainty.
4. Hyperparameter sensitivity
Performance can depend substantially on:
•	Kernel choice.
•	Matern nu.
•	Length-scale bounds.
•	Noise-level bounds.
•	EI xi.
•	UCB kappa.
Poorly chosen hyperparameter bounds can constrain the GP to an inappropriate model.
5. Weekly-data handling
Weekly observations are appended to the historical data.
The code does not explicitly detect:
•	Duplicate observations.
•	Contradictory measurements.
•	Changes in the underlying objective function.
 
#Trade-offs
-------------------------------------------------------
##Exploration vs Exploitation
EI and UCB are designed to balance exploration and exploitation.
•	EI uses the xi hyperparameter to influence exploration.
•	UCB uses the kappa hyperparameter to weight uncertainty.
Higher exploration can identify potentially better regions but may spend evaluations in areas with relatively low predicted performance.
Lower exploration can concentrate evaluations around apparently good regions but risks missing the global optimum.
 
##Matern vs RBF
The Matern kernel provides greater flexibility for modelling less-smooth functions.
The RBF kernel is appropriate where the objective is expected to vary smoothly.
The trade-off is therefore between: smoothness assumptions and flexibility
The appropriate choice depends on the characteristics of each objective function.

#Overall Assessment
The supplied implementation is a Gaussian Process-based Bayesian optimisation system designed to maximise eight black-box functions with between 2 and 8 input dimensions.
Its principal characteristics are:
•	8 optimisation functions
•	Input dimensions of 2–8
•	Inputs normalised/bounded to [0,1]
•	Gaussian Process surrogate
•	Matern or RBF kernels
•	White-noise component
•	GP hyperparameter optimisation using L-BFGS-B and multiple starting points
•	Expected Improvement and UCB
•	Function-specific xi and kappa hyperparameters
•	Final selection between EI and UCB candidates based on GP predicted mean
•	Neural network used as a comparison
•	Diagnostic surrogate plots
•	No formal held-out validation or regret analysis currently implemented




