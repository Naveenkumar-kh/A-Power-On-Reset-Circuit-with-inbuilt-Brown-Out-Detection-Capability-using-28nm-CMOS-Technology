# A-Power-On-Reset-Circuit-with-inbuilt-Brown-Out-Detection-Capability-using-28nm-CMOS-Technology
Project done in cloud based Analog IC Design Hackathon using Synopsys custom design platform

## Table of Contents

- [Introduction](https://github.com/Naveenkumar-kh/A-Power-On-Reset-Circuit-with-inbuilt-Brown-Out-Detection-Capability-using-CMOS/blob/main/README.md#introduction)
- [Timing diagram of POR and BOR signal](https://github.com/Naveenkumar-kh/A-Power-On-Reset-Circuit-with-inbuilt-Brown-Out-Detection-Capability-using-CMOS/edit/main/README.md#timing-diagram-of-por-and-bor-signal)
- [Device Parameters Extraction](https://github.com/Naveenkumar-kh/A-Power-On-Reset-Circuit-with-inbuilt-Brown-Out-Detection-Capability-using-CMOS/edit/main/README.md#device-parameters-extraction)
- [Circuit Design](https://github.com/Naveenkumar-kh/A-Power-On-Reset-Circuit-with-inbuilt-Brown-Out-Detection-Capability-using-CMOS/edit/main/README.md#device-parameters-extraction)
- [Simulation Output](https://github.com/Naveenkumar-kh/A-Power-On-Reset-Circuit-with-inbuilt-Brown-Out-Detection-Capability-using-CMOS/edit/main/README.md#simulation-output)
- [Author](https://github.com/Naveenkumar-kh/A-Power-On-Reset-Circuit-with-inbuilt-Brown-Out-Detection-Capability-using-CMOS/edit/main/README.md#author)
- [Acknowledgements](https://github.com/Naveenkumar-kh/A-Power-On-Reset-Circuit-with-inbuilt-Brown-Out-Detection-Capability-using-CMOS/edit/main/README.md#acknowledgements)
- [References](https://github.com/Naveenkumar-kh/A-Power-On-Reset-Circuit-with-inbuilt-Brown-Out-Detection-Capability-using-CMOS/edit/main/README.md#references)

## Introduction

After power-up, any electronic system needs certain amount of time to settle in a steady state supply voltage. During this initial period of transition, the system blocks should not start their functioning. This may cause unintended outputs or glitches in the system. So, a power-on reset circuit is needed, which resets the system during this initial state whenever a system powers up for the first time. This reset signal during the power-up sequence prevents any glitches or false outputs. If the system suddenly draws high current, there may be a momentary drop in the supply voltage. This drop in supply is not an acceptable behavior for any circuit and the system may require a reset to recover from it's effects. A brown-out detection circuit generates the required reset signal when such events occur in the system. This design implements both power-on reset and brown-out reset functions in a single CMOS circuit.

<p align="center">
<img src="https://user-images.githubusercontent.com/59077160/156010051-233cb14a-0633-4666-9d93-5c246ef6a79e.jpg">
</p>
<p align="center">
Fig 1. Circuit diagram of POR and BOR circuit
</p>

## Timing diagram of POR and BOR signal

The POR signal should rise to peak along with the supply voltage as the source ramps up and stay high for a preset value of time. When the supply voltage drops below a preset value of voltage BOR signal should be generated and is should stay for a preset amount of time in order to stabilize/recover the system from the power-on/brown-out events respectively. 

<p align="center">
<img src="https://user-images.githubusercontent.com/59077160/156047687-7b7d32fb-a69a-4de2-9b2c-d7d704e6fc44.jpg">
</p>
<p align="center">
Fig 2. Timing diagram of POR and BOR signal
</p>

## Device Parameters Extraction

For getting the device internal parameters µn, Cox and approximate Vth of the 28nm NMOS and PMOS devices, a dummmy NMOS and PMOS circuit with 3.5µm (maximum possible in the SAED_PDK32nm Synopsys library) L and W is constructed. The MOS devices are then injected with constant current sources and the internal parameters are calculated from the DC parameters obtained by doing DC analysis of the circuits in the Primewave environment. Here, maximum device sizes are taken to eliminate the effect of channel length modulation on the extracted device parameters.

## Circuit Design

### Power-On Reset Circuit

The power-on reset circuit consists of current generator, current mirror and a delay cell. Once the supply voltage reaches turn ON voltage of the current generator M26-M2, it starts generating the reference current for the cascaded current mirror stages. The three stages of current mirrors M26-M3, M28-M29, M30-31 are sized such that the last stage charges the capacitor C46 of the delay cell with nano-ampere range current. The current through M31 and the capacitor C46 decide the pulse with of the POR signal.

<p align="center">
<img src="https://user-images.githubusercontent.com/59077160/156036821-b3729fc0-4591-48e8-9cdb-22e06c08dacb.jpg">
</p>
<p align="center">
Fig 3. POR circuit
</p>

### Brown-Out Reset Circuit

The brown-out reset circuit consists of a resistor R50 and diode connected NPN BJT Q62 for sensing brown-out conditions. During normal operation if the supply voltage falls below Vlow = IR drop of R50 + Vbe of Q62, the MOSFET M47 which is normally ON in the steady state operation, turns OFF. As M47 turns off the current source M49 pulls the gate of M48 to GND thus turning it ON. The current through M48 decides how fast the charge is discharged from delay cell capacitor, which in turn decides the pulse width of BOR signal. So, M48 is sized based on the following relation. 

<p align="center">
ID(M48) = (1/2) µpCox (W/L) (Vgs(M48)−Vth)2

<p align="center">
<img src="https://user-images.githubusercontent.com/59077160/156040906-5155ceaf-d868-4a92-844d-668c0c9da53d.jpg">
</p>
<p align="center">
Fig 4. BOR circuit
</p>

### Schmitt Trigger Circuit

The generated POR and BOR pulses are given to a CMOS based Schmitt trigger which generates a clean reset pulse without any toggling conditions during transistion, as the Schmitt trigger has hysteresis property. The upper and lower set voltages of the Schmitt trigger is set in a way that the reset is generated only when the internally generated toggle pulse reaches certain high value and goes low only after reaching certain low limit. In this design these voltages are set at 0.9 and 0.6 volts respectively.

<p align="center">
<img src="https://user-images.githubusercontent.com/59077160/156045749-77a48a02-05ae-4a4c-bdd9-624ce9129e37.jpg">
</p>
<p align="center">
Fig 5. Schmitt trigger circuit
</p>

### Overall circuit

All the above designed blocks current generator, current mirror, delay cell, brown-out detector and Schmitt trigger are combined together and the required design is implemented as follow.

<p align="center">
<img src="https://user-images.githubusercontent.com/59077160/156046407-486ba96c-b5f8-438c-b0cd-9126bc176ed1.jpg">
</p>
<p align="center">
Fig 6. Implemented circuit of POR circuit with BOR detection
</p>

## Simulation Output

The simulation is done by generating a supply voltage with 1µs rise and fall times, steady state voltage of 1.8V max to check POR functionality. The supply is then given a disturbance of 0.5V for 2µs to check BOR functionality. And the simulation results match with the expected output.

<p align="center">
<img src="https://user-images.githubusercontent.com/59077160/156047290-314a0e19-7909-4d5f-950d-8532e2be0b83.jpg">
</p>
<p align="center">
Fig 7. Simulated output of the designed POR and BOR circuit
</p>

## Author

[Naveenkumar K H](www.linkedin.com/in/naveenkumar-kh), M.Tech. VLSI Design, VIT Vellore 

## Acknowledgements

- [Kunal Ghosh, Co-founder, VSD Corp. Pvt Ltd.](https://www.linkedin.com/in/kunal-ghosh-vlsisystemdesign-com-28084836)
- Chinmay Panda, IIT Hyderabad
- [Synopsis Team/Company](synopsys.com/company/contact-synopsys/office-locations/india/about-synopsys-india.html)
- [IIT Hyderabad](https://www.iith.ac.in/events/2022/02/15/Cloud-Based-Analog-IC-Design-Hackathon/)

## References

1. Le, H. B., Do, X. D., Lee, S. G., & Ryu, S. T. (2011). A long reset-time power-on reset circuit with brown-out detection capability. IEEE Transactions on Circuits and Systems II: Express Briefs, 58(11), 778-782.<br/>
2. Design of Analog CMOS Integrated Circuits (Behzad Razavi), McGraw-Hill, 2001.

