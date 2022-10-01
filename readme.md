#   解决simulink仿真模型部署到嵌入式系统上的问题


-   本文档记录了simulink生成C语言代码部署到STM32系列单片机中的详细过程

-   环境说明：   
    MDK5.32  
    matlab_2022b  
    stm32cubemx-6.4  
- 官方对软件支持的说明：  
    simulink  
    embedded coder     
    matlab coder   
    simulink coder  
    matlab从R2014a到最新的R2022b兼容
-   平台支持WINDOWS,暂不支持macos 、linux
-   stm32的开发目前只支持 stm32cubemx+hal库的模式，暂时不支持标准库

## 以下是详细说明（搬运自官方文档）
- Embedded Coder® Support Package for STMicroelectronics STM32 Processors enables users to build, load, and run Simulink models on STM32 devices using two separate workflows included in this support package. Any STM32F4xx, STM32F7xx, STM32G4xx and single core STM32H7xx family processor based boards are supported using STM32CubeMX-generated peripheral configurations and few ST Discovery boards are supported using built-in peripheral configurations. 
Capabilities of both workflows include: 
Rapid prototyping of algorithms on-chip using automated build, deploy, and execution
Perform real-time parameter tuning and logging using external mode
Perform processor-in-the-loop (PIL) with execution profiling
Generate processor optimized code, including CMSIS-DSP
Driver block libraries for on-chip and on-board peripherals such as ADC, digital I/O, PWM, SPI, I2C, and more (varies by support package and workflow)
STM32CubeMX workflow specific capabilities include: 
Support for peripheral configuration using STM32CubeMX integration
Suitable for generation of production code used in high volume deployment
Includes peripheral driver blocks for digital I/O, ADC, PWM, and IRQ
Speed motor control development with example models for Motor Control Blockset™
Supported Hardware: any STM32F4xx, STM32F7xx, STM32G4xx and single core STM32H7xx based board (Nucleo, Discovery, or custom)
Discovery board specific capabilities include: 
Peripheral driver blocks for digital I/O, ADC, PWM, IRQ, I2C, SCI, and SPI
Board-specific audio, MODBUS®, WiFi, ThingSpeak™, and environmental sensors blocks
Supported Hardware: STM32F746G-Discovery, STM32F769I-Discovery, STM32L475VG-Discovery (B-L475E-IOT01A), STM32F4-Discovery
For detailed information, view the online documentation and examples. View videos on how to install the package and how to deploy your first model. 
This support package is functional for R2013b and beyond. STM32CubeMX workflow was added in 2021b. See release notes for details. 
Note: This support package was formerly known as Embedded Coder Support for STMicroelectronics Discovery Boards till 2021b. 
For download or installation issues, please contact MathWorks Technical Support. 
For easy-to-use support of STM32 Nucleo boards, consider the Simulink Coder Support Package for STMicroelectronics Nucleo Boards.

- 中文：针对STM32处理器的嵌入式代码生成支持包使用户能够此支持包中的两个单独的工作流在STM32设备上构建、加载、运行simulink模型，
                                                                        1）使用stm32cubemx生成的外围配置支持任何基于STM32F4XX\STM32F7XX\STM32G4XX\STM32H7XX(只支持单核)
                                                                        2)  使用内置外围配置支持少数ST Discovery板
- 这两个单独的工作流包括：
    1) 使用自动构建、部署、和快速原型化芯片上算法
    2)  使用外部模式执行实时参数调整和日志记录
    3)  使用执行分析执行处理器在环（PIL）
    4)  生成处理器优化代码，包括CMSIS-DSP
    5)  用于片上和板上外围设备（如ADC\IO\PWM\SPI\IIC等）的驱动程序库（因支持包和工作流程而异）

- 工作流1
STM32CUBEMX工作流特定功能包括：

    1)  支持使用STM32CUBEMX集成进行外围设备配置
    2)  适合生成用于大容量部署的生产代码
    3)  包括用于数字I/O、ADC、PWM、IRQ的外围驱动器块
    4)  使用电机控制模块的示例模型开发电机转速控制
    5)  支持的硬件：STM32F4XX,STM32F7XX,STM32F4XX,和单核STM32H7XX(注意是单核，那个480M的可能不支持)，支持   NUCLEO-DISCOVERY

- 工作流2
开发板的特定功能包括：

    1)数字I/O，ADC,PWM,IRQ,I2C,SCI,SPI的外围驱动程序块板专用音频，MODBUS,WIFI,THINGSPEAR和环境传感器  
    2)支持的硬件开发板： STM32F746G 开发板    STM32V769I 开发板   STM32L475VG 开发板      STM32F4开发板



- 下面记录搭建环境的全过程:
        1)在matlab主页中点击附加功能
        <div align = center>
        ![avatar](/image/%E9%99%84%E5%8A%A0%E5%8A%9F%E8%83%BD.png)
        <div align = left>
        2）搜索embeeded coder support package for stmicroelectronics STM32 PROCESSORS。可以看到这里也有ti c2000系列支持包，arduino支持包。
        <div align = center>
        ![avatar](/image/embeddedSTM32.png)  
        <div align = left>
        3) 点击安装（根据自己的情况进行配置即可，有些细节在CSDN上查一下,这里要用梯子，否则可能出现安装失败的情况）
        4) 安装完成后需要进行配置，出现以下截图说明安装成功.(四个都打上绿勾即可，我这里已经安装过因此没有点击install)
        <div align = center>
        ![avatar](/image/%E5%AE%89%E8%A3%85%E5%AE%8C%E6%88%90.png)
        <div align = left>
        5）需要下载STM32F4DISCOVERY BOARD FIRMWARE PACKAGE以支持，这里直接点击download无法下载，可以在意法半导体官网直接下载该支持包.[下载链接](#<https://www.st.com/content/st_com/en/products/embedded-software/mcu-mpu-embedded-software/stm32-embedded-software/stm32-standard-peripheral-library-expansion/stsw-stm3206hdl=VZhiO7uh6WCv8FAmjZndng%3D%3D%2CcMwOhOMqoJLYxUp6IZ6yPR5fsu%2FDN7OxYoX03HaIC%2FS2ifIh1QIFhO0gQTBHH3kKvKRdg9nKJX8tcHWOMHdRS%2Fk97ggFL3Aqo%2F6ktYIeilILtvFHLrtY7AX4qc2VZHUkiVlHohAzi5STd5IbngDODs9lQU7xRnURf%2FVTxwwp7tmI2%2FFdex2AbS%2BesSYihGC2XoGN%2FAnyPGkWrkscNXH6Av5BQ8pji1HtotuntCqqLGUefY%2BKtAc4PLbfxSI6UwqbUjuzaJS%2BqfY2%2BogS46nDWe1VGn37zY%2FGPQKSzQYkcp0RjYhQ5TiBFAnX1s>)
        6)下载后解压，然后点击下一步，找到支持包的路径，然后点击validate,下方出现绿色√说明成功
        7）完成后续安装即可
        8）可以参考官网的相关例程

- 完成以上配置，即可开始后面的工作
## 以下案例对应的硬件平台为:NUCLEO_F401RE

1)点亮LED

- 硬件说明:LED灯的正极连接到单片机的PA5引脚，控制该引脚的电平即可

- STM32CUBEMX配置：打开STM32CUBEMX 选择对应的芯片(这里选择的是STM32F401RE)。
        <div align = center>
        ![avatar](/image/%E8%8A%AF%E7%89%87%E9%80%89%E6%8B%A9.png)
        <div align = left>
双击即可进入芯片配置界面
        <div align = center>
        ![avatar](/image/%E8%8A%AF%E7%89%87%E9%85%8D%E7%BD%AE.png)
        <div align = left>
然后配置一些RCC,GPIO啥的，配置流程如下

RCC
        <div align = center>
        ![avatar](/image/RCC_MODE.png)
        <div align = left>
CLOCK_CONFIGURATION
        <div align = center>
        ![avatar](/image/CLOCK_CONFIG.png)
        <div align = left>
DEBUG_MODE
        <div align = center>
        ![avatar](/image/DEBUG_MODE.png)
        <div align = left>
PROJECT
        <div align = center>
        ![avatar](/image/project.png)
        <div align = left>
GENERATE_CODE
        <div align = center>
        ![avatar](/image/generate_code.png)
        <div align = left>
ADVANCED SETTINGS
        <div align = center>
        ![avatar](/image/advanced_setting.png)
        <div align = left>
配置完成后将该工程文件保存到文件夹中，然后生成代码，至此STM32CUBEMX的配置就完成了。
        <div align = center>
        ![avatar](/image/生成工程文件.png)
        <div align = left>
##  Simulink配置过程
一、点亮第一个LED灯
        1)首先在simulink里面创建一个模型，这里记为LED_GREEN.slx，将该文件保存到上述工程文件夹中。
        2)按快捷键 CTRL+E,进行相关配置
硬件配置
        <div align = center>
        ![avatar](/image/%E7%A1%AC%E4%BB%B6%E9%85%8D%E7%BD%AE1.png)
        <div align = left>
硬件配置
        <div align = center>
        ![avatar](/image/%E7%A1%AC%E4%BB%B6%E9%85%8D%E7%BD%AE2.png)
        <div align = left>
代码生成
        <div align = center>
        ![avatar](/image/%E4%BB%A3%E7%A0%81%E7%94%9F%E6%88%90.png)
        <div align = left>
        3)搭建模型
库浏览器
        <div align = center>
        ![avatar](/image/库浏览器.png)
        <div align = left>

搭建模型
        <div align = center>
        ![avatar](/image/模型搭建.png)
        <div align = left>
模型参数设置
        <div align = center>
        ![avatar](/image/模型参数设置1.png)
        <div align = left>
        <div align = center>
        ![avatar](/image/模型参数设置2.png)
        <div align = left>
参数设置完成后，按快捷键CTRL+B即可生成工程文件，并自动进行烧录。
- 解决报错问题：（演示的时候解决）
- 以上开发方式适用于对STM32有基本了解的人员，该支持包目前还不成熟，体验较差。









下面介绍一种直接生成.c .h文件的方法，该方法相对简单，且适用性强，只要是基于C语言的嵌入式平台都能使用。

- 环境说明：

        MDK5.32     
        matlab_2022b        
        stm32cubemx-6.4     
        simulink        
        embedded coder      
        matlab coder        
        simulink coder      

-硬件支持：  NUCLEO-F401RE
stm32的开发目前只支持 stm32cubemx+hal库的模式，暂时不支持标准库
   



