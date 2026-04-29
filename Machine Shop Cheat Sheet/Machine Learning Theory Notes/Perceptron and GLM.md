### **Perceptron Algorithm**

- **Overview**: The perceptron is a simple linear binary classifier. It is not widely used in practice but is important for historical reasons and for its simplicity.
- **Functionality**: It predicts whether an input belongs to a certain class based on a thresholded linear function.
- **Learning Mechanism**: The perceptron updates its weights only when it makes an incorrect prediction. It doesn't account for the degree of error—only whether the classification is wrong.
Making a change on this page

### **Logistic Regression**

- **Functionality**: Unlike the perceptron, logistic regression estimates the probability that an input belongs to a particular class using the sigmoid function.
- **Sigmoid Function**: This function maps any real-valued number into the interval [0, 1], making it suitable for probability estimation.
- **Training**: Logistic regression minimizes the binary cross-entropy loss, which measures the discrepancy between the actual class labels and the predicted probabilities.

### **Exponential Family**

- **Definition**: A class of probability distributions that includes many common distributions like Gaussian, Bernoulli, and Poisson. These distributions are mathematically tractable and widely used in statistics and machine learning.
- **PDF Format**: Distributions in the exponential family can be expressed in a standardized form, which includes components like the natural parameter, sufficient statistic, and log-partition function.

### **Generalized Linear Models (GLMs)**

- **Extension of Linear Models**: GLMs extend linear regression to allow the dependent variable to follow any distribution from the exponential family, not just a normal distribution.
- **Components**:
    - **Random Component**: Specifies the distribution of the dependent variable.
    - **Systematic Component**: A linear predictor that combines input features linearly.
    - **Link Function**: Connects the mean of the distribution of the dependent variable to the linear predictor.

### **Softmax Regression**

- **Use in Multiclass Classification**: Softmax regression extends logistic regression to support multiple classes. It is particularly used when the classes are mutually exclusive.
- **Probabilities**: It converts logits, obtained by applying a linear transformation to the input features, into probabilities by using the softmax function.
- **Training**: Softmax regression typically minimizes the cross-entropy loss between the predicted probabilities and the actual class labels, facilitating model learning.

### **Key Mathematical Concepts**

- **Gradient Descent**: A method used to find the minimum of a function (e.g., loss function in logistic regression).
- **Cross-Entropy Loss**: Measures the difference between two probability distributions for a given random variable or set of events.

### **Practical Applications and Theoretical Importance**

- **Historical Context**: The perceptron was one of the first algorithms that demonstrated the feasibility of training machines to perform classification tasks.
- **Current Relevance**: While newer algorithms have surpassed the perceptron in performance and capabilities, understanding these foundational algorithms is crucial for comprehending more complex models in machine learning.