So we have a better idea about the project now and what needs to get done. I believe we have 2 main goals for the project. The first one being **To basically write out the entire paper for the Development of Neural Network based surrogate modes.** This is supposed to be a a review paper which means that the goal of the paper is to just list as many options for further research as possible. We want to give people a an idea of what options are available out there for further research.

### Task 1
What exactly is this review paper about?
To sum it up: We are trying to write a paper about different surrogate models, sampling methods, tuning methods, evaluation methods and regularization techniques that can be used to in applications of **geotechnics and civil engineering.**

A surrogate model is a model that simplifies complex equations and processes that are used for analysis that usually takes hours or even days for run. Whereas a surrogate model would only take key features from this expensive model, take the inputs and outputs from the expensive model to create input and output pairs and then use it to train the surrogate model. 

We are submitting this paper to the Machine Learning and Data Science in Geotechnics paper so we want to explore different machine learning models that can be used to predict different outcomes in civil engineering/geotechnics. Different models can be used for different use cases so maybe beam deformation, or predicting shear strain, or predicting stress, etc.

We don't actually want to develop these models. We just want to be able to be able to identify different use cases of surrogate models in the area of civil engineering and geotechnics. Surrogate models can also just be mathematical equations but we want to focus specifically on machine learning models which are also surrogate models.

### Task 2
This is the one you can rinse out to linkedin and twitter. Let's keep this one pretty simple. Kacper used an LSTM to take a series of points on a deformed beam and predict the next point on the beam. We want turn this model into a sequence-to-sequence model which means to take a series of points and predict the next series of points instead of just one point