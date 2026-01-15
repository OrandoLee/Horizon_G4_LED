# Horizon_G4_LED

## 1. 项目简介

**Horizon_G4_LED** 是 **Horizon-G4** 大型嵌入式开发计划的第一个子项目（P1）。

本项目旨在 STM32G431 开发平台上，通过对底层寄存器逻辑的抽象与硬件协议的对齐，实现**指令解析控灯**与**实时遥测数据可视化**的完整闭环。

## 2. 核心技术细节

### 2.1 高频系统时钟配置 (170MHz Clock Tree)

利用 STM32G4 系列的高性能特性，对时钟树进行了深度的数学建模与配置：

- **HSE 入口**：配置为外部 **24\text{MHz}** 晶振。
- **PLL 计算链条**：
  - **分频 (PLLM)**：**24\text{MHz} \div 6 = 4\text{MHz}**（获取最佳 PLL 参考频率）。
  - **倍频 (PLLN)**：**4\text{MHz} \times 85 = 340\text{MHz}**（符合 VCO 物理压控范围 **128\text{MHz} \sim 544\text{MHz}**）。
  - **系统输出 (PLLR)**：**340\text{MHz} \div 2 = 170\text{MHz}** (HCLK 主频)。

### 2.2 硬件锁存控制逻辑 (Latch Logic)

针对板载 LED 采用 **74HC573 锁存器** 隔离设计的特点，实现了精准的时序控制：

- **控制链路**：通过 **PD2 (LE)** 引脚作为闸门，控制 **PC8~PC15** 数据流向 LED。
- **电平协议**：LED 为共阳极连接，引脚输出 **Low** 为点亮，**High** 为熄灭。
- **时序规范**：
  1. 拉高 PD2（开门）。
  2. 更新 PC8~PC15 状态。
  3. 拉低 PD2（锁存并关门）。

## 3. 功能特性

- **双向指令解析**：通过 **USART1 (115200bps)** 接收字符指令，实现字符 `'1'` 开灯与 `'0'` 关灯的逻辑解析。
- **实时遥测输出 (Telemetry)**：利用 `printf` 重定向技术，通过 **FireWater 协议** 向上位机推送模拟锯齿波形。
- **数据可视化**：配合 **VOFA+** 上位机，通过手动绑定 **I0 通道**，实现 **10\text{ms}** 采样周期的波形实时监测。

## 4. 软件环境

- **配置工具**：STM32CubeMX (Version 6.x)
- **IDE**：Keil uVision5 (MDK-ARM)
- **调试器**：DAP-Link / ST-Link (SWD 模式)
- **上位机**：VOFA+ (v1.3.10)

## 5. 关键 API 记录

在开发过程中，重点掌握了以下 **HAL 库** 驱动函数：

- `HAL_UART_Receive(&huart1, &data, 1, 10)`：串口阻塞式接收指令。
- `HAL_GPIO_WritePin(GPIOx, Pin, State)`：引脚电平精确控制。
- `HAL_Delay(ms)`：系统节拍延时（基于 **170\text{MHz}** 自动校准）。