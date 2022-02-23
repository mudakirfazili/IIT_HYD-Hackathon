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

The [10T SRAM](images/10t.png) circuit designed in this project is similar in operation to the 6T SRAM cell except crital parameters like static noise margins (SNM) are improved. While an compromise is made with the area requirements however significant improvement on SNM is observed.
![10t](images/10t.png)

# Tools Used
Synopsys custom compiler was provided over remote desktop connection to the participants of this hackathon. Also, a 32nm PDK was provied that included the model files of the respective design elements like NMOS, PMOS, BJT etc. The testbenches are simulated using Primewave and the waveforms are shown on Waveview applications.

# SRAM Cell Design

The [schematic](images/10ts.png) of the basic cell is designed using 10 transistors (8 NMOS and 2 PMOS). Ports are created for inputs, outputs and power supply. Finally a symbol is created from the schematic. This symbol is then further used to first analyze the cell metrics then it can be used to scale the design to realize n-bit SRAM.
![10ts](images/10ts.png)

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

The read static noise margin is derived by latching the WL to logic 1 and BL & BLB to logic 1 as show in following [circuit](images/rsnmckt.png). Then the dc sweep analysis is done to the voltage at the pin Q and the corresponding QB voltage is recorded. Then a QB(v) vs Q(v) graph is plotted by importing the simulation data in MS excel. The corresponding graph results in the hold SNM butterfly curve. The largest square that can fit inside the openings of the curve signifies the maximum amount of noise the 10T SRAM can withstand while in read state. We have estimated RSNM to be **0.08v** for the designed circuit.The corresponding waveforms of Q and QB in waveview can be seen [here](images/rsnmwv.png).
![RSNMCKT](images/rsnmckt.png)
![RSNM](images/RSNM.png)

## WSNM

## Access Time

## Area Estimates

# Comparative Analysis

# References
1.  J. P. Kulkarni, K. Kim and K. Roy, "A 160 mV Robust Schmitt Trigger Based Subthreshold SRAM," in IEEE Journal of Solid-State Circuits, vol. 42, no. 10, pp. 2303-2313, Oct. 2007, doi: 10.1109/JSSC.2007.897148.
2.  Ahmad, Sayeed, Naushad Alam, and Mohd Hasan. "Pseudo differential multi-cell upset immune robust SRAM cell for ultra-low power applications." AEU-International Journal of Electronics and Communications 83 (2018): 366-375, doi: 10.1016/j.aeue.2017.09.022.
