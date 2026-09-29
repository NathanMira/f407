# STM32F407 LED 交替闪烁

基于 **STM32F407ZGT6** 的 Keil 工程。上电后两颗板载 LED 以 500 ms 为周期交替闪烁。工程由 STM32CubeMX 生成，使用 HAL 库。

## 硬件

| 项目 | 说明 |
| --- | --- |
| 芯片 | STM32F407ZGT6，LQFP144 |
| 调试接口 | SWD（PA13 = SWDIO，PA14 = SWCLK） |
| 主晶振 | HSE 8 MHz，经 PLL 得到 168 MHz 系统时钟 |
| LED0 | PF9，推挽输出 |
| LED1 | PF10，推挽输出 |

引脚与正点原子探索者 STM32F407 开发板一致。该板 LED 为低电平点亮：引脚输出低电平亮，高电平灭。

## 程序在做什么

`Src/main.c` 完成时钟和 GPIO 初始化后进入死循环：

1. PF9 输出高电平，PF10 输出低电平，延时 500 ms。
2. PF9 输出低电平，PF10 输出高电平，延时 500 ms。
3. 重复以上两步。

在低电平点亮的板上，效果是 LED0 与 LED1 交替亮灭。上电初始电平为高，两灯默认熄灭。

时钟配置在 `SystemClock_Config()`：

- HSE = 8 MHz，PLLM = 8，PLLN = 336，PLLP = 2，系统时钟 168 MHz
- AHB = 168 MHz，APB1 = 42 MHz，APB2 = 84 MHz

## 目录

```
f407/
├── f407.ioc          CubeMX 工程，改引脚或外设时用它重新生成代码
├── Inc/              头文件（main.h、gpio.h 等）
├── Src/              用户代码（main.c、gpio.c 等）
├── Drivers/          CMSIS 与 STM32F4 HAL
└── MDK-ARM/          Keil 工程
    ├── f407.uvprojx  用 Keil 打开这个文件
    └── startup_stm32f407xx.s
```

用户代码写在 `USER CODE BEGIN` / `USER CODE END` 之间。用 CubeMX 重新生成时，标记外的内容会被覆盖，标记内的会保留。

## 编译与下载

1. 安装 Keil MDK-ARM（本工程按 V5.32 生成）和 STM32F4 器件包。
2. 打开 `MDK-ARM/f407.uvprojx`。
3. 编译工程，通过 ST-Link 以 SWD 方式下载。

固件包版本为 STM32Cube FW_F4 V1.28.3，CubeMX 版本为 6.18.1。

## 版本管理

编译产物和本机 Keil 配置已写入 `.gitignore`，不进入仓库。包括 `MDK-ARM/f407/` 下的 `.hex`、`.axf`、`.o`，以及 `*.uvguix.*`、`*.uvoptx`、`DebugConfig/`、`.mxproject`。需要纳入版本管理的是源码、`f407.ioc` 和 `MDK-ARM/f407.uvprojx`。
