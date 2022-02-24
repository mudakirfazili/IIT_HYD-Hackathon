# Low-Power Schmitt Trigger Based 10T SRAM Cell
This repository contains the development and analysis of 10T SRAM using Synopsys Custom Compiler tool. The 10T SRAM cell is designed on 28nm VLSI fabrication technology. This project was developed during the IIT Hyderabad Cloud Based Analog IC Design Hackathon.

# Table of Contents
- [Introduction](#introduction)
  - [Problem Statement](#problem-statement)
- [Basics of SRAM](#basics-of-sram)
- [Tools Used](#tools-used)
- [SRAM Cell Design](#sram-cell-design)
- [SRAM Cell Analysis](#sram-cell-analysis)
  - [HSNM](#hsnm)
  - [RSNM](#rsnm)
  - [WSNM](#wsnm)
  - [BLM](#blm)
  - [WLM](#wlm)
  - [Access Time](#access-time)
  - [Area Estimates](#area-estimates)
- [Comparative Analysis](#comparative-analysis)
- [References](#references)

# Introduction

SRAM's are the type of random access memories. They store information in the form of static latch (cross-coupled inverters). They dont require periodic refresh and have short access delays and low power consumption. Due to these properties they are a better contedor for cache memories inside the processors and microcontrollers. They are usually present in various system in sizes ranging from several KBs to a few MBs'.

## Problem Statement

Development and analysis of a 10T SRAM cell on 28nm fabrication technology using Synopsys Custom Compiler.
1. Plot the various signal to noise margins.
2. Determine the access time.
3. Calculate the area estimates.

# Basics of SRAM

A schmitt trigger (ST) based 10T SRAM is designed in this work. Let us first understand how a [6T SRAM](images/6t.png) cell works. It consists of two cross-coupled inverters (MNL, MPL and MNR, MPR) and two access transistors at its complementary nodes Q and QB. The two bit lines (BL and BLB) are connected through access transistors. The access transistors are controlled by wordline (WL). Wordline is set 1 or 0 according to the operation needed. 1 for write operation and 0 for read as can be better understood by the following table.

| Control Signal  | Operation | Operation | Operation | Operation |
| --- | --- | --- | --- | --- |
|     | Write '0' | Write '1' | Read | Hold |
| BL  | 0 | 1 | Pre | 1|
| BLB | 1 | 0 | Pre | 1|
| WL  | 1 | 1 | 1   | 0|

![6t](images/6t.png)

The [10T SRAM](images/10t.png) circuit designed in this project is similar in operation to the 6T SRAM cell except crital parameters like static noise margins (SNM) are improved. While an compromise is made with the area requirements however significant improvement on SNM is observed. To improve the inverter characteristics, Schmitt trigger configuration is used. A Schmitt trigger increases or decreases the switching threshold of an inverter depending on the direction of the input transition. This adaptation is achieved with the help of a feedback mechanism.
![10t](images/10t.png)

# Tools Used
Synopsys custom compiler was provided over remote desktop connection to the participants of this hackathon. Also, a 32nm PDK was provied that included the model files of the respective design elements like NMOS, PMOS, BJT etc. The testbenches are simulated using Primewave and the waveforms are shown on Waveview applications.

# SRAM Cell Design

The [schematic](images/10ts.png) of the basic cell is designed using 10 transistors (8 NMOS and 2 PMOS). Ports are created for inputs, outputs and power supply. Finally a symbol is created from the schematic. This symbol is then further used to first analyze the cell metrics then it can be used to scale the design to realize n-bit SRAM.
![10ts](images/10ts.png)

The netlist of the 10T SRAM cell is:
```
.subckt sram BL BLB Q QB Vdd WL gnd
*.PININFO BL:B BLB:B Q:O QB:O Vdd:I WL:I gnd:I
MM7 BLB WL QB gnd n105 w=0.1u l=0.03u nf=1 m=1
MM6 Vdd QB net5 gnd n105 w=0.1u l=0.03u nf=1 m=1
MM5 BL WL Q gnd n105 w=0.1u l=0.03u nf=1 m=1
MM4 Vdd Q net15 gnd n105 w=0.1u l=0.03u nf=1 m=1
MM3 Q QB net15 gnd n105 w=0.1u l=0.03u nf=1 m=1
MM2 net15 QB gnd gnd n105 w=0.1u l=0.03u nf=1 m=1
MM1 net5 Q gnd gnd n105 w=0.1u l=0.03u nf=1 m=1
MM0 QB Q net5 gnd n105 w=0.1u l=0.03u nf=1 m=1
MM9 QB Q Vdd Vdd p105 w=0.1u l=0.03u nf=1 m=1
MM8 Q QB Vdd Vdd p105 w=0.1u l=0.03u nf=1 m=1
.ends sram
```

The above circuit is simulated and the [waveform](images/wave_write.png) clearly depicts the successful write 0 and write 1 operation. The [circuit setup](images/ckt.png) is done by connecting the corresponding inputs, outputs and power supply as mentioned in the following table.

| Parameter | Value |
| --- | --- |
| Voltage Supply (Vdc)  | 0.6v  |
| Rise Time | 75ps  |
| Bit-line Period (BL and BLB)  | 1.2us |
| Word-line Period (WL) | 700ps |

![wave_write](images/ckt.png)
![wave_write](images/write01.png)

# SRAM Cell Analysis
In this project the following analysis are conducted on the 10T SRAM cell:

## HSNM

The hold static noise margin is derived by latching the WL to logic 0 and BL & BLB to logic 1 as show in following [circuit](images/hsnmckt.png). Then the dc sweep analysis is done to the voltage at the pin Q and the corresponding QB voltage is recorded. Then a QB(v) vs Q(v) graph is plotted by importing the simulation data in MS excel. The corresponding graph results in the hold SNM butterfly curve. The largest square that can fit inside the openings of the curve signifies the maximum amount of noise the 10T SRAM can withstand while in hold condition. We have estimated HSNM to be **0.26v** for the designed circuit. The corresponding waveforms of Q and QB in waveview can be seen [here](images/hsnmwv.png).

![HSNMCKT](images/hsnmckt.png)
![HSNM](images/hsnm.png)

## RSNM

The read static noise margin is derived by latching the WL to logic 1 and BL & BLB to logic 1 as show in following [circuit](images/rsnmckt.png). Then the dc sweep analysis is done to the voltage at the pin Q and the corresponding QB voltage is recorded. Then a QB(v) vs Q(v) graph is plotted by importing the simulation data in MS excel. The corresponding graph results in the read SNM butterfly curve. The largest square that can fit inside the openings of the curve signifies the maximum amount of noise the 10T SRAM can withstand while in read state. We have estimated RSNM to be **0.08v** for the designed circuit.The corresponding waveforms of Q and QB in waveview can be seen [here](images/rsnmwv.png).
![RSNMCKT](images/rsnmckt.png)
![RSNM](images/RSNM.png)

## WSNM

The write static noise margin is derived by latching the WL & BLB to logic 1 and BL to logic 0 as show in following [circuit](images/wnmckt.png). Then the dc sweep analysis is done to the voltage at the pin QB and the corresponding QB voltage is recorded. Then a QB(v) vs Q(v) graph is plotted by importing the simulation data in MS excel. Then the plotted WSNM is plotted on the same graph of the RSNM and the  largest square that can fit inside the openings of the curve signifies the measure of the ability of the cell to pull down a 1 storing node to a voltage less than switching threshold voltage. We have estimated WSNM to be **0.26v** for the designed circuit.The corresponding waveforms of Q  in waveview can be seen [here](images/wsnm_cc.png).

![WSNMCKT](images/wnmckt.png)
![WSNM](images/wsnm.png)

## BLM

![BLMCKT](images/blmckt.png)
![BLM](images/blm.png)

## WLM

## Access Time

## Area Estimates

# Comparative Analysis

# References
1.  J. P. Kulkarni, K. Kim and K. Roy, "A 160 mV Robust Schmitt Trigger Based Subthreshold SRAM," in IEEE Journal of Solid-State Circuits, vol. 42, no. 10, pp. 2303-2313, Oct. 2007, doi: 10.1109/JSSC.2007.897148.
2.  Ahmad, Sayeed, Naushad Alam, and Mohd Hasan. "Pseudo differential multi-cell upset immune robust SRAM cell for ultra-low power applications." AEU-International Journal of Electronics and Communications 83 (2018): 366-375, doi: 10.1016/j.aeue.2017.09.022.
