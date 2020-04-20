---
layout:     post
title:      FPGA Development with wujian100 SoC
subtitle:    "\"Part SEVEN: TIMER\""
date:       2020-04-09
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
    - TIMER
---

## FPGA Development with wujian100 SoC

### Part SEVEN: TIMER

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

* For Part 6 UART please refer to [Part5_GPIO](https://shieldjy.github.io/2020/04/08/FPGA-Development-with-WJ100-SoC/)

### Part 7 TIMER

#### 1 What is Timer

Timer is a count up counter in SoC. There are normally several opration modes for timer, for example, auto-reload mode, counter mode, manual reload mode, etc. Interrupt is normally used when using auto-reload mode timer for controlling dc motor or any device requires periodical responds.

In wujian100 SoC, there are 16 channels/timers which I am not sure about since the official does not offer any docs. However, there are two modes, i.e. reload mode and freerunning mode which again i am not sure what freerunning could be used for.

#### 2 Let us see what we have got in library

Timer is quite a simple module to use in wujian100 and hence it does not offer us many advanced options. Here is library for timer which could be found in `.\csi_driver\include\drv_timer.h`.

```c
/**
  \brief       Initialize TIMER Interface. 1. Initializes the resources needed for the TIMER interface 2.registers event callback function
  \param[in]   idx  timer index
  \param[in]   cb_event  event call back function \ref timer_event_cb_t
  \param[in]   cb_arg    arguments of cb_event
  \return      pointer to timer instance
*/
timer_handle_t csi_timer_initialize(int32_t idx, timer_event_cb_t cb_event);

/**
  \brief       De-initialize TIMER Interface. stops operation and releases the software resources used by the interface
  \param[in]   handle timer handle to operate.
  \return      error code
*/
int32_t csi_timer_uninitialize(timer_handle_t handle);

/**
  \brief       control timer power.
  \param[in]   handle  timer handle to operate.
  \param[in]   state   power state.\ref csi_power_stat_e.
  \return      error code
*/
int32_t csi_timer_power_control(timer_handle_t handle, csi_power_stat_e state);

/**
  \brief       config timer mode.
  \param[in]   handle timer handle to operate.
  \param[in]   mode      \ref timer_mode_e
  \return      error code
*/
int32_t csi_timer_config(timer_handle_t handle, timer_mode_e mode);

/**
  \brief       Set timeout just for the reload mode.
  \param[in]   handle timer handle to operate.
  \param[in]   timeout the timeout value in microseconds(us).
  \return      error code
*/
int32_t csi_timer_set_timeout(timer_handle_t handle, uint32_t timeout);

/**
  \brief       Start timer.
  \param[in]   handle timer handle to operate.
  \return      error code
*/
int32_t csi_timer_start(timer_handle_t handle);

/**
  \brief       Stop timer.
  \param[in]   handle timer handle to operate.
  \return      error code
*/
int32_t csi_timer_stop(timer_handle_t handle);

/**
  \brief       suspend timer.
  \param[in]   handle timer handle to operate.
  \return      error code
*/
int32_t csi_timer_suspend(timer_handle_t handle);

/**
  \brief       resume timer.
  \param[in]   handle timer handle to operate.
  \return      error code
*/
int32_t csi_timer_resume(timer_handle_t handle);

/**
  \brief       get timer current value
  \param[in]   handle timer handle to operate.
  \param[out]   value     timer current value
  \return      error code
*/
int32_t csi_timer_get_current_value(timer_handle_t handle, uint32_t *value);

/**
  \brief       Get TIMER status.
  \param[in]   handle timer handle to operate.
  \return      TIMER status \ref timer_status_t
*/
timer_status_t csi_timer_get_status(timer_handle_t handle);

/**
  \brief       get timer reload value
  \param[in]   handle timer handle to operate.
  \param[out]   value    timer reload value
  \return      error code
*/
int32_t csi_timer_get_load_value(timer_handle_t handle, uint32_t *value);
```

* Be sure not to use function `csi_timer_suspend` since I found it actually an empty function and could only return error code.

```c++
/**
  \brief       suspend timer.
  \param[in]   instance  timer instance to operate.
  \return      error code
*/
int32_t csi_timer_suspend(timer_handle_t handle)
{
    TIMER_NULL_PARAM_CHK(handle);

    return ERR_TIMER(DRV_ERROR_UNSUPPORTED);
}
```

#### 3 How to use these functions

Now we have reached to the high points of this article, the use of Timer in wujian100 SoC.

1. Initialize timer handler. timer_num is the timer you would like to use, i.e. `0` for timer0, `1` for timer1, etc. And just like what we did in UART part, we had better write a callback function to deal with timer interrupt event call back.

    ```c++
    timer_handle = csi_timer_initialize(timer_num, timer_event_cb_fun);
    ```

2. configure timer as anyway you would like it to be.

    ```c++
    csi_timer_config(timer_handle, TIMER_MODE_RELOAD);
    ```

    or for freerunning mode

    ```c++
    csi_timer_config(timer_handle, TIMER_MODE_FREE_RUNNING);
    ```

3. set timeout `only in reload mode`, timeout is in microseconds, i.e. us

    ```c++
    csi_timer_set_timeout(timer_handle, timeout_us);
    ```

4. start timer and say BINGO **(must*)** !

    ```c++
    csi_timer_start(timer_handle);
    ```

5. Remember to put something in callback function. Otherwise you would see nothing is happening. And more intriging thing in CDK is that stop points does not work in call back function which make it difficult to debug interrupt which I will write about in next article.

* Note* It does not really matter if you say bingo or not.
