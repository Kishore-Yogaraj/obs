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