### Turn the stock controller into a variant subsystem
We want to do this so that we can add the team's controller beside it a switch between them for when we need to use the stock controller vs the team controller.
- Remember that the team controller will be responsible for taking an input from the accelerator and split the torque between the front wheels and the rear wheels

#### Setting Which Variant to Use

```python
blk = 'ConfiguredVirtualVehicleModel/Controllers'; set_param(blk,'LabelModeActiveChoice','ModifiedController')
```

### CntrlRx and CntrlTx
These are the inputs (receive) and output (transmit) of the system

![[Pasted image 20261103202203.png]]

This is the closed loop you see between the vehicle subsystem and the controller sub system

The input the controllers sub system is from the vehicle and the controller outputs back to the vehicle

CntrlTx
- This carries the commands from all seven different controllers and they are grouped as VCU, Engine, Trans, Brake, Diff, BMS, and Thermal

Something to note here the accelerator control path. the VCU reads `AccelFdbk` from `CntrlRx` so the pedal input most likely passes through the vehicle first
- This could mean that we replace the pedal value only on the copy of CntrlRx going into `StockController` 

#### Finding out what signals are passed from block block
- Click a signal line
- Click the signal tab in the too strip
- Click signal hierarchy
![[Pasted image 20261103221130.png]]

- You can click something like `AccelFdbk` and then click Source or Destination to see where that signal ends up or where it originated from

#### Observing values of signals
- Click the signal line you're interested
- Go to signal tab
- Click output value label
- Click the yellow box that appears on the signal line
- Tick the signals you are interested in observing
![[Pasted image 20261103221328.png]]

### Understanding the Changes
What we did was set up the interface layer so that we can split the torque between the front motor and the rear motor given the input from how much the accelerator pedal was pressed. 

The vehicle block will send a bus called CntrlRx which holds a bunch of information the controller can use but the one we are most concerned with is `AccelFdbk` which is a value between 0 and 1 which indicates **how much the accelerator was pressed down**. 

Inside our modified controller (**we created a modified controller so that we can compare the results of our modified controller with the baseline controller**) we built 3 subsystems that handle the torque split.

#### MappingtoTeamSignals
We needed this subsystem so that we change where signals come from without having to touch the actual controller (**TeamAddedController**).

We want to make sure that the controller always receives the same input. Right now we are dealing with MIL (**Model in the Loop**) so how we get `AccelPedal` is from `CntrlRx > Bus Selector > AccelPedal`

Eventually we will have HIL/VIL (Hardware in the Loop and Vehicle in the Loop). In this case we will most likely get a voltage input. So now instead of going into the controller and creating a subsystem for MIL, HIL and VIL and having to switch between them we can simply just add the logic in the `MappingtoTeamSignals` subsystem to make the change. You will also have other team mappings in here if needed

The purpose of this block is to do the following:
- Rename signals
- Convert units
- Standardize signs
- Group signals
- Add validity flags

Remember that the current controller is configured for a model in the loop. We will have to change and rename everything once we get access to the CAN database of the car. 
#### TeamAddedController
The is the meaty part of what we added. This is the controller that will handle the torque split once the `AccelPedal` signal is sent. Here we will create some kind of formula that splits how much torque we send to the front wheels and the rear wheels. Keep in mind that we don't directly control the torque of the front. The stock controller takes a value for "**how much the accelerator is pressed**" and converts that into a torque. What we want to do is take the entire `AccelFdbk` which is how much the accelerator is pushed then turn that into a lower percentage which becomes the new `AccelFdbk`. We then also send the direct torque command to the rear motor because we have access to directly control that. 
#### MappingtoPlantModel
This subsystem will then take the new `AccelFdbk` which is now represented as `FrontPedalCmd` we created and map it back to the bus using the **Bus Assignment** and then send the entire bus back to the stock controller