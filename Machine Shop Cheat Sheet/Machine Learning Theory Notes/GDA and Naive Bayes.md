### **Discriminative vs. Generative Learning Algorithms**

### **Discriminative Learning Algorithms**

- **Overview**: These algorithms model the decision boundary between classes directly.
- **Example**: Logistic regression.
    - Uses gradient descent to find the best decision boundary that separates the classes.
        
    - Maximizes the conditional likelihood, _P_(_y_∣_x_).
        
        𝑃(𝑦∣𝑥)
        

### **Generative Learning Algorithms**

- **Overview**: These algorithms model the distribution of individual classes.
- **Example**: Gaussian Discriminant Analysis (GDA).
    - Builds a model of what each class (e.g., malignant vs. benign tumors) looks like.
        
    - Uses the total data distribution to estimate class distributions.
        
    - Maximizes the joint likelihood, _P_(_x_,_y_).
        
        𝑃(𝑥,𝑦)
        
    - Typically more efficient with small datasets under the assumption the data distributions are well understood (e.g., Gaussian).
        

### **Gaussian Discriminant Analysis (GDA)**

- **Assumption**: Features _x_ given a class _y_ follow a Gaussian distribution.
    
    𝑥
    
    𝑦
    
- **Process**:
    
    1. **Model Class Distributions**: Estimate Gaussian parameters for each class (mean _μ_ and covariance Σ).
        
        𝜇
        
        Σ
        
    2. **Classification**: At test time, compute the probability of new examples belonging to each class using Bayes' Rule and choose the class with the highest probability.
        

### **Naive Bayes for Spam Classification**

- **Overview**: A simple probabilistic classifier based on applying Bayes' theorem with strong (naive) independence assumptions between the features.
    
- **Feature Representation**:
    
    - Emails are converted into a binary vector representing the presence or absence of certain words.
- **Model**:
    
    1. **Model 𝑃(𝑥∣𝑦)_P_(_x_∣_y_)**: Probability of features given the class.
    2. **Model 𝑃(𝑦)_P_(_y_)**: The class prior probabilities.
    
    - Assumes features _xi_ are conditionally independent given class _y_ — the "naive" assumption.
        
        𝑥𝑖
        
        𝑦
        
- **Parameter Estimation**:
    
    - Parameters are estimated via maximum likelihood, typically counting occurrences in the data.
- **Classification**:
    
    - Uses Bayes' Rule to compute the posterior probability _P_(_y_∣_x_) and classifies emails as spam or not based on which category has the higher probability.
        
        𝑃(𝑦∣𝑥)
        

### **Key Comparisons**

- **GDA vs. Logistic Regression**:
    - GDA assumes a Gaussian distribution of features within each class and can be more effective if the assumption holds due to its efficiency and fewer data requirements.
    - Logistic regression does not assume any specific distribution for the features and is more robust to deviations from model assumptions.
- **Naive Bayes vs. Logistic Regression**:
    - Naive Bayes is computationally efficient and works well with discrete data. It makes strong independence assumptions, which are often violated but can still provide reasonable performance.
    - Logistic regression tends to perform better in practice on spam classification tasks and does not require independence among features.

### **General Insights**

- **Generative vs. Discriminative**:
    - Generative models are often more suited to smaller datasets or when the data generation process is well understood.
    - Discriminative models generally provide more flexibility and robustness, particularly with large datasets.

### **Practical Tips**

- **Model Selection**:
    - Choose generative models when you have good knowledge about the underlying data distribution and constraints on computational resources.
    - Choose discriminative models when you have access to large amounts of data or need a model that is robust to variations in data distribution.