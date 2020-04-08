---
layout:     post
title:      FPGA Development with wujian100 SoC
subtitle:    "\"Part Five: GPIO (LED ON and OFF)\""
date:       2020-04-06
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
    - GPIO
---

## FPGA Development with wujian100 SoC

### Part Five: GPIO (LED ON and OFF)

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

### Part 5 GPIO

#### 1 What is GPIO

> A general-purpose input/output (GPIO) is an uncommitted digital signal pin on an integrated circuit or electronic circuit board whose behavior—including whether it acts as input or output—is controllable by the user at run time.
*wikipedia*

To be brief, GPIO is what we use to write or read digital High/Low signal to and from.

#### 2 Let us see what we have got in library

Open `drv_gpio.h`, as we could see in the following external functions that we could call. They are expained quite elaborate by t-head Inc..

```C++
/**
  \brief       Initialize GPIO handle.
  \param[in]   gpio_pin    gpio pin idx.
  \param[in]   cb_event  event callback function \ref gpio_event_cb_t
  \return      gpio_pin_handle
*/
gpio_pin_handle_t csi_gpio_pin_initialize(int32_t gpio_pin, gpio_event_cb_t cb_event);

/**
  \brief       De-initialize GPIO pin handle.stops operation and releases the software resources used by the handle.
  \param[in]   handle    gpio pin handle to operate.
  \return      error code
*/
int32_t csi_gpio_pin_uninitialize(gpio_pin_handle_t handle);

/**
  \brief       control gpio power.
  \param[in]   handle  gpio handle to operate.
  \param[in]   state   power state.\ref csi_power_stat_e.
  \return      error code
*/
int32_t csi_gpio_power_control(gpio_pin_handle_t handle, csi_power_stat_e state);

/**
  \brief       config pin mode
  \param[in]   pin       gpio pin handle to operate.
  \param[in]   mode      \ref gpio_mode_e
  \return      error code
*/
int32_t csi_gpio_pin_config_mode(gpio_pin_handle_t handle,
                                 gpio_mode_e mode);

/**
  \brief       config pin direction
  \param[in]   pin       gpio pin handle to operate.
  \param[in]   dir       \ref gpio_direction_e
  \return      error code
*/
int32_t csi_gpio_pin_config_direction(gpio_pin_handle_t handle,
                                      gpio_direction_e dir);

/**
  \brief       config pin
  \param[in]   pin       gpio pin handle to operate.
  \param[in]   mode      \ref gpio_mode_e
  \param[in]   dir       \ref gpio_direction_e
  \return      error code
*/
int32_t csi_gpio_pin_config(gpio_pin_handle_t handle,
                            gpio_mode_e mode,
                            gpio_direction_e dir);

/**
  \brief       Set one or zero to the selected GPIO pin.
  \param[in]   pin       gpio pin handle to operate.
  \param[in]   value     value to be set
  \return      error code
*/
int32_t csi_gpio_pin_write(gpio_pin_handle_t handle, bool value);

/**
  \brief       Get the value of  selected GPIO pin.
  \param[in]   pin       gpio pin handle to operate.
  \param[out]  value     buffer to store the pin value
  \return      error code
*/
int32_t csi_gpio_pin_read(gpio_pin_handle_t handle, bool *value);

/**
  \brief       set GPIO interrupt mode.
  \param[in]   pin       gpio pin handle to operate.
  \param[in]   mode      irq mode to be set
  \param[in]   enable    enable flag
  \return      error code
*/
int32_t csi_gpio_pin_set_irq(gpio_pin_handle_t handle, gpio_irq_mode_e mode, bool enable);
```

#### 3 How to use these functions

Now we have reached to the high points of this article, the use of GPIO in wujian100 SoC.

1. Unitialize the GPIO in case pre-configuration has been done unpurposely.

    ```C++
    csi_gpio_pin_uninitialize(gpio_pin_handle_t pin);
    ```

2. Initialize the GPIO function by port directly since wujian100 official offers only one single port.

    ```C++
    pin = csi_gpio_pin_initialize(gpio_pin, gpio_interrupt_handler);
    ```

3. Set GPIO pin mode. There are 5 modes for us to choose.

    Three for input: `GPIO_MODE_PULLNONE`,`GPIO_MODE_PULLUP` and `GPIO_MODE_PULLDOWN`

    Two for output: `GPIO_MODE_OPEN_DRAIN` and `GPIO_MODE_PUSH_PULL`

    For more knowledge about GPIO mode, please refer to [embeddedartistry](https://embeddedartistry.com/blog/2018/06/04/demystifying-microcontroller-gpio-settings/) by *PHILLIP JOHNSTON*.

    In this case we set mode as push pull used for output.

    ```C++
    csi_gpio_pin_config_mode(pin, GPIO_MODE_PUSH_PULL);
    ```

4. Set the GPIO direction as Output/Input whatever you would like it to be. In this case, well, we set it as Output since I have nothing to input.

    ```C++
    csi_gpio_pin_config_direction(pin, GPIO_DIRECTION_OUTPUT);
    ```

5. After configuration, we might need to output value to the port.

    ```C++
    csi_gpio_pin_write(pin, 1); //write pin with high voltage level
    csi_gpio_pin_write(pin, 0); //write pin with low voltage level
    ```

6. If you would like to read from a pin. Be sure to prepare a bool buffer, i.e. `bool val;` and read from pin using

    ```C++
    csi_gpio_pin_read(pin, &val);
    ```
