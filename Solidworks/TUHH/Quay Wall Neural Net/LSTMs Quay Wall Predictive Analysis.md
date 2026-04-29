Okay the goal of this project is to modify the code so that it performs sequence to sequence predictions rather than sequence to point predictions

In order to this we first need to understand the original code base so that we can modify wherever necessary

Take the time to understand LSTMs while you're doing this project as well

### Introduction

This code implements a **time series** prediction system using a **bidirectional LSTM neural network architecture**. It is designed to:
- Process multiple data files containing time series data
- Train an LSTM model on a portion of each time series
- Use the trained model to predict future values in the time series
- Evaluate the prediction accuracy using various metrics
- Save the results, models, and performance metrics to organized directories


> [!QUESTION] What is a time series prediction system?
> Time series data is just data that has been collected over a period of time in a specific . You can think of things like stock prices over days, weather temperature every hour, sensor data from a machine recorded every millisecond. The key idea here is that the order matters because what happened before affects what happen next. So time series prediction is the process of using past values to predict the future like predicting tomorrow's stock price based on the last 30 days
> 
> In our case, we are trying to predict how a beam deforms based on previous data about a beam with the same properties every second



> [!QUESTION] How do LSTMs remember
> LSTMs are designed with a **memory cell** that holds information across time steps. At each time step, the LSTM decides:

1. **What to remember** from the past
2. **What to forget**
3. **What to add** from the current input
4. **What to output** to the next time step

It does this through **three gates**:
- **Forget gate**: Decides what to erase from memory.
- **Input gate**: Decides what new information to store.
- **Output gate**: Decides what to output for the current step.
This mechanism lets the LSTM:
- Carry useful information forward
- Discard irrelevant stuff
- Make informed predictions based on what it's "seen" so far

---

### 🧠 How this helps in prediction

Imagine you have a time series of temperatures:

`[20.1, 20.4, 20.7, 21.0, 21.3, ?]`

A traditional network sees just the last few values (as a static chunk) and predicts `?`.

But an **LSTM** sees the **entire evolving sequence**, step by step. So when it's about to predict the next value:
- It already has an internal memory shaped by the entire sequence
- This memory might capture trends like "temperature has been rising steadily by 0.3 degrees every hour"
- It then uses that memory **plus the current input** to predict the future

That’s why LSTMs are so powerful for sequences like time series, speech, and text.

---
### 🔁 During training:
- It processes a full sequence (say 50 time steps).
- At each step, it updates its memory and predictions.
- The loss (difference between prediction and actual value) is computed.
- Backpropagation is applied through the **entire sequence** using a method called **Backpropagation Through Time (BPTT)**.

> [!QUESTION] What is a Bidirectional LSTM Neural Network?
> LSTM stands for Long Short-Term Memory. It's a type of neural network that is great with working with sequences - like sentences or time series data
> 
> What makes them special:
> - They can remember patterns over long periods of time
> - Tradition neural networks don't remember previous steps as well
> - For example: If your model is predicting the next temperature, it helps if it can remember the last 24 hours of temperatures 
> Bidirectional LSTM
> Normally, an LSTM reads a sequence from start to finish - from past to future
> A bidirectional one LSTM reads the sequence forward (left to right) and another LSTM reads the sequence backward (right to left)
> Both views are combined to make a better prediction
> In many tasks, knowing what came before and what comes after helps to make more accurate predictions. Think of reading a sentence, knowing both the context before and after a word helps understand its meaning better

## 🧠 First: What does a Bidirectional LSTM _really_ do?

A **Bidirectional LSTM** processes a sequence **in both directions**:
- One LSTM sees the sequence **left to right** (past → future)
- The other sees it **right to left** (future → past)

So for an input sequence like:

`[1:00, 2:00, 3:00, 4:00]`

At time step `2:00`, it uses:
- Info from the past: `1:00`
- Info from the future: `3:00, 4:00`
This means it’s **not predicting the future**, it’s using the **entire sequence context** to better understand each point.

---

## ✅ So… what is it _actually used for_ ?
### 🔸 1. **Imputation** (Filling in missing data)
**Example:** You have temperature readings at:

`[1:00, 2:00, __, 4:00, 5:00]`

You want to fill in `3:00`.
A **Bidirectional LSTM** can use:
- What happened before `3:00` (1:00, 2:00)
- What happened after `3:00` (4:00, 5:00)

**Why it works**: Unlike a unidirectional LSTM (which only sees the past), the bidirectional version learns from _both directions_ and can give a better estimate of the missing value.

---
### 🔸 2. **Sequence Labeling**
This is huge in **NLP** (natural language processing), where you want to label each word based on the whole sentence.

#### Example: Named Entity Recognition (NER)

`"Barack Obama was born in Hawaii"`

You want to label each word:
- "Barack" → PERSON
- "Obama" → PERSON
- "Hawaii" → LOCATION

The word "Barack" could be a name or a verb (in rare cases), but once you read the whole sentence, it’s clearly a name. So you want the model to:

- **Know what came before** a word
- **And what comes after** a word

Bidirectional LSTMs do this perfectly.

---
### 🔸 3. **Denoising / Smoothing**
In signal processing or sensor data, you sometimes have noisy sequences like:

`[10.1, 10.2, 15.0 (noise!), 10.3, 10.4]`

A BiLSTM can help smooth or clean that data by:
- Using _surrounding context_ to identify and correct outliers
- Learning that `15.0` is unlikely given both neighbors

---
### 🔸 4. **Offline Sequence Processing**

When you’re analyzing **complete sequences** (not real-time), like:
- Historical financial data
- Full audio clips
- Medical recordings (e.g. ECG traces)

You can run a BiLSTM on the **whole sequence at once** to extract rich patterns at every point.


> [!ATTENTION] Making predictions
> Strain is the independent variable and stress is the prediction. So only one feature

### Things I've Noticed at This Point

So it seems that this project uses a BiLSTM instead of a unidirectional LSTM for training. I think the reason this was chosen was because it is believed that it trains the model better and is able to develop better features. However, I find that there might be an issue with this. The big one being during inference. 

BiLSTM performs extremely well in sequence labelling and imputations however that's not what the goal here is. The goal is that we need to predict the next step. Unidirectional LSTMS are able to this a lot better. Although we can say "BiLSTM have better feature attraction since it learn the data from both directions, this might lead to overfitting. They perform very well at understanding complex pattern are more expressive but that means it won't generalize well since it's been training too much on the training data especially with two passes. 

This is especially problematic when our goal is simple, like next-step prediction. 

The other problem might be with misleading performance during training. Since during training the BiLSTM sees the future it might perform very well on validation loss. However, during the real inference, the model won't have access to future values which can lead to a mismatch between training and inference behavior.

Think about it, we trained a model to understand things a specific way and want it to understand it a different way. We should look into unidirectional LSTMs and compare.

The BiLSTM has learned to depend on patterns from both ends of the sequence and not just the past, this creates an unrealistic training set up. It might:
- Expect a symmetrical pattern
- Use the tail of the input (e.g., time_19) in a way that would only make sense if you were also going to predict earlier steps (like for imputation)

## Understanding the code so far

### Initial Setup and Configuration
The code begins by importing necessary libraries and setting up the environment:
- Data processing libraries
	- Pandas
	- NumPy
- File system operations
	- os
	- shutil
- TensorFlow and Keras for Deep learning
- Metrics and evaluations

## Problems I am discovering in the code and why we're seeing spikes in loss

### Using Wrong Activation Function
In the code we are using relu as our activation function which is not recommended. LSTMs are designed to use tanh activations internally. Using relu causes gradients to explore or vanish especially in deep RNNs. TensorFlow uses tanh as a default for a reason. It keeps our outputs bounded and smooth

### Deep Stacked BiLSTM Layers (3 Layers Deep)
Stacking 3 BiLSTM layers means you have 6 LSTMs in total. This is a heavy and expressive model which means we are going to deal with a lot of overfitting. Deep RNNs without proper regularization can will overfit and destabilize

### No validation set
We have no validation split and no validation data. This means we're monitoring the loss only on the training data. The model will be overfitting which causes the late epoch spikes. We can't tell how well the model is generalizing because it's comparing predictions with training data that has already been trained. We need to add a validation split and monitor the validation loss and validation rmse

### No learning rate schedule or Gradient Clipping
We have no gradient clipping which means the LSTMs will overshoot. We also have no learning rate decay which the optimizer keeps stepping at full strength even after 100s of epochs. We can add a gradient clipper to the optimizer and optionally ad a decay scheduler

### No dropout/regularization
There is no dropout between LSTM layers or in the model. In deep RNNs this means overfitting is likely, activations are not regularized and loss can spike as the model memorizes training patterns or focuses too hard on some batched. We need to add dropout and recurrent dropout inside the LSTM layers

### Script Overall Flow
The script iterates over test files containing soil data

Processes and filters data (based on skip points and known percentages)

Trains a bidirectional LSTM on the known portion of the data to predict the next single point

Uses that prediction as input to predict the next, and so on (autoregressive style)

Saves predictions, errors and model performance

### Outputs

**actual_data.txt**
A single column of the raw stress values from the filtered data set (`sigma1eff[kN/m^2])` after downsampling by point skip

This is the full list of values that were used to create the training sequence and test sequence

These match the order of the the predicted train data and predicted test data

**predicted train data**
These contain the autoregressive predictions on the training sequence (predictions made during training time, not directly compared to ground truth during training but evaluated later)

It predicts the next point, feeds that prediction back into the model as the next input, repeats until the end of the training set

**predicted test data**
Autoregressive predictions on the unseen test data

Start with n steps from the training set, predict the next point then feed it back and repeat for the entire length of the test set

### Errors when running the script for the first time
I get huge negative values when looking at the predicted test data with almost 5-6 orders of magnitude off from the actual stress values
- This leads me to believe we are working with the wrong columns?
- Error percentages are close to 1% some how even though the predicted values are in the millions
- I believe the script might be using a different column

### Understanding something
The LSTM model learns by giving it `n_steps` number of points and then we teach it given these number of previous points, this is what the next point should be. The model will train on this pattern for 80% of the dataset and the remaining 20% is used to test the model

Filtered sequence:
```python
[-1.5, -2.0, -2.6, -3.1, -3.7, -4.2]
```

Input:
```python
# X_train - input
[-1.5, -2.0, -2.6]
[-2.0, -2.6, -3.1]
[-2.6, -3.1, -3.7]
#Y_train - output
-3.1
-3.7
-4.2
```


### LSTMS are a recurrent neural network
This means that at the first step an input is provided, some computation is performed and then and output is generated. That output is then fed back into the neural network and is used to predict the next output with the use of the previous output and the input at time step 2.

RNNs have a long term dependency problem where once your start providing it with more information (more predictions) it becomes less effective at learning new things.

LSTMs provide a solution to this by providing an internal state to the RNN node

When an RNN input comes in it receives the state information as well. 

What is a state?

A state is an LSTM cell and it contains 3 gates. It has a forget gate, and input gate and an output gate.

The forget gate says what type of state information can be forgotten. The input state determines what new information can be added to the storage and the output gate determines what information should be outputted

These gates can be stored as numbers between 0 and 1. This is a percentage that determines how much should be allowed through the gate and how much shouldn't be allowed through the gate

### Why do we want to use a sequence to sequence based approach instead?
With the current approach, we end up with a couple of limitations

During inference we are feeding our own predictions back into the model. If a prediction is slightly off, it can cascade into future steps which can lead to drift or divergence over time.

The model doesn't learn long term dependencies. The model only gets supervised feedback for on future step at a time. It doesn't directly learn patterns that span multiple steps into the future.

Training and inference mismatch. During training the mode gets perfect sequences from the real data and during inference, it has to work with its own imperfect predictions. This mismatch is called exposure bias and is a common issue with autoregressive models. 

### What are the benefits of using a sequence to sequence approach
Using a sequence to sequence approach can help with reducing drift, improving long term accuracy and allowing for batched inference which is (faster)
### **1. No Error Accumulation During Inference**

**Autoregressive Problem:**  
Each prediction is used as input for the next → small errors compound.

**Seq2Seq Advantage:**  
Predicts the **entire output sequence in one forward pass**.  
→ No feedback loop, so **no cumulative drift**.

---

### **2. Learns Multi-step Temporal Patterns**

**Autoregressive Limitation:**  
Trained to predict just one step ahead — it doesn’t understand what the next 10–20 values should look like **as a whole**.

**Seq2Seq Advantage:**  
Supervised on the **entire output sequence**.  
→ Learns **longer-term dependencies** and temporal structure (e.g., oscillations, trend reversals).

---

### **3. Eliminates Exposure Bias**

**Exposure Bias = mismatch between training & inference.**

- Autoregressive: trained on clean ground truth → tested on own noisy outputs.
    
- Seq2Seq: trained and tested on the same style of data: **input → output sequence**.
    

✅ So the model doesn’t get confused by its own predictions.

---

### **4. Enables Parallel Training**

- In one-step models, you can only predict one value at a time.
    
- Seq2Seq lets you predict **multiple time steps simultaneously**, which speeds up training and inference.
    

---

### **5. More Interpretable Evaluation**

You can:

- Directly compare true vs. predicted sequences of length 20 or more.
    
- Visualize sequence alignment.
    
- Use sequence-level metrics (e.g., dynamic time warping, sequence RMSE).
    

---

### **6. More Suitable for Engineering Forecasting**

In applications like yours (soil stress evolution over time or strain):

- You likely care about the **entire future curve** (not just the next point)
    
- Seq2Seq allows better modeling of mechanical system behavior over multiple steps, which is closer to **how you'd simulate or forecast stress evolution** in real-life testing scenarios.


### Classic Failure Mode
![[Pasted image 20250413183158.png]]
RMSE: 1437198.4933
Mean Percentage Error: 31.37%
Number of test points: 151

### Things to consider when testing the sequence to sequence
We should test two main things:

This is the best approach

**The first one is to compare how well it performs when we use the autoregressive technique except with 20 points as the input into the recurrent neural network instead. This is supposed to smooth our the predictions so the error grows slower compared to the one step autoregressive model**

The second one is to use a hybrid approach where the input is some of the ground truth values and some of the predicted values. However this method doesn't make sense because if we already know what all the points are, what's the point in using machine learning to predict the points

The other solution is to predict all 20% of points at once. However this requires a very large dataset (close to 10000-50000 sequences)

### Things that we can do to improve the current approach
## ✅ 1. **Scheduled Sampling**

### 🧠 What It Solves:

**Exposure bias** — the mismatch between training (on clean data) and inference (on model’s own predictions), which causes drift in autoregressive or sequence-based models.
### 📦 How It Works:

During training, when feeding a sequence to the model:

- **Instead of always feeding the true previous value**, you **sometimes feed the model’s own prediction** from the previous step.
### ✅ Why It Helps Your Case:

- Soil stress curves are often smooth, but once the model starts to drift, the physical realism is gone
- Scheduled sampling helps the model **self-correct** early on during inference, especially in long horizons like 150-step predictions
### ⚠️ Caveat:
- Requires custom training loop (can’t be done with `model.fit()` alone)
- More complex to implement in Keras, but doable with TensorFlow’s `tf.GradientTape`
  
  
  ## ✅ 2. **Long Horizon Training**

### 🧠 What It Means:

Instead of training the model to predict just 1 or 20 steps, you **train it explicitly to predict long sequences** — 50, 100, 150+ time steps — from the beginning.

> The model learns to see longer trends and becomes more robust to small short-term noise.

### ✅ Why It Helps:

- Forces the model to learn **global patterns** in stress evolution (e.g., strain softening, phase transitions, plateaus)
- With one-step models, the loss function only penalizes short-term error. But long-horizon training penalizes **sequence-level deviation**.

### ⚠️ Trade-off:
- You need **much more data**
- Model may struggle to learn if your sequences are noisy or highly nonlinear

## ✅ 3. **Transformer Models (like Time Series Transformer or Informer)**

### 🧠 What Makes Transformers Different:

Transformers:

- Don’t use recurrence (unlike LSTMs)
- Use **self-attention** to weigh every input point in a sequence, regardless of how far back in time it is

So they can:
- Model **long-range dependencies** better
- Handle inputs and outputs of arbitrary length
- Are **parallelizable** (faster training)

### ✅ Why Transformers Help in Your Case:

Soil stress data can have:
- **Long-term dependencies** (e.g., effects of early strain on late-stage behavior)
- Non-local behavior (e.g., pore pressure suddenly affecting stress)
- Transformers are **better at remembering and leveraging earlier points**

### Example:
A Transformer can more easily learn:

> “Whenever stress decreases rapidly at time `t0`, a plateau happens around `t0 + 120`.”

An LSTM would struggle with that due to its limited memory and vanishing gradients.

### Results
Performed a lot better with 4 point skips already

I wonder if we can enable GPU for faster training times, reduce the number of LSTMs used and change the activation function and see what results we get.


### Improvements
- **Use a smaller output size**
- Train on overlapping targets
- **Use scheduled sampling in training**
- Use sequence to sequence with attention
- Tune Hidden Units and layers
- Number of LSTM layers
- Add a dropout

### Progress
- Got sequence to sequence to work
- Training runs on GPU now
- Removed bi directional (because of GPU)
- Optimized for inference script, and training script separately
- Using tanh instead of relu in order to use GPU
- Might need strong GPU for further testing
- If you want to use relu you have to use tanh
- A lot slower because we are using custom training loop instead of Keras traditional training loop
- We should also train the original model with GPU and see if its faster
- Train the original model with tanh
- Train it with 2 LSTM layers
- Train it without bidirectional LSTMs
- Train the scheduled sampling method with the original training method
- **No point in using GPU because it runs fast enough on cpu**

### Changes being made to train_seq.py
- Took out bidirectional to see if the model performs better
	- Did not perform better
- Use tanh to see if the model performs better
	- Removed relu
	- Was terrible
- **The current best model for the training script is to use a unidirectional LSTM with sequence to sequence predictions**

**Different architectures**
Different input and output sizes
- 20 input and 50 output
- 50 input and 20 output
Different parts of the curve for training
- 20% (compare for original)
- 50% (compare for original)

### Testing for single layer with different units
#### 1 Layer
**5 units**
- Time - 122.63 seconds
- Error - 8.15%
**25 units**
- Time - 213.20 seconds
- Error - 8.28%
**50 units**
- Time - 139.80 seconds
- Error - 7.87%
**50 Units again**
- Time - 541.72 seconds
- Error - 8.94%
#### 3 Layers
**50, 25, 15**
- Time - 293.17 seconds
- Error - 8.93%
**25, 25, 25**
- Time - 370.67 seconds
- Error - 9.39%
### New Goals
Our new goal is to be able to train an LSTM model to understand multiple different curves by learning the entire curve and then giving it 80% of a curve it has not seen before and predict the next 20%

This is a generalized LSTM model we are trying to achieve

### Steps to Achieve this
We can't train 500 different soil samples so we're going to start off with 10

First we need to write a script that plots all of the curves from these soil samples to see what we're working with
- This is simply just to see what kind of curves our model was trained on 
- After that we need to plot the curve of the soil sample that we plan to use to see if it fits within the trained data

After this we use the original sequence to sequence model to train on the new sample and check the error of the last 20% of the curve

Then we give the new inference script 80% of the data set and ask it to predict the next 20%. If it performs better than we did well.

### Changes in the generalized model script

## ✅ 1. **Epochs: 300 instead of 5000**

### Original Setup:

- **5000 epochs** + **patience = 500** was designed for **training on a single curve**, where learning was slower and prone to overfitting.
    
- Long training allowed convergence even if the loss curve was noisy due to limited data.
    

### Updated Setup:

- **Much larger dataset**: You're now training on thousands of `(X, y)` pairs across multiple files.
    
- **Model converges faster** on larger and more diverse datasets.
    
- **300 epochs + EarlyStopping** (patience = 20) often gives a good balance:
    
    - Avoids overfitting.
        
    - Stops training once the model plateaus.
        

### Suggestion:

If your training loss still decreases after 300 epochs, increase to 500–1000. But **don’t jump to 5000** unless you see underfitting — it wastes compute and can lead to overfitting.

---

## ✅ 2. **Patience: 20 instead of 500**

### Original:

- **Patience of 500** made sense when training lasted 5000 epochs — it waited for a long time before triggering early stopping.
    

### Updated:

- With fewer epochs and more data, the **loss curve is smoother**, and **training becomes more efficient**.
    
- **Patience = 20** means:
    
    - If loss doesn’t improve for 20 consecutive epochs, training stops.
        
    - Helps avoid overfitting and wasted compute.
        

### Suggestion:

You can tune this between 10–50 depending on how stable the loss curve is.

---

## ✅ 3. **2 LSTM layers with 64 units vs 3 layers with 25 units**

### Why change the model?

|Original|Updated|
|---|---|
|3 LSTM layers|2 LSTM layers|
|25 units per layer|64 units per layer|
|Smaller model|Slightly deeper capacity|
|Less data, fewer patterns|More data, needs more expressive power|

### Rationale:

- In a generalized setting, the model must learn **more complex relationships** across soil types — this requires:
    
    - More hidden units to capture richer patterns.
        
    - Slightly **wider** LSTM layers to increase model capacity.
        

### Why fewer layers?

- Going **deeper isn’t always better**. With 3 layers of 25 units:
    
    - You add more depth but not much expressive power.
        
    - It can cause **vanishing gradients** or unnecessary complexity.
        
- **2 well-tuned layers with higher capacity** is often more effective and easier to train.
    

---

## ✅ Summary of Changes

|Setting|Original (Single Sample)|New (Generalized Model)|Why?|
|---|---|---|---|
|Epochs|5000|300|Faster convergence, more data|
|Patience|500|20|More stable loss with big data|
|LSTM Layers|3 layers × 25 units|2 layers × 64 units|More expressive, better gradient flow|

---

## ✅ Final Thoughts

These are **starting defaults**, not hard rules. You should:

- Monitor training loss and RMSE.
    
- Try grid-searching over architectures: e.g., 2 vs 3 layers, 32 vs 64 units.
    
- Use validation data to tune patience and early stopping.


### Performance
sample 12 and 13 performed well
sample 11 and sample 9 did not
Bigger dip more error
