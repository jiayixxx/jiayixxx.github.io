---
layout:     post
title:      FPGA Development with wujian100 SoC
subtitle:    "\"Part Six: UART\""
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

### Part Six: UART

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

### Part 6 UART

#### 1 What is UART

> A universal asynchronous receiver-transmitter (UART /ˈjuːɑːrt/) is a computer hardware device for asynchronous serial communication in which the data format and transmission speeds are configurable. The electric signaling levels and methods are handled by a driver circuit external to the UART.
*wikipedia*

UART stands for universal asynchronous receiver-transmitter, however, wujian100 supports synchronous receiver and transmitter. It is more accurate to call it universal synchronous or asynchronous receiver-transmitter(USART). But we will follow UART since synchronous mode is quite useless unless you might want some high speed message or in other cases, we would prefer to use iic instead UART.

* For more UART knowledge, please refer to [wikipedia](https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter)

#### 2 Let us see what we have got in library

Open `.\csi_driver\wujian100_open\include\drv_usi_usart.h`, as we could see in the following external functions that we could call. They are expained quite elaborate by t-head Inc..

```C++
/**
  \brief       Initialize USART Interface. 1. Initializes the resources needed for the USART interface 2.registers event callback function
  \param[in]   idx usart index
  \param[in]   cb_event  event call back function \ref usart_event_cb_t
  \return      return usart handle if success
*/
usart_handle_t drv_usi_usart_initialize(int32_t idx, usart_event_cb_t cb_event);

/**
  \brief       De-initialize USART Interface. stops operation and releases the software resources used by the interface
  \param[in]   handle  usart handle to operate.
  \return      error code
*/
int32_t drv_usi_usart_uninitialize(usart_handle_t handle);
/**
  \brief       Get driver capabilities.
  \param[in]   idx usart index
  \return      \ref usart_capabilities_t
*/
usart_capabilities_t drv_usi_usart_get_capabilities(int32_t idx);

/**
  \brief       config usart mode.
  \param[in]   handle  usart handle to operate.
  \param[in]   baud      baud rate.
  \param[in]   mode      \ref usart_mode_e .
  \param[in]   parity    \ref usart_parity_e .
  \param[in]   stopbits  \ref usart_stop_bits_e .
  \param[in]   bits      \ref usart_data_bits_e .
  \return      error code
*/
int32_t drv_usi_usart_config(usart_handle_t handle,
                             uint32_t baud,
                             usart_mode_e mode,
                             usart_parity_e parity,
                             usart_stop_bits_e stopbits,
                             usart_data_bits_e bits);


/**
  \brief       Start sending data to USART transmitter,(received data is ignored).
               This function is non-blocking,\ref usart_event_e is signaled when operation completes or error happens.
               \ref drv_usi_usart_get_status can get operation status.
  \param[in]   handle  usart handle to operate.
  \param[in]   data  Pointer to buffer with data to send to USART transmitter. data_type is : uint8_t for 5..8 data bits, uint16_t for 9 data bits
  \param[in]   num   Number of data items to send
  \return      error code
*/
int32_t drv_usi_usart_send(usart_handle_t handle, const void *data, uint32_t num);

/**
  \brief       Abort Send data to USART transmitter
  \param[in]   handle  usart handle to operate.
  \return      error code
*/
int32_t drv_usi_usart_abort_send(usart_handle_t handle);

/**
  \brief       Start receiving data from USART receiver. \n
               This function is non-blocking,\ref usart_event_e is signaled when operation completes or error happens.
               \ref drv_usi_usart_get_status can get operation status.
  \param[in]   handle  usart handle to operate.
  \param[out]  data  Pointer to buffer for data to receive from USART receiver.data_type is : uint8_t for 5..8 data bits, uint16_t for 9 data bits
  \param[in]   num   Number of data items to receive
  \return      error code
*/
int32_t drv_usi_usart_receive(usart_handle_t handle, void *data, uint32_t num);

/**
  \brief       query data from UART receiver FIFO.
  \param[in]   handle  usart handle to operate.
  \param[out]  data  Pointer to buffer for data to receive from UART receiver
  \param[in]   num   Number of data items to receive
  \return      fifo data num to receive
*/
int32_t drv_usi_usart_receive_query(usart_handle_t handle, void *data, uint32_t num);

/**
  \brief       Abort Receive data from USART receiver
  \param[in]   handle  usart handle to operate.
  \return      error code
*/
int32_t drv_usi_usart_abort_receive(usart_handle_t handle);

/**
  \brief       Start synchronously sends data to the USART transmitter and receives data from the USART receiver. used in synchronous mode
               This function is non-blocking,\ref usart_event_e is signaled when operation completes or error happens.
               \ref drv_usi_usart_get_status can get operation status.
  \param[in]   handle  usart handle to operate.
  \param[in]   data_out  Pointer to buffer with data to send to USART transmitter.data_type is : uint8_t for 5..8 data bits, uint16_t for 9 data bits
  \param[out]  data_in   Pointer to buffer for data to receive from USART receiver.data_type is : uint8_t for 5..8 data bits, uint16_t for 9 data bits
  \param[in]   num       Number of data items to transfer
  \return      error code
*/
int32_t drv_usi_usart_transfer(usart_handle_t handle, const void *data_out, void *data_in, uint32_t num);

/**
  \brief       abort sending/receiving data to/from USART transmitter/receiver.
  \param[in]   handle  usart handle to operate.
  \return      error code
*/
int32_t drv_usi_usart_abort_transfer(usart_handle_t handle);

/**
  \brief       Get USART status.
  \param[in]   handle  usart handle to operate.
  \return      USART status \ref usart_status_t
*/
usart_status_t drv_usi_usart_get_status(usart_handle_t handle);

/**
  \brief       flush receive/send data.
  \param[in]   handle usart handle to operate.
  \param[in]   type \ref usart_flush_type_e .
  \return      error code
*/
int32_t drv_usi_usart_flush(usart_handle_t handle, usart_flush_type_e type);

/**
  \brief       set interrupt mode.
  \param[in]   handle usart handle to operate.
  \param[in]   type \ref usart_intr_type_e.
  \param[in]   flag 0-OFF, 1-ON.
  \return      error code
*/
int32_t drv_usi_usart_set_interrupt(usart_handle_t handle, usart_intr_type_e type, int32_t flag);

/**
  \brief       set the baud rate of usart.
  \param[in]   baud  usart base to operate.
  \param[in]   baudrate baud rate
  \return      error code
*/
int32_t drv_usi_usart_config_baudrate(usart_handle_t handle, uint32_t baud);

/**
  \brief       config usart mode.
  \param[in]   handle  usart handle to operate.
  \param[in]   mode    \ref usart_mode_e
  \return      error code
*/
int32_t drv_usi_usart_config_mode(usart_handle_t handle, usart_mode_e mode);

/**
  \brief       config usart parity.
  \param[in]   handle  usart handle to operate.
  \param[in]   parity    \ref usart_parity_e
  \return      error code
*/
int32_t drv_usi_usart_config_parity(usart_handle_t handle, usart_parity_e parity);

/**
  \brief       config usart stop bit number.
  \param[in]   handle  usart handle to operate.
  \param[in]   stopbits  \ref usart_stop_bits_e
  \return      error code
*/
int32_t drv_usi_usart_config_stopbits(usart_handle_t handle, usart_stop_bits_e stopbit);

/**
  \brief       config usart data length.
  \param[in]   handle  usart handle to operate.
  \param[in]   databits      \ref usart_data_bits_e
  \return      error code
*/
int32_t drv_usi_usart_config_databits(usart_handle_t handle, usart_data_bits_e databits);

/**
  \brief       get character in query mode.
  \param[in]   handle  usart handle to operate.
  \param[out]  ch the pointer to the received character.
  \return      error code
*/
int32_t drv_usi_usart_getchar(usart_handle_t handle, uint8_t *ch);

/**
  \brief       transmit character in query mode.
  \param[in]   handle  usart handle to operate.
  \param[in]   ch  the input character
  \return      error code
*/
int32_t drv_usi_usart_putchar(usart_handle_t handle, uint8_t ch);

/**
  \brief       Get usart send data count.
  \param[in]   handle  usart handle to operate.
  \return      number of currently transmitted data bytes
*/
uint32_t drv_usi_usart_get_tx_count(usart_handle_t handle);

/**
  \brief       Get usart received data count.
  \param[in]   handle  usart handle to operate.
  \return      number of currently received data bytes
*/
uint32_t drv_usi_usart_get_rx_count(usart_handle_t handle);

/**
  \brief       control usart power.
  \param[in]   handle  usart handle to operate.
  \param[in]   state   power state.\ref drv_usi_power_stat_e.
  \return      error code
*/
int32_t drv_usi_usart_power_control(usart_handle_t handle, csi_power_stat_e state);

/**
  \brief       config usart flow control type.
  \param[in]   handle  usart handle to operate.
  \param[in]   flowctrl_type   flow control type.\ref usart_flowctrl_type_e.
  \return      error code
*/
int32_t drv_usi_usart_config_flowctrl(usart_handle_t handle,
                                      usart_flowctrl_type_e flowctrl_type);

/**
  \brief       config usart clock Polarity and Phase.
  \param[in]   handle  usart handle to operate.
  \param[in]   cpol    Clock Polarity.\ref usart_cpol_e.
  \param[in]   cpha    Clock Phase.\ref usart_cpha_e.
  \return      error code
*/
int32_t drv_usi_usart_config_clock(usart_handle_t handle, usart_cpol_e cpol, usart_cpha_e cpha);

/**
  \brief       control the transmitter.
  \param[in]   handle  usart handle to operate.
  \param[in]   enable  1 - enable the transmitter. 0 - disable the transmitter
  \return      error code
*/
int32_t drv_usi_usart_control_tx(usart_handle_t handle, uint32_t enable);

/**
  \brief       control the receiver.
  \param[in]   handle  usart handle to operate.
  \param[in]   enable  1 - enable the receiver. 0 - disable the receiver
  \return      error code
*/
int32_t drv_usi_usart_control_rx(usart_handle_t handle, uint32_t enable);

/**
  \brief       control the break.
  \param[in]   handle  usart handle to operate.
  \param[in]   enable  1- Enable continuous Break transmission,0 - disable continuous Break transmission
  \return      error code
*/
int32_t drv_usi_usart_control_break(usart_handle_t handle, uint32_t enable);
```

#### 3 How to use these functions

Now we have reached to the high points of this article, the use of UART in wujian100 SoC.

1. Unitialize the UART in case pre-configuration has been done unpurposely.

    ```C++
    csi_usart_uninitialize(usart);
    ```

2. Initialize usart handler and make sure to create a global handler.

    ```C++
     usart = csi_usart_initialize(uart_idx, (usart_event_cb_t)usart_event_cb);
     g_usart_handle = usart;
    ```

3. Configure usart, in this case usart has been configured as asychronous mode without parity flag, with 115200 baud rate, one stop bits and eight data bits.

    ```C++
    ret = csi_usart_config(usart, 115200, USART_MODE_ASYNCHRONOUS, USART_PARITY_NONE, USART_STOP_BITS_1, USART_DATA_BITS_8);
    ```

4. Then you have successfully configured USART. But before use, you need to create a call back function appeared in step 2 `usart_event_cb` which deals with interrupt callback code, i.e. However, it does not matter if you make it an empty function if you would like to process this callback event in your main function.

    ```C++
    /******************************************************************************
    * @brief      UART interrupt event call back function prototype for wujian100
    * @param[in]  idx     usart port to operate.
    * @param[in]  event   event type.
    * @author     Jiayi
    * @return     None
    ******************************************************************************/
    static void usart_event_cb(int32_t idx, uint32_t event)
    {
        uint8_t g_data[15];

        switch (event) {
            case USART_EVENT_SEND_COMPLETE:
                tx_async_flag = 1;
                break;

            case USART_EVENT_RECEIVE_COMPLETE:
                rx_async_flag = 1;
                break;

            case USART_EVENT_RECEIVED:
                csi_usart_receive_query(g_usart_handle, g_data, 15);

            default:
                break;
        }
    }
    ```

5. How to use UART to output or input? Just use `printf` function and `fgetc` function since `board_init` file has helped us with the function remapping. For more details please refer to [Part4_Hello_World](https://shieldjy.github.io/2020/03/31/FPGA-Development-with-WJ100-SoC/).

6. I would come up with more about USART usage in the future.
