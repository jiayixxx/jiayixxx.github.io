---
layout:     post
title:      FPGA Development with wujian100 SoC
subtitle:    "\"Part EIGHT: Interrupt(VIC)\""
date:       2020-04-13
author:     Jiayi
header-img: img/20200326/zhixintongde.jpg
catalog: true
tags:
    - FPGA
    - wujian100
    - 无剑100
    - 平头哥
    - t-head
    - C-Sky Devolopment Kit(CDK)
    - Interrupt
    - VIC(Vector Interrupt Controller)
---

## FPGA Development with wujian100 SoC

### Part EIGHT: Interrupt (VIC)

Author: 加一(Jiayi)

## Something to say

Recently I participate a contest named Integrate Circuit Innovation Contest which requires me to use WJ100 developed by Ali Inc. team t-head and a FPGA develop board with Xlinx XC7A200TR3B Core. It's not my first time to cope with FPGA but still, I find it difficult to interpret Verilog Code and make the FPGA works. Luckily, with the help of WJ100 Sdk and CDK(C-sky Develop Kit) which developed by Ali Inc. we could jump the Verilog and long waiting synthesizing part directly to use the pre-setted circuit and easy writing C to develop.

---

## About WJ100

>T-Head's Wujian SoC Platform utilizes the cloud-terminal-integration design philosophy that fuses software and hardware. Full stack integration of chips, operating systems and algorithms enables customers to develop chip products that can be mass-production.
>
>Low power consumption: User-defined power consumption scenarios, with standby power consumption of less than 1uA, and operating power consumption of less than 100uA/MHz

According to the official sites of t-head Inc., WJ100 is a low cost and high power efficiency SoC, which barely means that it could be easily deployed on any chips and consumes lower power.

However, as I talked before, it is a open source project and as I believed, the real function of this SoC is to simplify the use of FPGA and to offer the developer a brand new way to develop: integrate Soc and FPGA to deal with some projects which require both power efficiency and fast steady frequency.

* related websites
[t-head](https://www.t-head.cn/)

### How to use WJ100 SoC

This tutorial is for those who utilize vivado to generate bitstream file and CDK to develop your own projects on *Windows*.

* For Part 1 Bitsream Generation please refer to [Part_1_Bitstream_Generation](https://shieldjy.github.io/2020/03/25/FPGA-Development-with-WJ100-SoC/).

* For Part 2 CDK Toolkit and wujian100 SDK please refer to [Part_2_CDK_Toolkit&Wujian100_SDK](https://shieldjy.github.io/2020/03/27/FPGA-Development-with-WJ100-SoC/).

* For Part 3 Start a New Project on CDK please refer to [Part_3_Start_a_New_Project_on_CDK](https://shieldjy.github.io/2020/03/29/FPGA-Development-with-WJ100-SoC/)

* For Part 4 Hello World please refer to [Part4_Hello_World](https://shieldjy.github.io/2020/03/31/FPGA-Development-with-WJ100-SoC/)

* For Part 5 GPIO please refer to [Part5_GPIO](https://shieldjy.github.io/2020/04/06/FPGA-Development-with-WJ100-SoC/)

* For Part 6 UART please refer to [Part6_UART](https://shieldjy.github.io/2020/04/08/FPGA-Development-with-WJ100-SoC/)

* For Part 7 TIMER please refer to [Part7_TIMER](https://shieldjy.github.io/2020/04/08/FPGA-Development-with-WJ100-SoC/)

### Part 7 TIMER

#### 1 What is Interrupt

>In digital computers, an interrupt is an input signal to the processor indicating an event that needs immediate attention. An interrupt signal alerts the processor and serves as a request for the processor to interrupt the currently executing code, so that the event can be processed in a timely manner. If the request is accepted, the processor responds by suspending its current activities, saving its state, and executing a function called an interrupt handler (or an interrupt service routine, ISR) to deal with the event. This interruption is temporary, and, unless the interrupt indicates a fatal error, the processor resumes normal activities after the interrupt handler finishes. *wikipedia*

For more information about interrupt, please refer to [wikipedia](https://en.wikipedia.org/wiki/Interrupt)

#### 2 What is VIC

VIC stands for vector interrupt controller, which is actually a pointer points to the interrupt vector table that associates a list of interrupt handlers with a list of interrupt requests in a table of interrupt vectors. Thus this controller controls the sequece of interrupt to make sure the program runs well.

#### 3 How does interrupt work

Normally, interrupt is triggered by interrupt handler. Interrupt handler could categrized by several methods, i.e. internally or externally, and level triggerd or edge triggered for external trigger. External trigger is always related to GPIO or UART, and Internal trigger is always related with timer, watch dog(used to prevent some error), etc.

The interrupt handler will send a request to VIC in CPU, and CPU will arrange the request according to interrupt vector table (IVT). Once the interrupt has been operated by CPU, the normal programme routine is hanged and the interrupt callback function which pre-stored in the IVT will come to work.
