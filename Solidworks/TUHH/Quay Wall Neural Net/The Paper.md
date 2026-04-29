This is task 1 of Kacper's project which is to write a review paper. This paper will serve as a starting point for other researchers who want to explore different surrogate models that can be used to perform analysis in the field of civil and geotechnical engineering.

In the email that Kacper sent me, it said that there should be some specific things that need to be talked about. These include:
- Type of surrogate models
- Sampling methods
- Tuning Methods
- Evaluation Metrics
- Regularization techniques
- Types of neural networks
	- This will go into surrogate models
- Activation functions

The goal of the paper is to find different references and write about current methods that are used for surrogate models specifically surrogate models that are neural networks. You can briefly talk about basic mathematical models but we want the focus to be on machine learning specifically.

Before we begin I would like to familiarize myself with some of these definitions:

### Sampling methods
Sampling methods in the context of surrogate modeling refer to the different techniques used to select points from the design or parameter space where the expensive high fidelity model will be evaluated to generate training data for the surrogate model. These methods are important because the quality, distribution, and the number of sample points will directly impact the accuracy and reliability of the resulting surrogate model 

In simple terms: sampling refers to finding input that have the best quality, distribution, etc. and use the high fidelity model to get outputs from this and use these inputs and outputs to train your surrogate model

Think about it this way:
Every time you run your complex, expensive model (like a CFD simulation or a detailed structural analysis), you're generating one precious data point. You might only be able to afford 50 or 100 of these runs due to computational cost or time constraints.

The fundamental questions sampling methods answer are:
- Which specific combinations of input parameters should you evaluate?
- How can you distribute these points to capture the full behavior of your system?
- How can you avoid wasting evaluations on redundant or less informative regions?

The goal is to capture the most information about your system's behavior with the fewest possible expensive evaluations. Then, once you have those carefully chosen input-output pairs, you use them as training data to build your surrogate model.

Good sampling strategies ensure that your surrogate model will generalize well across the entire design space, not just at the points you evaluated. They help you capture important features like nonlinearities, thresholds, and interactions between variables that might otherwise be missed with poorly distributed sample points.

### Space-Filling Designs
These methods aim to distribute points evenly throughout the parameter space to capture the global behavior of the system:
1. **Latin Hypercube Sampling (LHS)**: One of the most popular methods that ensures sample points are evenly distributed across each input dimension individually. It divides each dimension into equal intervals and places exactly one sample in each interval, creating a stratified sampling approach that provides good coverage with relatively few samples.
2. **Quasi-Monte Carlo methods**: These use low-discrepancy sequences (like Sobol, Halton, or Hammersley sequences) to generate points that are more uniformly distributed than random sampling, reducing gaps and clustering that can occur with purely random approaches.
3. **Maximin and Minimax Distance Designs**: Maximin designs maximize the minimum distance between any two sample points, while minimax designs minimize the maximum distance from any point in the design space to the nearest sample point. Both aim to optimize the space-filling properties.
4. **Orthogonal Arrays**: These ensure balanced coverage of the parameter space by arranging samples so specific combinations of parameter values appear an equal number of times.

### Adaptive Sampling Methods
These methods intelligently add new sample points based on information gained from previous evaluations:
1. **Expected Improvement (EI)**: Often used in Bayesian optimization, EI selects points that have the highest expected improvement over the current best solution, balancing exploration of uncertain regions and exploitation of promising regions.
2. **Uncertainty Sampling**: Selects points where the surrogate model's prediction uncertainty is highest, targeting regions where additional information would most benefit model accuracy.
3. **Voronoi-Based Sampling**: Identifies the largest Voronoi cells in the parameter space (regions closest to each existing sample point) and places new samples in these underrepresented regions.
4. **Active Learning MacKay (ALM) and Active Learning Cohn (ALC)**: These methods select points that maximize expected information gain about the model parameters or output variance, respectively.

### Gradient-Based Methods
These focus on capturing the functional behavior of the system:
1. **Response Surface Gradient-Based Sampling**: Places more samples in regions where the response surface has steep gradients (high rates of change), as these regions are more difficult to approximate accurately.
2. **Lipschitz Sampling**: Uses the Lipschitz constant (maximum rate of change) of the function to guide sampling toward regions where the function changes rapidly.

### Multi-Fidelity Sampling
When multiple fidelity levels of the model are available:
1. **Trust-Region Methods**: Sample densely in regions where the low-fidelity model agrees well with high-fidelity evaluations and more sparsely elsewhere.
2. **Co-Kriging Based Sampling**: Leverages correlations between low and high-fidelity models to determine where high-fidelity samples would be most informative.

### Practical Implementation Considerations
When discussing sampling methods in your review, you might want to address:
- **Sample Size Determination**: How to decide how many samples are needed for adequate surrogate model accuracy
- **Constraint Handling**: How to handle sampling in constrained design spaces
- **Dimensionality Challenges**: How sampling strategies adapt to high-dimensional spaces (the curse of dimensionality)
- **Sequential vs. One-Shot Sampling**: Trade-offs between generating all samples at once versus iterative approaches

The choice of sampling method significantly impacts both the computational cost of building the surrogate model and its final accuracy. Different problems may benefit from different sampling strategies depending on the smoothness of the function, dimensionality of the parameter space, and available computational budget.


### Tuning Methods
Tuning methods in surrogate modeling refer to the approaches used to optimize the surrogate model's structure, parameters, and hyperparameters to ensure it accurately approximates the high-fidelity model or experimental data. This is a critical step that significantly impacts the surrogate model's performance, generalization ability, and reliability.

Here's a comprehensive explanation of tuning methods in surrogate modeling:

### Parameter Estimation Methods
These methods focus on finding the optimal values for the surrogate model's internal parameters:

1. **Maximum Likelihood Estimation (MLE)**: Finds parameter values that maximize the probability of observing the training data given the model. This is commonly used for tuning parameters in Gaussian Process models (kriging), where correlation parameters are estimated to best fit the observed data.
2. **Least Squares Fitting**: Minimizes the sum of squared differences between model predictions and actual observations. This approach is common in polynomial response surfaces and radial basis function networks.
3. **Bayesian Estimation**: Incorporates prior knowledge about parameters and updates these beliefs based on observed data, resulting in a posterior distribution over parameter values rather than point estimates.
4. **Cross-Validation Based Estimation**: Tunes parameters to minimize prediction error on validation data not used for training, helping to prevent overfitting and ensure generalizability.

### Hyperparameter Optimization
Hyperparameters control the higher-level structure and learning process of the surrogate model:

1. **Grid Search**: Systematically evaluates model performance across a predefined set of hyperparameter combinations. Though computationally expensive, it's comprehensive and guaranteed to find the best combination within the searched space.
2. **Random Search**: Randomly samples hyperparameter combinations from predefined distributions. Often more efficient than grid search, especially in high-dimensional hyperparameter spaces where not all dimensions are equally important.
3. **Bayesian Optimization**: Uses probabilistic surrogate models (often Gaussian Processes) to model the relationship between hyperparameters and model performance, then selects the most promising hyperparameters to evaluate next.
4. **Evolutionary Algorithms**: Employs genetic algorithms or evolution strategies to evolve populations of hyperparameter sets toward improved performance, using mechanisms inspired by natural selection.
5. **Gradient-Based Optimization**: For hyperparameters that can be differentiated, methods like gradient descent can optimize them directly, though this requires the loss function to be differentiable with respect to the hyperparameters.

### Architecture Optimization
For neural network-based surrogate models:

1. **Network Architecture Search (NAS)**: Automated methods to discover optimal network architectures, including the number of layers, nodes per layer, and connectivity patterns.
2. **Growing and Pruning Techniques**: Dynamically adjusting the network structure by adding nodes or layers where needed (growing) or removing unnecessary ones (pruning).
3. **Early Stopping**: Terminates training when performance on validation data stops improving, preventing overfitting and determining the optimal number of training iterations.

### Multi-Fidelity and Transfer Learning Approaches
1. **Knowledge Transfer Tuning**: Leverages information from previously trained models or lower-fidelity models to guide the tuning of higher-fidelity surrogate models.
2. **Multi-Task Learning**: Simultaneously tunes surrogate models for multiple related tasks, allowing shared representations that can improve performance across all tasks.

### Practical Implementation Considerations
When discussing tuning methods in your review, consider addressing:

1. **Computational Efficiency**: Trade-offs between exhaustive tuning approaches and more efficient but potentially suboptimal methods.
2. **Robustness**: How different tuning methods affect the surrogate model's performance under various conditions or when applied to new data.
3. **Automation**: The degree to which tuning can be automated versus requiring expert intervention.
4. **Cross-Validation Strategies**: Specific cross-validation approaches (like k-fold, leave-one-out) that work well for surrogate model tuning.
5. **Ensemble Approaches**: How multiple tuned models can be combined to produce more robust predictions than any single model.

The effectiveness of tuning methods depends on the specific surrogate modeling technique being used, the characteristics of the problem (dimensionality, nonlinearity, noise), and the available computational resources. Different surrogate modeling frameworks (Gaussian Processes, neural networks, radial basis functions, etc.) often require tailored tuning approaches to achieve optimal performance.

### Regularization techniques:
Regularization techniques in surrogate modeling are methods used to prevent overfitting and improve the generalization ability of the models. When building surrogate models, there's always a risk that the model will fit the training data too closely (including its noise and peculiarities) rather than capturing the underlying relationship between inputs and outputs. Regularization helps address this challenge.

Here's a comprehensive explanation of regularization techniques in surrogate modeling:

### Parameter Penalty Methods

These methods add penalties to the model's objective function based on parameter values:
1. **L1 Regularization (Lasso)**: Adds a penalty proportional to the absolute values of model parameters. This encourages sparsity by driving some parameters exactly to zero, effectively performing feature selection. In surrogate modeling, this can help identify which input variables truly influence the output.
2. **L2 Regularization (Ridge)**: Adds a penalty proportional to the squared values of model parameters. This shrinks parameters toward zero without eliminating them entirely, helping to reduce model complexity while keeping all features. For surrogate models with many parameters, this prevents any single parameter from dominating the model's behavior.
3. **Elastic Net**: Combines L1 and L2 regularization, benefiting from both approaches. This is particularly useful when dealing with correlated input features in surrogate models.

### Smoothness-Promoting Regularization

These techniques enforce smoothness in the surrogate model's response:
1. **Derivative-Based Regularization**: Penalizes large values of the model's derivatives, encouraging smooth transitions in the response surface. This is especially important for engineering applications where abrupt changes in predicted outputs may be physically unrealistic.
2. **Tikhonov Regularization**: A general form of regularization that can be customized to enforce various smoothness constraints on the solution, commonly used in surrogate models based on radial basis functions.

### Data-Driven Regularization

These approaches use data manipulation to prevent overfitting:
1. **Cross-Validation**: While not a regularization technique itself, cross-validation helps select the optimal level of regularization by assessing how well the model generalizes to unseen data.
2. **Early Stopping**: Monitors performance on validation data during training and stops when performance begins to deteriorate, preventing the model from overfitting the training data.
3. **Data Augmentation**: Artificially expands the training dataset by adding slightly modified copies of existing data or generating synthetic samples. Though less common in surrogate modeling than in image processing, it can be useful when sampling the high-fidelity model is expensive.

### Neural Network-Specific Regularization

For neural network-based surrogate models:
1. **Dropout**: Randomly deactivates a portion of neurons during training, forcing the network to learn redundant representations and preventing co-adaptation of features. In surrogate modeling, this helps create more robust approximations.
2. **Batch Normalization**: Normalizes the inputs to each layer, stabilizing and accelerating training while also providing a slight regularization effect. This helps surrogate models train more effectively on diverse input ranges.
3. **Weight Decay**: Implements L2 regularization specifically in the context of neural network training, gradually reducing weights to prevent them from growing too large.

### Physics-Informed Regularization

These methods incorporate known physical principles:
1. **Physics-Based Constraints**: Incorporates known conservation laws, boundary conditions, or other physical principles as regularization terms. This ensures that surrogate models respect fundamental physical laws even when trained on limited data.
2. **Dimensionless Parameterization**: Reformulates the problem using dimensionless parameters, reducing the parameter space and implicitly regularizing the model through physical scaling laws.

### Multi-Fidelity Regularization
1. **Low-Fidelity Guidance**: Uses predictions from lower-fidelity models to regularize the behavior of high-fidelity surrogate models, particularly in regions with sparse high-fidelity data.
2. **Residual Modeling**: Instead of directly modeling the high-fidelity response, models the difference (residual) between high and low-fidelity models, which is often simpler and requires less regularization.

### Practical Implementation Considerations

When discussing regularization in your review, consider addressing:
1. **Hyperparameter Selection**: How to determine the optimal strength of regularization (often denoted as λ or α) through methods like cross-validation or Bayesian optimization.
2. **Problem-Specific Regularization**: How the choice of regularization should be informed by the physical characteristics of the system being modeled.
3. **Adaptive Regularization**: Techniques that vary the strength of regularization across different regions of the input space based on data density or response complexity.
4. **Regularization for Uncertainty Quantification**: How regularization affects the surrogate model's uncertainty estimates and confidence intervals.

Effective regularization is crucial for surrogate models, especially when the available training data is limited compared to the complexity of the system being modeled. The right regularization approach can significantly improve a surrogate model's ability to generalize beyond the training data, making it more useful for design optimization, uncertainty quantification, and sensitivity analysis.


### **K-fold Cross-validation**

**K-fold cross-validation** is a widely used statistical method for model validation and regularization, especially valuable in surrogate modeling where datasets are limited or prone to noise. The primary objective of this method is to assess model performance and reduce the risk of overfitting by systematically partitioning the available dataset into KKK subsets (or "folds") (Hastie et al., 2009). Training occurs iteratively, using K−1K-1K−1 folds for training and the remaining fold for validation. This process is repeated KKK times, each fold serving once as the validation set, with the model’s performance averaged across all iterations (Kohavi, 1995).

In neural network surrogate modeling, K-fold cross-validation provides a robust estimate of model generalization by ensuring that performance metrics are not overly influenced by a particular partitioning of the dataset (Goodfellow et al., 2016). Particularly in civil and geotechnical engineering applications—where datasets are frequently small and costly to acquire—this method effectively prevents models from capturing spurious correlations unique to a single training-validation split, leading to enhanced predictive reliability (Zhang & Phoon, 2022).

Furthermore, cross-validation facilitates hyperparameter tuning (e.g., learning rates, network architecture, regularization parameters) by providing reliable estimates of generalization performance, thereby improving surrogate model accuracy and robustness (Yu et al., 2022). Typically, values of K=5K = 5K=5 or K=10K = 10K=10 are employed, balancing computational cost and the robustness of performance estimates (Hastie et al., 2009).

### **References (with DOI):**

- **Hastie et al. (2009)**  
    Hastie, T., Tibshirani, R., & Friedman, J. (2009). _The Elements of Statistical Learning: Data Mining, Inference, and Prediction_ (2nd ed.). Springer.  
    **DOI:** 10.1007/978-0-387-84858-7
    
- **Kohavi (1995)** (Foundational paper on cross-validation)  
    Kohavi, R. (1995). A study of cross-validation and bootstrap for accuracy estimation and model selection. _Proceedings of the International Joint Conference on Artificial Intelligence (IJCAI)_, 2(12), 1137–1143.  
    **DOI:** 10.5555/1643031.1643047
    
- **Goodfellow et al. (2016)**  
    Goodfellow, I., Bengio, Y., & Courville, A. (2016). _Deep Learning._ MIT Press.  
    **DOI:** 10.7551/mitpress/10267.001.0001
    
- **Zhang & Phoon (2022)** (Cross-validation in geotechnical/civil engineering context)  
    Zhang, W., & Phoon, K. K. (2022). Data-driven methods for geotechnical site characterization: Opportunities and challenges. _Georisk: Assessment and Management of Risk for Engineered Systems and Geohazards_, 16(2), 145–167.  
    **DOI:** 10.1080/17499518.2021.1952617
    
- **Yu et al. (2022)**  
    Yu, Y., Yang, H., & Wang, Y. (2022). Neural network-based surrogate modeling for geotechnical engineering applications: A review. _Computers and Geotechnics_, 146, 104792.  
    **DOI:** 10.1016/j.compgeo.2022.104792


Polynomial Chaos Expansion (PCE) is a surrogate modeling technique widely employed in uncertainty quantification, representing model outputs as expansions of orthonormal polynomial basis functions of random inputs (Shahzadi and SoulÏmani, 2021). Mathematically, PCE approximates the response as shown in Equation 1, where

ψj𝜓j

are multivariate polynomial basis functions dependent on inputsX X , and coefficients

CjCj

are computed from training data, typically through regression or projection methods. This formulation allows rapid evaluation of outputs and direct computation of statistical metrics such as mean, variance, and sensitivity indices. 

Equation completed in PCE


PCE is advantageous in civil and geotechnical engineering due to its transparent mathematical structure, offering interpretable polynomial terms that naturally quantify input contributions to output variance. It efficiently calculates statistical properties and failure probabilities, particularly beneficial in reliability analysis of foundations, slopes, and dams. For example, PCE-based models have been effectively applied to propagate uncertainties in soil properties through finite element analyses, enabling rapid sensitivity and reliability assessments. Furthermore, PCE models facilitate probabilistic assessments in structural analysis and geotechnical simulations, including evaluations of dam deformation under uncertain soil conditions and the reliability of slope stability, significantly reducing computational effort compared to full numerical simulations. 

However, the principal drawback of PCE is the curse of dimensionality. The number of polynomial terms expands rapidly with increasing input dimensions and polynomial order, significantly raising computational demands and the required volume of training data. This can limit practicality for complex or high-dimensional problems unless dimensionality reduction or sparse approximation methods are employed. Additionally, selecting appropriate polynomial families (such as Legendre or Hermite) matched to input distributions and regularising the expansions to prevent overfitting necessitate substantial mathematical insight. Implementing adaptive sparse techniques or advanced regression methods can help alleviate these issues but add complexity to model construction. 

Nonetheless, for moderately dimensional and smooth-response engineering problems, PCE remains an efficient, theoretically sound approach, providing both accuracy and valuable insights into uncertainty contributions.