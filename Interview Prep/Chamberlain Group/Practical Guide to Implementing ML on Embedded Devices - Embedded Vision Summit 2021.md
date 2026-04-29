- Presented at the 2021 embedded vision summit
- Pet portal won the CES 2021 smart home innovation category
- We used to need a specialized processor or gpu in order to achieve faster performance in inference
- Options have expanded greatly
- In order to start with edge inference the first thing you need to realize is that there are many decisions to be made
![[Pasted image 20241001214803.png]]

**Practical Examples of using edge inference**
![[Pasted image 20241001221124.png]]
- If you choose the google tpu for the deployment that choice dictates every other part of the stack since the tpu works best with tensor flow and TFlite
- If you use the jetson nano then it opens up more possibilities because it uses tensor rt as the inference engine which supports most models including custom layers
- For example you can allow the model to drive the choice of training framework while your hardware only influences the inference engine and the level of optimization

![[Pasted image 20241001224514.png]]

- Before we needed a strong cpu and gpu in order to do training with machine learning models
- However now, we can use digital signal processors or fpga to perform inference quickly
- Software engineers started making use of cmd vector instructions which allow multiple math operations to be able to be calculated at the same time

**There are 2 main categories of projects that can be done when putting together an inference solution**
- In the proof of concept or other low volume projects its perfectly okay to allow your model and software choices to influence the hardware you use 
- Its easy to put together a prototype using relatively low cost off the shelf components
- The primary goal in this is development speed so you'll need a well-documented and easy to use tool chain along with a robust community
- When taking a product to mass production usually you'll uncover many other factors that drive hardware choices
- These include things like manufacturer expertise, vendor roadmap, and bom cost
- Often you'll fuind that supporet for a specific deep learning model or specific inferencing engine will take a back seat to these other factors
- When hardware choice is out of your control, software developers can acheive good results with careful selection of model and software

**Example of possible work flow for choosing and optimizing your model**
![[Pasted image 20241001225539.png]]

- A good place to start is with a reference model
- The reference model is a model that produces highly accurate results 
- Speed is mostly irrelevant here because the purpose here is to prove that you training data set is up to task and that the desired solution is at least feasible
- You're almost certainly need to calculate and annotate your own training data

![[Pasted image 20241001225729.png]]

- While research data sets are great for research, they are rarely sufficient for real world use 
- Neural networks what you show them and if you don't show them some good examples of your actual use case you can't expect them to perform very well
- Research data sets often contain errors and biases which you'll want to avoid
- You don't have to completely abandon open data sets if you supplement your own data with the data from the open data sets it will increase diversity you can think of it like a form of data augmentation 
- Note that for smaller models a smaller data set is usually okay since overfitting becomes less of a risk as you decrease the parameter count
- A smaller model is often incapable of memorizing your data set but you'll still want to have a good training validation split for testing
- Once you have you reference model, you might want to use a structurally different model for your deployment
- Most deep learning models can be though of as two different and independent parts that can be swapped around

![[Pasted image 20241001230430.png]]
- **Backbone** is responsible for extracting interesting features from raw input data while the purpose of the **head and neck** is to interpret the features and produce meaningful results
- You'll need a lot of data to successfully train a backbone from scratch
- For vision tasks this means millions of annotated inputs
- However, there's a lot of generalization in how most neural networks learn
	- You can save time by loading your backbone with pre-trained weights even if those weights were trained on a completely different task
	- Its possible to attach a completely different head and neck to this backbone and continue training from that point
	- This is essentially a form of transfer learning which is referred to as fine tuning
	- Things to consider when choosing a backbone
		- Accuracy for your task
		- Memory capabilities of your target hardware 
			- weights are stored in non volatile memory
			- and for activations which require ram
			- Some backbones only work with certain training frameworks and uncertain run times
- When choosing a head and neck you only have to worry about compatibility with your desired training framework and run time
	- accuracy and speed should not be neglected

**Optimization Options**
![[Pasted image 20241001231133.png]]

- Top two rows relate to the model
	- they require you to choose between accuracy and speed
- Third row focuses on inference engine
	- This can usually give you some type of speed boost with no sacrifice to accuracy
- Bottom row focuses on data
	- Allows you to boost accuracy without losing any speed
- Best place to start is to find some good out of the box components
	- Find an inference engine that is already optimized to your hardware
	- Find a pre-optimized or pre-trained model from your inference engine's model zoo
- After you get this working you'll need to move up a level to amateur
	- Often you can find a community supported implementation of a model that comes from a more recent academic paper
	- These usually work but often you'll find incomplete documentation, some bugs, library compatibility issues that need to be resolved
	- Automated quantization pruning and compression are becoming more attainable with the use of a variety of open source tools
	- These tools are still a bit tricky to use
	- Commercial tools like deeplite and edge impulse helps smooth out this sutomated optimization process
	- For an inference engine you can consider an community supported run time with additon optimizations
	- Annotate your own data to fine tune your data
- Pros can mix and match the head and backbone or code it yourself
- You'll need to convert and compile you trained model so that it can be deployed to the edge
- You need to make sure your run time uses the the features of the cpu
	- will the runtiome use memory in such a way as to maximize cahcing
	- Higher FPS could be less accurate and lower fps could be more accurate so you'll have to find the sweet spot
- Reserve cpu cycles for fetching data and transmitting results
- You also have give the cpu a break to let the heat dissipate

**Example of Implementing ML Object detection on Embedded Devices**
- Vehicle detection in the garage 
- Focus was to run object detection on an arm coretx-a cpu without any peripheral accelerators
- Nathan decided to test the model on a few arm based dev boards such as the Seeed NPI, Asus Tinerkboard and Raspberry Pi 2B
- The tinkerboard's clock speed is only double that of the raspberry pi the performance is actually four times as fast so what's going on?
	- Both are 32 bit rmv7 architecture
	- The cortex a17 on the tinker board has some other speed boosts over the cortex a7 found on the other 2 boards
	- These are the hidden fundamentals 
	- The difference is that the DMIPS/MHz is significantly better on the a17 over the a7
	- That difference is reflected in the practical real world inference speed for all the models that were tested
	- The other result was how fast we can run object detection inference on this hardware 
	- There are faster models available like Pelee

**Conclusions**
- Inference on edge devices has become both possible and practical
- Small hardware features can make a big difference in speed
- Selecting the right model and the right inference engine for you hardware can expand the scope of what is possible
- Appropriate data will improve model accuracy