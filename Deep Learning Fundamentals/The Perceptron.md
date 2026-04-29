
### Neural Networks - Overview
- Inspired by neural networks in the human brain and vaguely simulate how a human learns
- A **perceptron** is the most basic form of a neural network
- The perceptron, is analogous to the biological neuron of a brain
- The perceptron is trained to receive inputs in the form of input nodes and transfer the appropriate output
	- Similar to how with neurons the dendrite receives electrical signals while the axon branches out to multiple axon terminals to send the appropriate signal
- Many neurons form a biological network similar to how multilayered perceptron forms a fully connected neural network
- **Back propagation** is what allowed neural networks to figure out patterns that are far too convoluted and far too complex for a human to extract
- **This section will dive deeper into the workings of a perceptron, weights and gradient descent**

![[Pasted image 20240826164207.png#center|650]]

### Machine Learning
- Machine Learning is the concept of building computational systems that learn over time based on experience
- Uses a variety of algorithms to describe and analyze data to learn from it and improve and predict useful outcomes
- **Supervised learning** is a popular machine learning technique that acts as a guide to teach the algorithm how to draw a conclusion
	- Supervised learning typically begins with a dataset that's associated with labeled features which define the meaning of our data
	- Finds patterns that can applied to an analytics process
- **Unsupervised learning** is when all the learner gets is a large data set with no labels and its job is to process the data, find similarities and differences in the data provided and act on it depending on the required output

![[Pasted image 20240826165533.png#center|600]]

### Supervised Learning Example
- **Consider the task of learning to classify handwritten digits**
- The learner has to figure out a rule for labelling a newly handwritten digit
- The supervised learner would receive a handful of numbers with the correct labels which denote the number that each image represents
- The learners is being **supervised** as it is provided the extra information in the training and then through CNNs it learns the the relationship between the labeled data and the 

![[Pasted image 20240826170324.png]]

### Classification
- Classification predictive modeling approximates a mapping function which predicts the class or category for a given observation
- **Simply put:** Takes some kind of input x and classifies it and maps it to some category
- Generally based on previous data sets, it would calculate the likelihood of something belonging to a certain class
**Classification Example**
- Take 3 people
	- 2 mmol/L, 20 years old (A)
	- 11 mmol/L, 60 years old (B)
	- 5 mmol/L, 45 years old (C)
- Which ones are likely to have the disease?
- The model can be represented in a cartesian system 
- Glucose levels on the y axis and age on the x axis
- We can assign a label to each one of the patients to determine if they have the disease or not
	- Person A will be labeled with no disease
	- Person B will be labeled with the disease
- **How do we determine person C?**
	- Since classification is a supervised learning model we need previously labelled data sets so we can develop a model to calculate the likelihood of a new data 
	- If we plot more data we would be able to see that the older you are and the more blood glucose level you have you're more likely to have the disease
	- The data can be separated and classified using linear regression and a line
		-  **How do we obtain this line?**
	- New data below the line will be a different class from the ones above the lines
	- Changing the workload
- **How does the algorithm determine the line that divides the two classes?**
	- Initially the algorithm starts with a random line with some random equation
	- The algorithm will then continuously adjust the line based on the labelled data until it find the equation of a line with minimal error
		- Random equation = -0.02(x1) -2(x2) + 6
	- Here we notice that x1 and x2 are multiplied by two random constants which are known as the weights which dictate the slope of the line
		- -0.02
		- -2
	- "+6" represents the bias which is where the slope intersects the vertical axis
	- Different weights and biases will create different lines
	- The initial equation looks like:
		- w1(x1) + w2(x2) + b
### Perceptron
- The most basic form of a neural network that takes inspiration from the brain
- Similar to the brain, the perceptron is a basic processing element that also receives inputs, processes it and generates an output based on the predictive model
- We can now visualize the process of perceptron using nodes
	- The model node will receive two input nodes which are the age and blood sugar x1 and x2 respectively
- Lets say we want to predict if a 30 year old person with a blood sugar level of 3 mmol/L is diabetic
- The perceptron will take the two inputs, run it through the equation and then determine the score 
- If the person's linear combination is positive it will have a score of 1 meaning that they are not diabetic
- We can reshape the perceptron model and consider the bias as an input value as well\
	- This bias value will be 1
	- Why 1?
		- The first node of the perceptron will take the inputs and multiply each input by their respective weights
		- The bias 1 is multiplied by the bias value 14 in the case of this situation
- **Activation function**
	- The activation function of a node is what defines the outputs
	- The function we've been using this entire time is the **step function**
	- The first node takes the linear combination of all the inputs and now its the step function which checks the result of the linear combination 
	- If its positive the point is below the lines and is assigned a label of one otherwise if the score is less than 0 the point is above the line and assigned a label of 0
	- **To summarize**
		- The input nodes linear combination is computed in the model node whose result is then processed into the activation function, which then predicts the output
		- All of this makes up the perceptron
![[Pasted image 20240828115851.png#center|600]]


> [!NOTE] Perceptron in my own words
>- The perceptron is a single layer neural network and a multilayered perceptron is called a neural network
>- The perceptron consists of 4 parts
>	- Input values
>	- Weights and Bias
>	- Net sum
>	- Activation Function
> ![[Pasted image 20240828120310.png]]
>  - The input values are multiplied by the weights and bias and then are run through the model node which is some calculation of the weighted inputs
> 	 - In the above example the model node was a calculation of the linear combination
> 	 - **Input with the highest weight has a greater effect on the score. In our case blood sugar has a greater effect on which class the new inputs fall into**
>  - Finally the activation function is what defines the output values
> 	 - This essentially means it takes the value that's outputted by the model node and runs it through a function to determine which class it belongs to
> 	 - In the example we've talked about so far if the output is positive, the step function assigns that input a value of 1 meaning it belongs to the class of people without diabetes
>  - **Perceptron's are usually used to classify data into two parts. Therefore, it is also known as a linear binary classifier**

### Error Function
- Display a random line with some random equation its then going to look at this line and identify any errors and categorize the following data sets
- Based on these errors it will adjust the weights until the line fits the equation
- The random line will be drawn on our labelled data, the linear model will then predict the value for each of the labelled values
- If it notices that one of the predicted values does not match the labelled values, it will then adjust the line to ensure that the predicted values and the labelled values all match up
- They key metric to solving this problem is the error function which tells us if the model is doing well or not depending on how large or how small the error is
- Through **gradient descent** the network is going to tiny steps to improve the linear model in every iteration
- These tiny steps correspond to the learning late which needs to be sufficiently small because when the line is fixing it we don't want it to move too aggressively in one direction
- These iterations eventually lead us to the perfect linear model

### Sigmoid - How does it actually calculate the error?
- To do this we need a continuous error function called **E**
- Each misclassified point gets a big penalty
	- Size of the point reflects how big the penalty is for that point
- The total error is equal to the sum of these penalties associated with each point 
- We can assume a total error that is very high 
- We can move the line in the direction with the most errors. We keep doing this until all error penalties are sufficiently small and we adjust the weights of our linear model to better classify the points and thereby minimizing and decreasing our total error sum

![[Pasted image 20240828134922.png#center|500]]
- Instead of a step function we can use a sigmoid function to determine the probability of someone being diabetic
- Take the value of our linear combination and sub it into the sigmoid function which can tell us the likelihood of someone having the disease
- We will continue with using the sigmoid function rather than the step function moving forward to get a more accurate look at the errors
- If the result of the sigmoid function is > 50% that person is more likely to be healthy where as if the result of the sigmoid function is <  50% that person is more likely to be diabetic

### Cross Entropy
- **How does the computer specifically determine which linear model better classifies our data?**
- The best model is the one that gives us a the higher probabilities for the points that we know should be in the positive region and the lowest probabilities to the points we know should be in the negative region
- First we need to calculate the probability of the points being what they actually are
- All the probabilities correspond to the likeliness of something being blue of being in the positive
- For the red points we need to determine their probability of them being red, not blue
- The error can be written as the summation of natural logarithm of each probability
$$
ln(P(red)) + ln(P(blue))
$$
- If we apply this equation to both diagrams we get a negative number for both which makes sense since the log of a number between 0 and 1 results in a negative value
- What we can do here is take the negative of the summation to see which graph has the bigger error
![[Pasted image 20240828170505.png#center|600]]
- We can see that the graph on the left has a much larger error
- **The summation of logarithms is what we refer to as cross entropy**
- **The more incorrect our model is in separating our data, the larger its cross-entropy value and this the larger the error**
- **In the code we have to check to see how well our current random line separates our data**
- When points are incorrectly classified have a larger error penalty which refers to how we can calculate if one graph provides a more accurate result than the other
- **Equation**
$$
-Σyln(p) + (1-y)(ln(1-p))/m
$$
- If our point is labeled blue, if we tested this person and they are healthy, it will have a label of 1
- If our point is red than y will be 0
- We divide by the total number of points by m afterward as we calculate for average in the error rather than the summation of the error
- **Gradient descent** will allows us to keep minimizing the error and obtain a linear model 

### Gradient Descent
- We will use gradient descent to minimize the error so that we can get a line with minimal errors
- What we need to do is first take its gradient, its derivative with respect to the weights, if we were to add the gradient to our linear parameters, w1, w2 and the bias would result in new weights and biases of a line with a much higher error function
![[Pasted image 20240828174146.png#center|500]]
- By subtracting that value **(derivative of the error)** by **([w1, w2, b])**, this tells us the value that's going to decrease the error function the most
- This results in a linear model with a smaller error
- This iteration will keep going on over and over minimizing the error function, eventually obtaining a line with a small enough error that correctly classifies our data
$$
gradient-of-error-function = ((pts*(p-y))/m)*0.01
$$
- We want to take very small jumps between each line that's drawn 
- To account for this we can scale our gradient down by multiplying it with what's called a learning rate which is normally a small value 

### Perceptron Summary After Review - Diabetes Predictor
- **Single Layered Neural Network**
	- Has one layer of neurons
	- **A neuron performs two steps**
		- Takes the inputs and multiplies by the weights and sums all of it up
		- Passes the weighted sum onto the activation function which then determines the final output of the neuron 
	**Example Of A Single Layered Neural Network**
	![[Pasted image 20240906231345.png]]


> [!NOTE] Something to Note
> - What we learned about the perceptron is how to create a perceptron when given a set of labelled data
> - Actually using the perceptron to make predictions is the easy part
> - However, developing the algorithm to train the perceptron on a set of data is what makes it complicated

**Here is what a practical perceptron may look like**
![[Pasted image 20240907003459.png]]
- The inputs are the age, glucose level and the bias 
- These inputs are then sent to the neuron which calculates the weighted sum and passes it onto the activation function
- The activation function then classifies whether a person is diabetic or not

> [!question] The linear model is what classifies the data into two groups. The linear model is first determined at random and then slowly adjusts itself until it reaches the optimal model that classifies most points correctly. The linear model is adjusted by changing the weights. How does the linear model know to adjust the weights?
> ![[Pasted image 20240907003926.png]]
> - The algorithm will start with a random linear model
> - Calculates the error associated with this model
> - Readjusts weights to minimize the error and properly classify the data
> - The linear model will move towards points that have a higher error eventually correcting itself to the optimal linear model


> [!question] How do we determine which points have the largest error given the current linear model?
> ![[Pasted image 20240907004257.png]]
> - To do this you need a continuous error function
> - This error function will assign a penalty to each misclassified points
> - To do this we use the sigmoid as the activation function instead of a step function
> 	- The goal of the activation function is to not provide a yes or no but to provide the likeliness of an outcome (ie. How likely is it for this person to be healthy given a set of conditions)


> [!question] Now we have a better way to classify each set of points and can use it to classify our data with more detail. Through out the iterations, how will the algorithm determine which linear model better classifies our data?
> ![[Pasted image 20240907004805.png]]
> - First note that we want a linear model that gives higher probability to points we know are supposed to be healthy people (likeliness of a point being blue)
> - To do this we use **cross entropy** which is the **summation** **of logarithms of the probability of a certain point being its actual values**
> - Cross entropy is our error value or "E"
$$
	ln(P(red)) + Ln(P(blue))
$$

> [!question] So now we determined a value for E. How do we minimize this?
> - We minimize this using gradient descent
> - What we need to do is first take its gradient, its derivative with respect to the weights, if we were to add the gradient to our linear parameters, w1, w2 and the bias would result in new weights and biases of a line with a much higher error function
> -  By subtracting that value **(derivative of the error)** by **([w1, w2, b])**, this tells us the value that's going to decrease the error function the most
> - This results in a linear model with a smaller error
> - This iteration will keep going on over and over minimizing the error function, eventually obtaining a line with a small enough error that correctly classifies our data
> - We want to take very small jumps between each line that's drawn 
> - - To account for this we can scale our gradient down by multiplying it with what's called a learning rate which is normally a small value 
$$
gradient-of-error-function = ((pts*(p-y))/m)*0.01
$$

![[Pasted image 20240828174146.png#center|500]]
**Code for Training Algorithm for Perceptron**

```python
import numpy as np
import matplotlib.pyplot as plt

def draw(x1, x2):
    ln = plt.plot(x1,x2)
    
def sigmoid(score):
   return 1/(1+np.exp(-score))

def calculate_error(line_parameters, points, y):
    m = points.shape[0]
    p = sigmoid(points*line_parameters)
    cross_entropy = -(1/m)*(np.log(p).T * y + np.log(1-p).T*(1-y))
    return cross_entropy

#Function to calculate the gradient descent over 500 iterations
def gradient_descent(line_parameters, points, y, alpha):
    m = points.shape[0]
    for i in range(2000):
        p = sigmoid(points*line_parameters)
        #Subtracting this value from the line parameters will provide new parameters with a lower error value
        gradient = (points.T * (p - y))*(alpha/m)
        line_parameters = line_parameters - gradient
        w1 = line_parameters.item(0)
        w2 = line_parameters.item(1)
        b = line_parameters.item(2)
        x1 = np.array([bottom_region[:, 0].min(), top_region[:, 0].max()])
        x2 = -b / w2 + x1 * (-w1/w2)
    draw(x1, x2)
        

n_pts = 100 #Number of points
np.random.seed(0) #Gives us the same random points
bias = np.ones(n_pts)
random_x1_values = np.random.normal(10, 2, n_pts) #Identify center of normal distribution, standard deviation and number of points needed
random_x2_values = np.random.normal(12, 2, n_pts)
top_region = np.array([random_x1_values, random_x2_values, bias]).T #Allows for x1 and x2 values of one point to be on each row
bottom_region = np.array([np.random.normal(5, 2, n_pts), np.random.normal(6, 2, n_pts), bias]).T
all_points = np.vstack((top_region, bottom_region))
line_parameters = np.matrix([np.zeros(3)]).T #We don't want to hard code the weight so we assign the initial value a 0
y = np.array([np.zeros(n_pts), np.ones(n_pts)]).reshape(n_pts*2, 1)

#Allows for multiple plots on the same figure
#"ax" is the axes object and allows us to control everything about the individual plot
_, ax = plt.subplots(figsize=(4, 4)) #4 inches wide x 4 inches high
ax.scatter(top_region[:, 0], top_region[:, 1], color='r')
ax.scatter(bottom_region[:, 0], bottom_region[:, 1], color='b')
gradient_descent(line_parameters, all_points, y, 0.06)
plt.show()

```

### Overall Flow of the Training Algorithm

1. **Initial Data Generation**:
    - A **set of points** is generated and plotted, with two classes: 0 (blue points) and 1 (red points). These represent the data that the algorithm will use to fit a logistic regression model.
    - These points are randomly generated, and the actual class labels (`0` or `1`) are predetermined based on how the points were generated (i.e., top and bottom regions).
2. **Initial Random Line (Parameters)**:
    - The algorithm starts with **random parameters** for the decision boundary (a line), initialized to zero. This decision boundary (the line) is what the model will adjust over time to separate the two classes.
    - **Note**: The initial line might not be plotted, but the weights are initialized as zeros, meaning the line could be in a "random" or ineffective position (like a horizontal line at first).
3. **Probability Calculation**:
    - For each point, the algorithm calculates the **probability** of it belonging to class 1 using the **sigmoid function** based on the current parameters (weights and bias).
        - The sigmoid function converts the weighted sum of the inputs into a probability between 0 and 1.
        - Initially, with random parameters, these probabilities won't be very accurate.
4. **Cross-Entropy Calculation**:
    - The algorithm computes the **cross-entropy loss**. This loss function measures how well the current probabilities match the true class labels (`0` or `1`).
    - The goal of the algorithm is to minimize this loss by adjusting the decision boundary (the line).
5. **Gradient Descent (Parameter Update)**:
    - The algorithm uses **gradient descent** to adjust the parameters (weights and bias). It calculates the **gradient** of the cross-entropy loss with respect to each parameter.
    - Based on the gradient, the algorithm updates the parameters to **reduce the cross-entropy loss**. The parameters are updated so that the new decision boundary will better separate the two classes.
6. **Recalculate Probabilities and Repeat**:
    - After updating the parameters, the algorithm recalculates the **probabilities** for each point based on the new line (decision boundary).
    - It then recalculates the **cross-entropy loss** and repeats the **gradient descent** step.
    - This process continues for each iteration of the loop (2000 times in this case).
7. **Decision Boundary Visualization**:
    - In each iteration, the updated decision boundary (line) is plotted on the graph so you can visualize how the line is gradually adjusted to better separate the two classes.
### Summary of Key Points:
- **The goal** is to minimize the cross-entropy loss by adjusting the decision boundary.
- **Gradient descent** is used to iteratively update the parameters (weights and bias) so that the line better separates the two classes.
- Each iteration recalculates probabilities, loss, and the gradient to move closer to an optimal solution.
\
some changes