# Phase Locked Loop(PLL)-IC-Design
This is a work done in a two day VSD -IAT Cloud based workshop where  all the steps  in the IC design flow was performed for PLL circuit using Google Skywater 130nm node and open source tools such as ngspice and magic.

## INDEX

### THEORETICAL
> - Introduction to Phase Locked Loop
> - Introduction to Phase Frequency Detector
> - Introduction to Charge Pump
> - Introduction to Voltage Controlled Oscillator and Frequency Divider
> - Tapeout
> - Tool setup and Design flow
> - Introduction to PDK, Specifications and Prelayout circuits

### PRACTICAL
> - PLL Components Circuit design
> - PLL Components Circuit Simulation
> - Steps to combine PLL Subckts and Full design Simulation
> - Layout design
> - Layout walkthrough
> - Parasitic Extraction
> - Post Layout Simulations
> - Steps to Combine Layouts
> - Tapeout Labs 

### FEW TROUBLESHOOTING TIPS

REFERENCE

ACKNOWLEDGEMENTS

### THEORETICAL

## INTRODUCTION TO PHASE LOCKED LOOP

**Phase Locked Loop(PLL)** is a feedback loop system which is used to produce the signal without any frequency or phase noise and is mostly designed for clocking applications. 

To generate clock signal, we can consider using **Quartz Crystal Oscillator(XO)** or **Voltage Controlled Oscillator(VCO)** for our application.
Oscillators are mainly used to provide stability in frequency that is under any fluctuating conditions, the output should produce constant frequency.
*Quartz Crystal* provide Superior Spectral Purity (means no unwanted frequency or fluctuation in phase) and phase noise performance but not that flexible as VCO as they produce certain frequency alone
*Voltage Controlled Oscillator* easily controlled by input voltage, good flexibility, Implemented easily with inverters but have noise and fluctuation in their phase.

The purpose of using the PLL is to mimic the spectral purity of the quartz crystal while maintaining the flexibility. 
The below Graph shows the Pure Spectrum and Noisy Spectrum.

![image](https://user-images.githubusercontent.com/44599861/133963017-9a5c1e41-3916-4b24-9351-72a53a071625.png)

**PLL COMPONENTS**

PLL comprises of Phase Frequency Detector(PFD), Charge pump(CP), Loop filter(LPF), Voltage Controlled Oscillator(VCO), and Frequency Divider(FD) blocks.

![image](https://user-images.githubusercontent.com/44599861/133963443-ab9835c1-ef6b-43be-813a-00ee11efb802.png)

Following explains about each block:
**PFD** helps in comparing output signal with the Reference signal.
**CP** converts digital comparison output of PFD in to Analog signal.
**LPF** helps in smoothening of the cp o/p signal.
**VCO** helps in adjusting frequency according to the input signal frequency
**FD** converts whole system in to frequency multiplier. 
Here it is divided by 8 that is compared with ref, so that the VCO will be adjusted such that the frequency divided signal matches the reference this in turn means the o/p will be a freq which is a multiple of a reference. In this case 8*reference signal.

**Few Important Terms**

*LOCK RANGE*
Once locked, the PLL frequency range is able to follow input frequency variations

*CAPTURE RANGE*
Starting from an unlocked condition, PLL is able to lock-in the frequency range

*SETTLING TIME*
The time within which the PLL is able to lock-in from an unlocked condition

## INTRODUCTION TO PHASE FREQUENCY DETECTOR

**Phase Frequency Detector (PFD)** where comparison of Reference Signal(REF) and Output Signal(OUT) is done.
so try to do XOR between these two signals and check will it provide any difference. Let us consider two condition for OUT signal: 1. Lagging (Fig.3)
        2. Leading (Fig.4)
        
![image](https://user-images.githubusercontent.com/44599861/133965033-3e53f013-9b33-4d1a-a51a-928f629f9d91.png)

![image](https://user-images.githubusercontent.com/44599861/133965134-bfdd9110-9d63-4cbc-8635-8d31b933dfdc.png)

It can be clearly observed that the XOR is not showing any visible difference, hence we can approach in different method.
The leading and lagging signal distinguish will help to increase or decrease in phase.

![image](https://user-images.githubusercontent.com/44599861/134042827-c96a11dd-869a-432d-b807-00458409395f.png)

Up and Down signal help us to indicate lag and lead respectively.
For down signal, the falling edge of out detected becomes active until falling edge of REF
For up signal, the falling edge of REF detected becomes active until falling edge of out

![image](https://user-images.githubusercontent.com/44599861/134042943-2195dc7a-508c-4d39-a860-e94f9321ceb8.png)

Frequency difference between the signal is captured.
If the out freq is the higher, the down signal gets activated that means we must slow down the output.
When up signal gets activated, we must speed up the output.
The same thing can be Represented using state machine:

![image](https://user-images.githubusercontent.com/44599861/133966344-1c2def4a-a7c3-4896-8d75-f4d9755109e5.png)

Fliflops are used to detect the edges.

![image](https://user-images.githubusercontent.com/44599861/133966443-57ecf071-7f2a-467a-a4df-2b4af14f40b3.png)

![image](https://user-images.githubusercontent.com/44599861/133966565-d9958428-32a4-4bea-81cb-1c88ed2b8cc1.png)

![image](https://user-images.githubusercontent.com/44599861/133966575-85589a01-240b-43b1-853b-e81582079f9c.png)

**Disadvantage**:

Dead Zone issue where PDF is insensitive to phase difference. The output appears to be clipped when signals does not have distinguishable difference 
 
## INTRODUCTION TO CHARGE PUMP

**Charge Pump(CP)** converts the ouput from PFD in to analog signal to control the oscillator. This can be achieved by using the Current Steering circuit. The capacitor charges and increase the voltage, when Up signal is active.

![image](https://user-images.githubusercontent.com/44599861/133967475-7a53f3b8-0d6f-425e-aae8-cad6b4869d01.png)
![image](https://user-images.githubusercontent.com/44599861/133967671-fd56f689-e47d-47d1-8c4a-f510068cf047.png)


when down signal is active, the output discharges and decreases the voltage.

![image](https://user-images.githubusercontent.com/44599861/133967617-8101d2ea-5f2d-4713-8c43-6f83bbe293f2.png)
![image](https://user-images.githubusercontent.com/44599861/133967680-0bf522d7-aeb1-407a-a142-229ed4fcf8f3.png)

![image](https://user-images.githubusercontent.com/44599861/134043384-81eb60fa-e163-4692-8f15-ca2f12fe331b.png)
![image](https://user-images.githubusercontent.com/44599861/134043313-1913e10f-df78-49f1-ab35-67cc7709fb1f.png)

**Disadvantage**

when up and down transistors are off, there is still a small current flowing through them in form of leakage and the capacitor keeps charging.

The Capacitor is replaced by loop filter because at times the fluctuations occur and also the averaging is not smooth as expected. Loop Filter Stabilizes the PLL and smoothens any fluctuations in the output.

![image](https://user-images.githubusercontent.com/44599861/133968324-63b4a3e8-20bc-44c2-9ddf-2d8a4c9b0045.png)

For stability, consider THUMB RULE:
- Cx~=C/10
- Loopp Filter B.W ~=(Highest Output Frequency)/10

## INTRODUCTION TO Voltage Controlled Oscillator AND FREQUENCY DIVIDER

In this design for **Volatage Controlled Oscillator (VCO)**, Most common Ring Oscillator design can be used where odd number of series combination of inverters with each has certain delay. The problem is Oscillator has fixed Frequency

![image](https://user-images.githubusercontent.com/44599861/134000609-a88371bc-3b5d-42cf-a173-2b65b01ee1a9.png)

For VCO, to control the frequency which depends on delay and delay depends on current supplied to the Ring Oscillator.
Below diagram shows that the two current sources are used as for the ring Oscillator at top and bottom.
Vctrl is the supply which controls the current, which in turn controls the frequency of the Oscillator.

![image](https://user-images.githubusercontent.com/44599861/134002602-5548aa61-ee22-4f49-86bc-dced5f64cff5.png)

**Frequency Divider (FD)** 
In toggle FlipFlop, the output is half the frequency of the input signal

![image](https://user-images.githubusercontent.com/44599861/134003185-d902cce3-2348-430c-a160-60ad5bbb9d69.png)

So for example, if you give the input of 5ns, the output will be 10ns.
In the figure above, the green signal with the output signal is input.

For 8xclock multiplier, use three of this toggle to get the frequency division by 8.

## TAPEOUT

**Tape out** is after all the design steps are implemented, the final design is send to the Fab after preparing it.
Preparing is like I/O pads, Perripherals, Memory, testing mechanisms and others.

*Caravel* is an template SOC is used here to place our IP. 

## TOOL SETUP AND DESIGN FLOW

Here we have used two open source toools called **Ngspice** and **Magic**.

- Ngspice:
  sudo apt-get install ngspice
  
  To Run:
  
  ngspice <circuit_file_name>
  
- Magic:
  - sudo apt-get update && sudo apt-get upgrade       // Update OS
  - git clone git://opencircuitdesign.com/magic       //Clone the Magic Repository
  - sudo apt-get install csh                          //Install csh shell
  - cd magic                                          //Go into the cloned directory
  - ./configure                                       //Run Configure script
  -  make                                             //Run make command to compile
  -  sude make install                                //Install Magic

To Run:

magic -T <technology_file_from_PDK><the_layout_file_to_open>

**DEVELOPMENT FLOW**

![image](https://user-images.githubusercontent.com/44599861/134010277-9e62cb70-39a3-4e1e-85df-b96cb89db846.png)

## INTRODUCTION TO PDK,SPECIFICATIONS AND PRE LAYOUT CIRCUITS

The PDK used in this work is **Open Source Google-Skywater 130nm**.
Refer this https://github.com/google/skywater-pdk

The sky-water Library is required. The library cloned and used here is sky130_fd.pr
The PDK has following contents:
- io: input-output
- pr: primitives(spice)
- sc: standard cell
- hd: high density
- hs: high speed
- lp: low power
- hdll: high density low leakage

**PLL SPECIFICATIONS**

Bellow are the Operating conditions at which the PLL works.

1.Corner - 'TT'     
  >> TT is Typical Typical, which represent the outcome of the doping process

2.Supply Voltage - 1.8v

3.Room Temperature

4.VCO mode and PLL mode

5.Input Fmin =5Mhz; Fmax = 12.5Mhz

6.Multiplier -8x

7.Jitter(RMS)<~20ns

8.Duty Cycle - 50%

*1, 2 and 3 are the PVT(Process, Voltage, Temperature) Corner conditions required here.*

**PRELAYOUT**

This is the development stage at which the transistor level simulation of the circuit is carried out

**PRE-LAYOUT: FD**

![image](https://user-images.githubusercontent.com/44599861/134014182-8a0a6f0c-18a9-4306-aee3-306e52d19c78.png)

**PRE-LAYOUT: PFD**

![image](https://user-images.githubusercontent.com/44599861/134014453-762f4c75-85d7-4a9a-81a2-05609182106e.png)

**PRE-LAYOUT: CP**

![image](https://user-images.githubusercontent.com/44599861/134014581-a4397f8f-6d79-4883-bc5d-df290df15bb9.png)

**PRE-LAYOUT: VCO**

![image](https://user-images.githubusercontent.com/44599861/134014814-05e4d001-7c69-40e5-a434-2e1dd91a7e2b.png)

### PRACTICAL

## PLL COMPONENTS CIRCUIT DESIGN

![image](https://user-images.githubusercontent.com/44599861/135150125-eaca5d5a-9c5c-4a83-b014-002b26965f07.png)

touch FD.cir creates an empty file.

Open FD.cir file and type your instructions

![image](https://user-images.githubusercontent.com/44599861/135150194-6ab82622-10ef-4ea4-9c24-94f3e361c693.png)

## PLL COMPONENETS CIRCUIT SIMULATIONS

Simulation of the code is carried out in ngspice and look in to ouput obtained for all the blocks in PLL.

**FREQUENCY DIVIDER**

![image](https://user-images.githubusercontent.com/44599861/135150584-5312619b-d283-438c-987d-302b178417b8.png)

![image](https://user-images.githubusercontent.com/44599861/135150783-c5fdd791-e88a-450e-9474-33fbc4dba218.png)

The transient analysis plot is seen and the tran instruction provides the amount of time in the plot. The output is half the frequency of the input, hence the FD is working.

**CHARGE PUMP**

*With no input given to up and down signal*:

![image](https://user-images.githubusercontent.com/44599861/134018295-c5395af2-cbc9-4158-b952-eada6ee5c757.png)

ic is the initial condition of a signal.

![image](https://user-images.githubusercontent.com/44599861/135151130-3dd0482a-fbea-4b20-8205-f4e365f8abfe.png)

The slope of the signal is very small

*with PULSE given as input to up signal*:

![image](https://user-images.githubusercontent.com/44599861/134018715-556cf436-554b-4082-bd24-58935244a17f.png)

![image](https://user-images.githubusercontent.com/44599861/135151439-6b2b1926-7c6f-4366-8b28-2cf476db0ddd.png)

The expected charactristics is obtained.

**VOLTAGE CONTROLLED OSCILLATOR**

v2 is the control signal here

![image](https://user-images.githubusercontent.com/44599861/134018875-2717e9ce-f1d2-4edb-b736-acba71bdac9b.png)

![image](https://user-images.githubusercontent.com/44599861/135151641-ec2fbdc6-082a-446f-936e-71627ee52ea2.png)

![image](https://user-images.githubusercontent.com/44599861/135151688-1a546b0d-a108-4c82-8dd6-8bfb9a1f187d.png)

Full swing is obtained because an extra inverter is added at the output.

**PHASE FREQUENCY DETECTOR**

![image](https://user-images.githubusercontent.com/44599861/134019050-7bdeba2b-8e07-4e61-a5d3-043ca5b1d754.png)

![image](https://user-images.githubusercontent.com/44599861/135151926-673c070e-7a52-4a39-b979-93520f042fce.png)

![image](https://user-images.githubusercontent.com/44599861/135152209-65aa3d12-d221-4473-8109-631f0b892c25.png)

v2 and v3 are two input signals which have phase difference of 6ns. The output is able to detect the slight difference increase

## STEPS TO COMBINE PLL SUB-CIRCUITS AND PLL FULL DESIGN

All the subcircuits are combined together in to one file ans simulated

The components are instantiated. 

![image](https://user-images.githubusercontent.com/44599861/134022384-eca83de2-72b4-4344-9e04-9de7ed5dba4f.png)

![image](https://user-images.githubusercontent.com/44599861/134022429-5444321d-2798-48df-8b7c-5a7c735145f5.png)

![image](https://user-images.githubusercontent.com/44599861/135152603-4bbd7b4d-e62d-429b-9e60-7b6171403d97.png)

![image](https://user-images.githubusercontent.com/44599861/135152788-529cc203-5788-4193-bb9d-ce49dcf2db46.png)

First row has the charge pump output. Below, up and down signal is seen. Then comes the output signal and their divided signal by 2 and by 4. Last row is the Reference signal. Actually, all the eight signal are overlapping

## LAYOUT DESIGN

Using the command in the below figure, the magic tool is opened using sky130A.tech file.

![image](https://user-images.githubusercontent.com/44599861/135152918-9e0fd87c-6a87-4b56-8bbf-2c5272cad2ec.png)

![image](https://user-images.githubusercontent.com/44599861/135152957-0e060e37-c0ee-4911-b7e4-47cb4d1fae68.png)

## LAYOUT WALKTHROUGH

Each Component layout is shown below.

**FD**

![image](https://user-images.githubusercontent.com/44599861/135153346-e0c141ec-6b5c-4aac-a060-bd5c8ec44dae.png)
![image](https://user-images.githubusercontent.com/44599861/135153414-431b7fe4-0a0a-4465-81d8-109ec2e47805.png)

**PFD**
![image](https://user-images.githubusercontent.com/44599861/135153528-e8b6f936-15ea-478b-a229-90307ae361f3.png)
![image](https://user-images.githubusercontent.com/44599861/135153553-d2343144-1124-423e-96af-8eb0263cadf4.png)

**VCO**

![image](https://user-images.githubusercontent.com/44599861/135153650-3f974708-2cd9-4cc6-90fd-84136f1205df.png)
![image](https://user-images.githubusercontent.com/44599861/135153680-2c741abc-2b3e-43c7-a210-834927226c8d.png)

**CP**

![image](https://user-images.githubusercontent.com/44599861/135153815-84764fbe-65f9-4344-978c-9e822f8ec6cf.png)
![image](https://user-images.githubusercontent.com/44599861/135153842-32b61efa-a72d-4d6e-9b4b-780ef175d962.png)

## PARASITIC EXTRACTION

To extract the information about parasitic components, open the layout, select the layout by pressing i and type commands in the
tkcon window.

![image](https://user-images.githubusercontent.com/44599861/135159102-68df7e0a-3d51-45cc-8b4c-ab3a2f9c4ab9.png)

![image](https://user-images.githubusercontent.com/44599861/135159147-28751f3e-b19b-4231-871b-80b610eeb14d.png)

![image](https://user-images.githubusercontent.com/44599861/135159212-bae44d80-3cf3-49f4-a829-7e452a48548e.png)

PFD.spice will be created
## POST LAYOUT SIMULATIONS
open and type in the PFD_Postlay.cir
![image](https://user-images.githubusercontent.com/44599861/135159797-db7de911-1a82-470b-8e63-30f01129e33a.png)

![image](https://user-images.githubusercontent.com/44599861/135205184-ee35f202-6b7b-4636-9881-e74c47442ba1.png)

Press ctrl+s to save
press ctrl+x to exit
simulate using ngspice

![image](https://user-images.githubusercontent.com/44599861/135196412-88aed321-8094-455f-97fd-6f3bfd650b06.png)

![image](https://user-images.githubusercontent.com/44599861/135205211-b9b945d8-6c1b-49ce-9443-efc104265fb0.png)
![image](https://user-images.githubusercontent.com/44599861/135205235-2243108c-12f9-49f6-a79b-dfa8a96945e5.png)
![image](https://user-images.githubusercontent.com/44599861/135205250-f7450e01-9317-4dca-9405-8ba0ea24dca1.png)


## STEPS TO COMBINE LAYOUT

![image](https://user-images.githubusercontent.com/44599861/135202566-cf524df9-5c2b-4056-b0f3-1d3758c18b43.png)
![image](https://user-images.githubusercontent.com/44599861/135202600-5d898bef-7db2-48cb-bcbc-d9c0654d48c8.png)
![image](https://user-images.githubusercontent.com/44599861/135202624-59346dcf-277e-476a-9b72-515f84db3f15.png)
![image](https://user-images.githubusercontent.com/44599861/135202654-6058e6eb-5185-4aee-b86e-88023315583c.png)
![image](https://user-images.githubusercontent.com/44599861/135202689-13cecabd-ea2a-4c51-8e86-7719fa86f85e.png)
![image](https://user-images.githubusercontent.com/44599861/135202717-7da580bf-d7db-4c39-a066-1e156bed1322.png)
![image](https://user-images.githubusercontent.com/44599861/135202732-da87462b-43b9-4f1c-ac33-765543ac3fd3.png)
![image](https://user-images.githubusercontent.com/44599861/135202768-cf913f79-8d3c-4529-b716-1110041a545d.png)
![image](https://user-images.githubusercontent.com/44599861/135202793-ca666b97-a965-4825-a9ea-68171aea2302.png)
![image](https://user-images.githubusercontent.com/44599861/135202805-dfc78178-5761-4648-90cc-3a26b9cb5897.png)

## TAPEOUT LABS

![image](https://user-images.githubusercontent.com/44599861/135202980-3e0d1910-30a3-40d9-afab-2323ca547093.png)
![image](https://user-images.githubusercontent.com/44599861/135203127-92b40c59-6ce9-4c61-8945-4dc2b75a6c02.png)
![image](https://user-images.githubusercontent.com/44599861/135203194-64a29bb9-8d6b-4a1a-ad14-1350b0ed0e41.png)
![image](https://user-images.githubusercontent.com/44599861/135203270-906ab4be-2a9f-47d9-8712-ac5c7eec2559.png)
![image](https://user-images.githubusercontent.com/44599861/135203336-11d3973d-9b09-40d3-a894-e4699093c2bb.png)
![image](https://user-images.githubusercontent.com/44599861/135203456-fe0794ef-daf6-4307-900d-4a8ffa32f826.png)
![image](https://user-images.githubusercontent.com/44599861/135203509-d24e0420-41f1-4df8-86dc-a632c4e92e8d.png)
![image](https://user-images.githubusercontent.com/44599861/135203549-bf7e855a-b88f-4dac-9732-7f40786ce2c1.png)
![image](https://user-images.githubusercontent.com/44599861/135203909-0c2dc7e0-fbde-44c8-93e5-f06c3252249d.png)
![image](https://user-images.githubusercontent.com/44599861/135203961-52984420-3dfe-4b57-ac6c-044e2f0aeadd.png)

### FEW TROUBLESHOOTING TIPS

- Look in to the issue arised
- Before combining, Try simulating each component individually and compare with expected output.
   - check the frequency range the VCO is working
   - check whether the PFD is able to detect the small differences
   - For charge pump, when no signal is given but still it is charging means there is Leakage issue
- If the output is crashing or some signals are flat, look in to the net names, connectivity's or parameter values.
- if nothing is working, try adjusting the loop filter value
-
    **Simulating Issues**
- when you run ngspice and there error of File not found:
  - check the directory, where the file is stored and change the directory in the terminal or copy the file to the working directory
  - If include files in the program is not found, check whether the included file is in the same directory where the .cir file is saved
  - check the names of the included file and the saved file name

**REFERENCE**

https://github.com/lakshmi-sathi/avsdpll_1v8
https://github.com/google/skywater-pdk/tree/main/libraries
http://opencircuitdesign.com/magic/
http://ngspice.sourceforge.net/
https://github.com/efabless/caravel

**ACKNOWLEDGEMENTS**

Kunal Ghosh, co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd. 

Lakshmi Sathidevi , MS ECE 


    












































































  


