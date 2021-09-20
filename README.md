# Phase Locked Loop(PLL)-IC-Design
This is a work done in a two day VSD -IAT Cloud based workshop where  all the steps  in the IC design flow was performed for PLL circuit using Google Skywater 130nm node and open source tools such as ngspice and magic.

## INDEX

### THEORETICAL
> - Introduction to Phase Locked Loop
> - Introduction to Phase Frequency Detector
> - Introduction to Charge Pump
> - Introduction to VCO and Frequency Divider
> - Tapeout
> - Tool setup and Design flow
> - Introduction to PDK, Specifications and Prelayout circuits
> - Circuit design simulation tool -Ngspice setup
> - Layout design tool - Magic setup

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

## INTRODUCTION TO PHASE LOCKED LOOP

**Phase Locked Loop(PLL)** is a feedback loop system which is used to produce the signal without any frequency or phase noise and is mostly designed for clocking applications. 

To generate clock signal, we can consider using **Quartz Crystal Oscillator(XO)** or **Voltage Controlled Oscillator(VCO)** for our application.
Oscillators are mainly used to provide stability in frequency that is under any fluctuating conditions, the output should produce constant frequency.
*Quartz Crystal* provide Superior Spectral Purity (means no unwanted frequency or fluctuation in phase) and phase noise performance but not that flexible as VCO as they produce certain frequency alone
*Voltage Controlled Oscillator* easily controlled by input voltage, good flexibility, Implemented easily with inverters but have noise and fluctuation in their phase.

The purpose of using the PLL is to mimic the spectral purity of the quartz crystal while maintaining the flexibility. 
The below Graph(Fig.1) shows the Pure Spectrum and Noisy Spectrum.

![image](https://user-images.githubusercontent.com/44599861/133963017-9a5c1e41-3916-4b24-9351-72a53a071625.png)

**PLL COMPONENTS**

PLL comprises of Phase Frequency Detector(PFD), Charge pump(CP), Loop filter(LPF), Voltage Controlled Oscillator(VCO), and Frequency Divider(FD) blocks (Fig.2)

![image](https://user-images.githubusercontent.com/44599861/133963443-ab9835c1-ef6b-43be-813a-00ee11efb802.png)

Following explains about each block:
**PFD** helps in comparing output signal with the Reference signal.
**CP** converts digital comparison output of PFD in to Analog signal.
**LPF** helps in smoothening of the cp o/p signal.
**VCO** helps in adjusting frequency according to the input signal frequency
**FD** converts whole system in to frequency multiplier. 
Here it is divided by 8 that is compared with ref, so that the VCO will be adjusted such that the frequency divided signal matches the reference this in turn means the o/p will be a freq which is a multiple of a reference. In this case 8*reference signal.

## INTRODUCTION TO PHASE FREQUENCY DETECTOR

Phase Frequency Detector (PFD) where comparison of Reference Signal(REF) and Output Signal(OUT) is done.
so try to do XOR between these two signals and check will it provide any difference. Let us consider two condition for OUT signal: 1. Lagging (Fig.3)
        2. Leading (Fig.4)
        
![image](https://user-images.githubusercontent.com/44599861/133965033-3e53f013-9b33-4d1a-a51a-928f629f9d91.png)

![image](https://user-images.githubusercontent.com/44599861/133965134-bfdd9110-9d63-4cbc-8635-8d31b933dfdc.png)

It can be clearly observed that the XOR is not showing any visible difference, hence we can approach in different method.
The leading and lagging signal distinguish will help to increase or decrease in phase.

![image](https://user-images.githubusercontent.com/44599861/133965521-4fbea6f1-6550-419c-babc-2049b46b9961.png)

Up and Down signal help us to indicate lag and lead respectively.
For down signal, the falling edge of out detected becomes active until falling edge of REF
For up signal, the falling edge of REF detected becomes active until falling edge of out

![image](https://user-images.githubusercontent.com/44599861/133966031-0365ff09-65a7-4597-b86b-b8013fc78e83.png)

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

Charge Pump(CP) converts the ouput from PFD in to analog signal to control the oscillator. This can be achieved by using the Current Steering circuit. The capacitor charges and increase the voltage, when Up signal is active.

![image](https://user-images.githubusercontent.com/44599861/133967475-7a53f3b8-0d6f-425e-aae8-cad6b4869d01.png)
![image](https://user-images.githubusercontent.com/44599861/133967671-fd56f689-e47d-47d1-8c4a-f510068cf047.png)


when down signal is active, the output discharges and decreases the voltage.

![image](https://user-images.githubusercontent.com/44599861/133967617-8101d2ea-5f2d-4713-8c43-6f83bbe293f2.png)
![image](https://user-images.githubusercontent.com/44599861/133967680-0bf522d7-aeb1-407a-a142-229ed4fcf8f3.png)

![image](https://user-images.githubusercontent.com/44599861/133967731-3acc67ef-b3dd-4cc9-9ca4-c2c7a3bd67d7.png)

**Disadvantage**
when up and down transistors are off, there is still a small current flowing through them in form of leakage and the capacitor keeps charging.

The Capacitor is replaced by loop filter because at times the fluctuations occur and also the averaging is not smooth as expected. Loop Filter Stabilizes the PLL and smoothens any fluctuations in the output.

![image](https://user-images.githubusercontent.com/44599861/133968324-63b4a3e8-20bc-44c2-9ddf-2d8a4c9b0045.png)

For stability, consider THUMB RULE:
- Cx~=C/10
- Loopp Filter B.W ~=(Highest Output Frequency)/10

## INTRODUCTION TO VCO AND FREQUENCY DIVIDER



![image](https://user-images.githubusercontent.com/44599861/133968681-2a00374f-887c-4b36-8ab8-b378b4b32fc9.png)























  


