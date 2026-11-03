For this competition UWAFT will add a new battery and rear electric drive to the Cherokee, but the stock controller that comes with the Cherokee can't be reflashed. 

The current stock Cherokee pairs and engine with two motors. **Motor A** controls the 1.6 L turbocharged gas engine's speed through a planetary gearset and acts as a generator-starter and **Motor B** works alongside the first motor and the gas engine to add drive torque to the transmission output.

The rear wheels are powered using a rear-axle disconnect in order to save fuel during normal conditions. When it needs to be powered it uses a mechanical drive shaft to connect to the front-end powertrain.

Since the current stock controller can't be re programmed to provide the torque split between the front and back wheels now that we have a new rear motor we need to design our own PSC that sits between the accelerator pedal and the stock PSC and sends a signal directly to the rear motor.
- It will read the pedal signal and turn it into a total torque request
- It sends the rear share to the new rear motor
- It converts the front share into a mock signal for the stock PSC

Why do we even want a rear motor?
- Pros
	- A rear motor gives us more space to add a hybrid battery. Currently the Cherokee is not a plug in hybrid but this will make it a plug in hybrid
	- Fewer moving parts
- Cons
	- You actually lose a lot of towing and heavy duty capacity. With the mechanical set up the engine and motors directly provide power to the rear wheels using the driveshaft which you lose using a smaller rear electric motor.
	- The rear motor will most likely not provide as much horse power as the engine
	- This allows the Cherokee to tow around 3500 lbs compared to the Honda CR-V which is 1000 lbs or the Toyota RAV4 Hybrid which is 1750 lbs

- A **stock controller** is one of the computer (ECU) that Stellantis installed in the Jeep. It's the factory original
- A car has many ECUs each running software for a specific job but we are mainly concerned with the stock **PSC** (**Propulsion Supervisory Control**)
	- Propulsion is the system or force responsible for pushing the car forward or backwards
	- The PSC is responsible for taking the accelerator pedal signal and decides how much torque the factory powertrain should produce
		- **Torque** is the rotational twisting force caused by the engine or motor. The more you push down on the accelerator pedal the more torque the engine or motor is going to produce
		- **Powertrain** is the entire group of components that generate power and deliver it to the road surface to make the vehicle move forwards or backwards. So everything involved in the car's propulsion
			- Engine or motor - heart of system that creates the energy
			- Transmission - gearbox that controls and adjusts how much power and speed go to the wheels
			- Driveshaft - spinning tube that carries power from the transmission to the axles
			- Axles and differentials - components that split the power and physically rotate the wheels
- **Reflashing** means to overwrite the software stored on the existing controller. As per the the rules of the competition (E-2.15) we are not allowed 
	- This constraint is why our controller design looks the way it does. Since we can't edit the stock PSC to make it aware of the rear motor we have to influence it from the outside
	- **Normal Jeep:** The pedal sends its signal directly to the stock PSC, which sets the torque
	- **Team PSC:** The controller reads the real pedal and works out the total torque and the front/rear split and send the rear share to the new motor then send the stock PSC a mock pedal signal that asks for the front share

**Example of what we need**
For example, say the driver presses the pedal 60%, and your controller wants only part of the torque to come from the front. It could send the stock PSC a signal equivalent to a lighter pedal press, such as 35%. The stock PSC responds as if the driver had pressed that far, without knowing your controller is involved.

To choose the right mock signal, your team needs to know how the stock PSC converts pedal position into torque. That’s why Sean’s document says you need the stock PSC’s transfer function, and why one of its open questions is when you’ll get it

**Expectations after last meeting**
1. **Set up the project:** Make a new branch, start the project with `matlab.toml`, and open the model using the shortcut.
2. **Create the variant:** Turn the stock controller block into a variant subsystem, then add your modified controller as a second variant. Sean suggested the `update diagram` activation time.
3. **Research variant control modes:** Sean wasn’t sure which mode is best, so you could come back with a recommendation.
4. **Start the signal spreadsheet:** For each controller signal, define its team-convention name, unit, sign convention, and sample rate.
    - **Units:** Convert them using gain blocks, with the gain stored as a variable in the data dictionary.
    - **Sample rates:** Set them explicitly rather than using `-1` (inherited).
5. **Add the mappings (my inference):** Put one mapping before the control logic to convert plant signals to team convention, and the reverse after it. This matches Ba Son’s question and the first milestone in Sean’s document, but I can’t confirm it was assigned.