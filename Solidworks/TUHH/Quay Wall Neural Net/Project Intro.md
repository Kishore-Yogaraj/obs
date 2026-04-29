We are going to attempt to finish the second project in the month of March and half way into April if possible.

This second project is about using Neural Networks to do an analysis on Quay Wall Deformation. Kacper mentioned something about LSTMS, but I'm not entirely sure how LSTMs work and how they would be used for this project. I heard about something with Transformers that can be used but again, I don't know much about Transformers either to know how it should be used for the project. 

As far as I know based on what I talked to Kacper about, is that a quay wall is a wall setup for boats to dock against.

But what makes them so special?

### What is Quay Wall?

Quay walls are just vertical or near-vertical structures built along the shoreline of a harbor, port or navigable waterway. They act as an interface between the land and water which allows ships to dock alongside for loading and unloading cargo or passengers. This is similar to how you would park a car against a curb.

Quay walls are designed to withstand enormous horizontal forces. When massive container ships docks against a quay wall there are large amounts of lateral pressure which the wall must absorb without damage.

Quay wall acts as the boundary between two environments, land and water. So it must be able to withstand the pressure of the soil from behind while also dealing with tidal variations, wave action and seismic forces. **We want to predict how these quay walls deform in the soil**

Quay walls also experience a lot of vertical load from cargo handling equipment on top, such as massive container cranes which can weigh hundreds of tons. Quay walls also need to be durable because they are exposed to harsh marine environments with saltwater corrosion, freeze-thaw cycles in colder regions and biological deterioration. It should service a life of 50-100 years

### Project Goal
The goal of the project is to write a state-of-the-art review paper for the Machine Learning and Geotechnics journal. The exact goal of the paper is: **Development of a Neural network-based surrogate models**.

The general goal is to write a review of how surrogate model can be developed without actually doing it. So this should be purely theoretical with references

This is interesting because what exactly am I going to end up developing for this project?

### What is a surrogate model
The surrogate model is essentially the machine learning model. Or to be more accurate the machine learning model acts as the surrogate model.

In engineering people often deal with with systems that are very expensive to evaluate. For example, a finite element analysis of a quay wall could take hours or even days to complete a single simulation which can become problematic when you want to run hundreds or thousands of simulations especially when you want to optimize a design or perform an uncertainty analysis.

Instead of running the expensive models multiple times you can use a surrogate model instead. This would run in seconds rather than hours. First you would run the expensive model a small number of times with different input parameters and you'll get different values of input and output pairs which become your training data. You then use this data to train a mathematical approximation (your surrogate model) which can quickly predict what the expensive model would output for new inputs.

Neural networks can be especially powerful as surrogate models because they can capture complex non-linear relationships between inputs and outputs.

For this project I'm likely going to use neural networks for the surrogate model to replace the computationally expensive techniques we currently use. The neural network would most likely take inputs like soil properties, wall dimensions, loading conditions, and water levels to predict deformations that would otherwise require really lengthy simulations.

### The Paper
It looks like Kacper wants me to do a lot of research in the following categories:
- Types of surrogate models
- Sampling methods
- Evaluation metrics
- Regularization techniques
- Types of Neural Networks
- Activation Functions

We should find references and write about all the different methods we currently have on surrogate models to predict quay wall deformations.



Prioritize the paper
Include pros and cons
Describe what should be used for each use case