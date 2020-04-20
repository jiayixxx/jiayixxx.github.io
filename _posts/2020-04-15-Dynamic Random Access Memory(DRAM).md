---
layout:     post
title:      FPGA Development with wujian100 SoC
subtitle:    "\"Part EIGHT: Interrupt(VIC)\""
date:       2020-04-15
author:     Jiayi
header-img: img/20200326/zhixintongde.jpg
catalog: true
tags:
    - DRAM
    - Device
---

## 1. What is DRAM

Dynamic random-access memory (DRAM) is a type of random access semiconductor memory that stores each bit of data in a memory cell consisting of a tiny capacitor and a transistor, both typically based on metal-oxide-semiconductor (MOS) technology.*wikipedia*

## 2. Structure of DRAM Cells

![Fig1.DRAM_Cell_Structure](https://upload.wikimedia.org/wikipedia/commons/b/bd/DRAM_Cell_Structure_%28Model_of_Single_Circuit_Cell%29.PNG)
*Figure 2.1 DRAM Cell Structure

From figure 2.1, as we could see, DRAM is actually a 1T1C(one transistor and one capacitor) structure. Writing process is accomplished by selecting bit line 1 and word line 2 thus pull up/down the voltage of capacitor 4. Firstly, data is stored by applying high voltage to both word line 2 and bit line 1. And then data is stored by applying a low voltage to the word line 2 and trapping the charge on the storage capacitor. Subsequently, the data is accessed by applying a high voltage to the wordline to make FET work in depletion mode, conduction bitline and capacitor, so that the voltage on the bit line 1 could be sensed. Then Word line or Bit line is turned to be Low Voltage Level to config next cell so that FET works in cut off mode and thus the voltage level is stored in capacitor 4.

However, this memory cell structure is non-volatile for two reason. For one, the voltage level of capacitor is hold due to its capacity of charges, but the charges will leak as time passed by. This process is basically due to the current leakage. Study shows, the major leakage paths in a DRAM cell are reverse junction leakage from the storage node and gate induced drain leakage (GIDL) current [1]. `need to fill`

[1] u, J. and K. Aflatooni (2006). Leakage Current in DRAM Memory Cell. 2006 16th Biennial University/Government/Industry Microelectronics Symposium.
