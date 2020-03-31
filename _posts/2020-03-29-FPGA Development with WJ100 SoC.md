---
layout:     post
title:      FPGA Development with wujian100 SoC
subtitle:    "\"Part Three: Start a New Project on CDK\""
date:       2020-03-29
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
---

## FPGA Development with wujian100 SoC

### Part Three: Start a New Project on CDK

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

This tutorial is for those who utilize vivado to generate bitstream file and CDK to develop your own projects with *Windows*.

* For Part 1 Bitsream Generation please refer to [Part_1_Bitstream_Generation](https://shieldjy.github.io/2020/03/25/FPGA-Development-with-WJ100-SoC/).

* For Part 2 CDK Toolkit and wujian100 SDK please refer to [Part_2_CDK_Toolkit&Wujian100_SDK](https://shieldjy.github.io/2020/03/27/FPGA-Development-with-WJ100-SoC/).

### Part 3 Start a New Project on CDK

1. Open your CDK desktop.

2. Before create a new projeck, we need to create a new workspace for it. Click `create a new multi-project workspace` and choose your name and path. Finish the create of project.
    ![step2](https://s1.ax1x.com/2020/03/31/GK9EsP.png)

3. After we have successfully created the workspace, we could now create a new project. Find `Project` on your toolbar and choose `new project` and there will be a *new project* window.

4. Since CDK doesn't offer an template for wujian100, we must choose a core similar to wujian100 SoC. So let us choose `Smartl_E902-BareMetal` under `ERISC Series`. However, as I believe, whatever you choose are both alright because we are going to delete all the files and change almost every configuration later.
    ![step4](https://s1.ax1x.com/2020/03/31/GK9iRA.png)

5. Remove every folder in the project you just created. Both in the workspace and in the folder. Be careful because you need to restore `main` folder and `{yourprojectname}.cdkprj`
    ![step5](https://s1.ax1x.com/2020/03/31/GK9AMt.png)

6. Go to `wujian100_open` repo path and open `sdk`, copy following folders to ./{yourcdkprojectname}: `board`, `csi_core`, `csi_driver`, `csi_kernel`, `libs`, `utilities`.
    ![step6.1](https://s1.ax1x.com/2020/03/31/GK9PGd.png)
    Open `projects-examples-hello_world` and copy `config` folder to ./{yourcdkprojectname}, just like:
    ![step6.2](https://s1.ax1x.com/2020/03/31/GK9FxI.png)

7. Right click `{yourprojectname}` in the left side bar or wherever called `project view` window and choose `add source folder` option.
    ![step7](https://s1.ax1x.com/2020/03/31/GK9eZ8.png)

8. Add folders just moved to your project folder *one by one* and click `NO` for "copy source folder to current project path".

9. Finally you should see the project view like this with 8 folders and all its contents are inside.
    ![step9](https://s1.ax1x.com/2020/03/31/GK9mdS.png)

10. Open `project` menu on the toolbar and select `open active project settings`.
    ![step10](https://s1.ax1x.com/2020/03/31/GK9nIg.png)

11. In `target` label under `target info` we could write our description about wujian100, i.e.

    ```text
    Core: wujian100 SoC
    Board: Xilinx XC7A200TR3B
    ```

12. In `Compiler` label we need to modify include path, delete several include path related to the original template. The include path should be like the following.

    ```path
    $(ProjectPath)
    $(ProjectPath)//configs
    $(ProjectPath)/board/wujian100_open_evb/include
    $(ProjectPath)/csi_core/include
    $(ProjectPath)/csi_driver/include
    $(ProjectPath)/csi_driver/wujian100_open/include
    $(ProjectPath)/csi_kernel/include
    $(ProjectPath)/csi_kernel/rhino/arch/include
    $(ProjectPath)/csi_kernel/rhino/common
    $(ProjectPath)/csi_kernel/rhino/core/include
    $(ProjectPath)/csi_kernel/rhino/driver
    $(ProjectPath)/csi_kernel/rhino/pwrmgmt
    $(ProjectPath)/include
    $(ProjectPath)/libs/include
    $(ProjectPath)/libs/include/ringbuffer
    $(ProjectPath)/libs/include/sys
    ```

13. In `Assembler` label we need also change the include path to be like following.

    ```path
    $(ProjectPath)
    $(ProjectPath)//configs
    ```

14. In `Linker` label under `link file` dock, we `must` change that into our gcc_csky.ld file.

    ```path
    $(ProjectPath)/board/wujian100_open_evb/gcc_csky.ld
    ```

15. In `User` laber under `Run user programm AFTER build/rebuild` dock, we need to change that into our after_bd.sh

    ```path
    $(ProjectPath)/utilities/aft_build.sh
    ```

16. Click `OK` to end the configuration.

17. Build the project and finally we could see the `Done` message.
    ![step17](https://s1.ax1x.com/2020/03/31/GK9KiQ.png)
