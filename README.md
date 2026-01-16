# Horizon_G4_LED

- ## 1. 项目简介

  本项目的目标是：

  - 熟悉 STM32CubeMX 的时钟与 GPIO 配置流程。
  - 掌握 STM32 HAL 库中 GPIO 控制函数的使用。
  - 实现 LED 的周期性闪烁。

  ## 2. 硬件资源

  - **开发板**: STM32G431RBT6 (ARM Cortex-M4 @ 170MHz)
  - **LED 引脚**:
    - `LD2` -> `PC8` (部分通用开发板)

  ## 3. 开发环境

  - **配置工具**: STM32CubeMX
  - **IDE**: Keil MDK v5 / STM32CubeIDE
  - **烧录工具**: CMSIS-DAP Debugger

  ## 4. 核心配置步骤

  ### STM32CubeMX 配置

  1. **RCC**: 开启 HSE (外部高速时钟)，并在 Clock Configuration 中配置主频至 **170MHz**。
  2. **GPIO**:
     - 选择对应引脚（如 `PC8`）设为 `GPIO_Output`。
     - 设置输出电平为 `High` (初始熄灭)，模式为 `Push-Pull`。
  3. **Project Manager**: 选择MDK-ARM并生成代码。

  ### 核心代码实现

  在 `main.c` 的 `while(1)` 循环中添加以下代码：

  C

  ```
  /* USER CODE BEGIN WHILE */
    while (1)
    {
  	HAL_GPIO_WritePin(GPIOD, GPIO_PIN_2, GPIO_PIN_SET);   // 1. 打开锁存器闸门
      HAL_GPIO_WritePin(GPIOC, GPIO_PIN_8, GPIO_PIN_RESET); // 2. 给低电平点亮 LED8
      HAL_GPIO_WritePin(GPIOD, GPIO_PIN_2, GPIO_PIN_RESET); // 3. 关闭闸门锁死状态
      /* USER CODE END WHILE */
  
      /* USER CODE BEGIN 3 */
    }
  ```

  ## 5. 编译与烧录

  1. 在 IDE 中点击 **Build** (F7) 编译项目，确保无 Error。
  2. 通过 CMSIS-DAP 连接开发板与电脑。
  3. 点击 **Download** (F8) 将程序烧录至芯片。
  4. 观察开发板上的 LED,其开始以 1Hz 的频率闪烁。

  ## 6. 关键 API 记录

在开发过程中，重点掌握了以下 **HAL 库** 驱动函数：

- `HAL_GPIO_WritePin(GPIOx, Pin, State)`：引脚电平精确控制。


