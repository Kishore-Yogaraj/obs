### What's Actually Happening
We are studying how well a hot airflow loses heat toa cold pipe wall. Here's the physical set up:
- A blow pushes air through a heat which heats the are to about 450K
- That hot air travels through a 4.88 m long copper pipe
- Cold water flows over the outside of the pipe, keeping the walls cool
- We measure how fast the air cools down as it travels through the pipe

**Big Question:** Does the heat transfer rate we measure match what engineering textbook correlations predict?

### The Two Goals
**Goal 1 - Measure heat transfer:** You'll measure air velocity and temperature at different points along the pipe, then calculate how much heat was transferred and compare it to a standard engineering formula

**Goal 2 - Understand thermocouple errors:** Thermocouples don't always read the true gas temperature. Radiation from the hot surroundings and heat conduced along the wires can bias the readings. We will quantify how large these errors actually are

### What We Actually Do in the Lab

**Part 1: Pitot Tube (Velocity Measurements)**
Goal here is to measure the velocity profile of air at the pipe exit (station 3)
- Insert pitot tube at the pipe wall (r* = 1.0", velocity = 0 due to no-slip)
- Move it inward step by step - 11 positions in total, from , from r* = 0.99" down to r* = 0" (the centerline)
- At each position we will save the pressure data to a file
- We will later convert this to velocity using:
![[Pasted image 20260305004025.png]]

**Part 2: Thermocouple (Temperature Measurements)**
We will be measuring the temperature profiles at the three stations simultaneously:
- **Station 1 (Near the pipe inlet):** There are three thermocouples at this station. T1a (ling), T1b (short), T1c (at the wall)
- **Station 2 (middle)** one thermocouple
- **Station 3 (pipe exit)** one thermocouple

We traverse all of them together radially, again at the same 11 position  and saving the temperature data at each location


### Calculations
#### Heat transfer analysis
- Average the saved data at each radial position
- Plot velocity and temperature profiles that should look like turbulent pipe flow profiles (relatively flat in the middle, shar drop near the wall)
- Numerically integrate across the pipe cross section to get the mass flow rate and mean temperatures at each station
- Calculate the actual heat transfer rate q using the energy balance in equation 1
- Predict q using the a textbook heat transfer correlation (equation 2) and compare if they degree

#### Thermocouple error analysis
- **Long thermocouple (T1a):** The wires are long enough that fin conduction effects are negligible. Just do a radiation energy balance on the junction to find the true gas temperature and the error delta T
- **Short thermocouple (T1b):** Conduction along the wires does matter. Model the wires as fins and solve for the true gas temperature
- **Radiation shield:** Estimate how much a cylindrical shield around T1a would reduce the radiation error


### Questions
**How many divisions for each measurement**
- 25 divisions per full rotation
1: 10 divisions - 0.4
2: 20 divisions - 0.8
3: 20 divisions - 0.8
4: 40 divisions - 1.6
5: 70 divisions - 2.8
6: 90 divisions - 3.6 
7: 100 divisions - 4.0 
8: 120 divisions - 4.8 
9: 150 divisions - 7.0 - 
10: 180 divisions - 7.2
11: 200 divisions - 8.0