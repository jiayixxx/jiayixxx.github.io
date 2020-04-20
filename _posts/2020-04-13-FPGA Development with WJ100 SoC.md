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

Normally, interrupt is triggered by interrupt handler. Interrupt handler could been categrized by several methods, i.e. internally or externally, level triggerd or edge triggered for external trigger. External trigger is always related to GPIO or UART, and Internal trigger is always related with timer, watch dog(used to prevent some error), etc.

The interrupt handler will send a request to VIC in CPU, and CPU will arrange the request according to interrupt vector table (IVT). Once the interrupt has been operated by CPU, the normal programme routine (what are in `main` function) is hanged and the interrupt callback function which pre-stored in the IVT will come to work.

![pic](www..com)

#### 4 What do we get in wujian100

```c
    User_Software_IRQn              =   0,      /* User software interrupt */
    Supervisor_Software_IRQn        =   1,      /* Supervisor software interrupt */
    Machine_Software_IRQn           =   3,      /* Machine software interrupt */
    User_Timer_IRQn                 =   4,      /* User timer interrupt */
    Supervisor_Timer_IRQn           =   5,      /* Supervisor timer interrupt */
    CORET_IRQn                      =   7,      /* core Timer Interrupt */
    GPIO0_IRQn                      =   16,     /* uart Interrupt */
    TIM0_IRQn                       =   17,     /* timer0 Interrupt */
    TIM1_IRQn                       =   18,     /* timer1 Interrupt */
    TIM2_IRQn                       =   19,     /* timer2 Interrupt */
    TIM3_IRQn                       =   20,     /* timer3 Interrupt */
    TIM4_IRQn                       =   21,     /* timer4 Interrupt */
    TIM5_IRQn                       =   22,     /* timer5 Interrupt */
    TIM6_IRQn                       =   23,     /* timer6 Interrupt */
    TIM7_IRQn                       =   24,     /* timer7 Interrupt */
    PWM_IRQn                        =   25,     /* pwm Interrupt */
    RTC_IRQn                        =   26,     /* rtc Interrupt */
    WDT_IRQn                        =   27,     /* wdt Interrupt */
    USI0_IRQn                       =   28,     /* usi0 Interrupt */
    USI1_IRQn                       =   29,     /* usi1 Interrupt */
    USI2_IRQn                       =   30,     /* usi2 Interrupt */
    PMU_IRQn                        =   31,     /* pmu Interrupt */
    DMAC0_IRQn                      =   32,     /* dmac0 Interrupt */
    TIM8_IRQn                       =   33,     /* timer8 Interrupt */
    TIM9_IRQn                       =   34,     /* timer9 Interrupt */
    TIM10_IRQn                       =  35,     /* timer10 Interrupt */
    TIM11_IRQn                       =  36,     /* timer11 Interrupt */
    TIM12_IRQn                       =  37,     /* timer12 Interrupt */
    TIM13_IRQn                       =  38,     /* timer13 Interrupt */
    TIM14_IRQn                       =  39,     /* timer14 Interrupt */
    TIM15_IRQn                       =  40,     /* timer15 Interrupt */
```

According to enumerate type IRQn in soc.h, there are basically 10 types of interrupt, i.e., software interrupt, timer, gpio, pwm(palse width modulation), rtc(real time clock), coret(core timer), wdt(watchdog timer), usi(universal serial interface), dmac(direct memory access), pmu(power management unit).

To dig more, we could find functions that set VIC in `core_rv32.h`.

```c
/**
/**
  \ingroup  CSI_Core_FunctionInterface
  \defgroup CSI_Core_VICFunctions VIC Functions
  \brief    Functions that manage interrupts and exceptions via the VIC.
  @{
 */

/* The following MACROS handle generation of the register offset and byte masks */
#define _BIT_SHIFT(IRQn)         (  ((((uint32_t)(int32_t)(IRQn))         )      &  0x03UL) * 8UL)
#define _IP_IDX(IRQn)            (   (((uint32_t)(int32_t)(IRQn))                >>    5UL)      )
#define _IP2_IDX(IRQn)            (   (((uint32_t)(int32_t)(IRQn))                >>    2UL)      )

/**
  \brief   Enable External Interrupt
  \details Enable a device-specific interrupt in the VIC interrupt controller.
  \param [in]      IRQn  External interrupt number. Value cannot be negative.
 */
__STATIC_INLINE void csi_vic_enable_irq(int32_t IRQn)
{
    CLIC->INTIE[IRQn] |= CLIC_INTIE_IE_Msk;
}

/**
  \brief   Disable External Interrupt
  \details Disable a device-specific interrupt in the VIC interrupt controller.
  \param [in]      IRQn  External interrupt number. Value cannot be negative.
 */
__STATIC_INLINE void csi_vic_disable_irq(int32_t IRQn)
{
    CLIC->INTIE[IRQn] &= ~CLIC_INTIE_IE_Msk;
}

/**
  \brief   Enable External Secure Interrupt
  \details Enable a secure device-specific interrupt in the VIC interrupt controller.
  \param [in]      IRQn  External interrupt number. Value cannot be negative.
 */
__STATIC_INLINE void csi_vic_enable_sirq(int32_t IRQn)
{
    CLIC->INTIE[IRQn] |= (CLIC_INTIE_IE_Msk | CLIC_INTIE_T_Msk);
}

/**
  \brief   Disable External Secure Interrupt
  \details Disable a secure device-specific interrupt in the VIC interrupt controller.
  \param [in]      IRQn  External interrupt number. Value cannot be negative.
 */
__STATIC_INLINE void csi_vic_disable_sirq(int32_t IRQn)
{
    CLIC->INTIE[IRQn] &= ~(CLIC_INTIE_IE_Msk | CLIC_INTIE_T_Msk);
}

/**
  \brief   Check Interrupt is Enabled or not
  \details Read the enabled register in the VIC and returns the pending bit for the specified interrupt.
  \param [in]      IRQn  Interrupt number.
  \return             0  Interrupt status is not enabled.
  \return             1  Interrupt status is enabled.
 */
__STATIC_INLINE uint32_t csi_vic_get_enabled_irq(int32_t IRQn)
{
    return (uint32_t)(CLIC->INTIE[IRQn] & CLIC_INTIE_IE_Msk);
}

/**
  \brief   Check Interrupt is Pending or not
  \details Read the pending register in the VIC and returns the pending bit for the specified interrupt.
  \param [in]      IRQn  Interrupt number.
  \return             0  Interrupt status is not pending.
  \return             1  Interrupt status is pending.
 */
__STATIC_INLINE uint32_t csi_vic_get_pending_irq(int32_t IRQn)
{
    return (uint32_t)(CLIC->INTIP[IRQn] & CLIC_INTIP_IP_Msk);
}

/**
  \brief   Set Pending Interrupt
  \details Set the pending bit of an external interrupt.
  \param [in]      IRQn  Interrupt number. Value cannot be negative.
 */
__STATIC_INLINE void csi_vic_set_pending_irq(int32_t IRQn)
{
    CLIC->INTIP[IRQn] |= CLIC_INTIP_IP_Msk;
}

/**
  \brief   Clear Pending Interrupt
  \details Clear the pending bit of an external interrupt.
  \param [in]      IRQn  External interrupt number. Value cannot be negative.
 */
__STATIC_INLINE void csi_vic_clear_pending_irq(int32_t IRQn)
{
    CLIC->INTIP[IRQn] &= ~CLIC_INTIP_IP_Msk;
}

/**
  \brief   Set Interrupt Priority
  \details Set the priority of an interrupt.
  \note    The priority cannot be set for every core interrupt.
  \param [in]      IRQn  Interrupt number.
  \param [in]  priority  Priority to set.
 */
__STATIC_INLINE void csi_vic_set_prio(int32_t IRQn, uint32_t priority)
{
    CLIC->INTCFG[IRQn] = (CLIC->INTCFG[IRQn] & (~CLIC_INTCFG_PRIO_Msk)) | (priority << CLIC_INTCFG_PRIO_Pos);
}

/**
  \brief   Get Interrupt Priority
  \details Read the priority of an interrupt.
           The interrupt number can be positive to specify an external (device specific) interrupt,
           or negative to specify an internal (core) interrupt.
  \param [in]   IRQn  Interrupt number.
  \return             Interrupt Priority.
                      Value is aligned automatically to the implemented priority bits of the microcontroller.
 */
__STATIC_INLINE uint32_t csi_vic_get_prio(int32_t IRQn)
{
    return CLIC->INTCFG[IRQn] >> CLIC_INTCFG_PRIO_Pos;
}

/**
  \brief   Set interrupt handler
  \details Set the interrupt handler according to the interrupt num, the handler will be filled in irq vectors.
  \param [in]      IRQn  Interrupt number.
  \param [in]   handler  Interrupt handler.
 */
__STATIC_INLINE void csi_vic_set_vector(int32_t IRQn, uint32_t handler)
{
    if (IRQn >= 0 && IRQn < 1024) {
        uint32_t *vectors = (uint32_t *)__get_MTVT();
        vectors[32 + IRQn] = handler;
    }
}

/**
  \brief   Get interrupt handler
  \details Get the address of interrupt handler function.
  \param [in]      IRQn  Interrupt number.
 */
__STATIC_INLINE uint32_t csi_vic_get_vector(int32_t IRQn)
{
    if (IRQn >= 0 && IRQn < 1024) {
        uint32_t *vectors = (uint32_t *)__get_MTVT();
        return (uint32_t)vectors[32 + IRQn];
    }

    return 0;
}
```

Fortunately, most of them are functions called by the system. Hence there are quite few functions we might use.

For example, we might only use `csi_vic_set_prio` in the interrupt configuration part.

```c
/**
  \brief   Set Interrupt Priority
  \details Set the priority of an interrupt.
  \note    The priority cannot be set for every core interrupt.
  \param [in]      IRQn  Interrupt number.
  \param [in]  priority  Priority to set.
 */
__STATIC_INLINE void csi_vic_set_prio(int32_t IRQn, uint32_t priority)
{
    CLIC->INTCFG[IRQn] = (CLIC->INTCFG[IRQn] & (~CLIC_INTCFG_PRIO_Msk)) | (priority << CLIC_INTCFG_PRIO_Pos);
}
```

### **However, IRQ interrupt priority is `strongly advised` to prevent some sequence error, since t-head Inc. does not offer us official paper about default IRQ sequence.**

#### 5 How to use VIC

Luckily, although VIC is complicated inside the SoC, the usage is quite simple. All we need to do is simply add a line to set priority after configuring the interrupt, take timer as example.

```c
/*initialize timer handler*/
timer_handle = csi_timer_initialize(timer_num, timer_event_cb_fun);
/*config timer*/
ret = csi_timer_config(timer_handle, TIMER_MODE_RELOAD);
/*config priority*/
csi_vic_set_prio(TIMER0_IRQn, LO_PRIO);
/*set timer timeout*/
csi_timer_set_timeout(timer_handle, 1000 * timeout_ms);
```

**Note** the first param in csi_vic_set_prio must be one of the enumerate types in IRQn_type above and should be the interrupt that you would like to configure.
