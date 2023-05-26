## GPIO

### GPIO简介

    GPIO作为通用输入输出端口，其全称为General Purpose Input Output，能够通过软件自由配置引脚状态及工作模式。除通用输入输出功能外，GPIO端口还可被用作第二功能的配置，即为复用功能。每个I/O引脚都有相应支持的复用功能，可通过GPIO_CFG(0xf87xx[5:0])寄存器配置为复用功能。(yc3122中f87xx中的'xx'的范围为0~0x7f)

### 功能描述

    具体电气特性见datasheet(IO的上下拉内阻约为50K)

* FP ：0xfa810[5] 为0控制 FP信号，阻断IO与芯片的连接
* HOLD ：0xfaa14[0]为1控制HOLD信号，使能GPIO 保持当前状态
* PU ：0xf87xx[7:6] 为0x01 控制PU 使能 IO将上拉
* PD ：0xf87xx[7:6] 为0x10 控制PD 使能 IO将下拉
* OEN ：输出使能信号
* I ：输出信号（1:输出高、0：输出低）
* IE：输入使能信号
* C：输入信号
* ANA_IN : 0xf87xx[7:6] 为0x11 控制ANA_IN 使能 IO将上下拉

<pre class="vditor-reset" placeholder="" contenteditable="true" spellcheck="false"><p data-block="0"><img src="https://file+.vscode-resource.vscode-cdn.net/d%3A/work/module/docsify/YC3122/md/image/GPIO/1680263519212.png" alt="1680263519212"/></p></pre>

### 通用输入输出

#### 输入模式

    通过配置 0xf87xx 寄存器中的bit[7:6]配置为输入模式；00：浮空输入	01：上拉输入	10：下拉输入	11：模拟输入。Gpio默认使用AHB时钟。

* 浮空输入:
  * 浮空就是输入引脚即不接高电平也不接低电平，如上图所示，I/O端口的数据在每个AHB时钟被采样到输入数据寄存器，通过读访问输入数据寄存器获取当前I/O状态。
* 上拉输入：
  * 电阻与VCC相连，形成上拉电阻，I/O端口在空闲时为高电平，能够用于检测由高到低的电平变化。
* 下拉输入：
  * 电阻与GND相连，形成下拉电阻，I/O端口在空闲时为低电平，能够用于检测由低到高的电平变化。
* 模拟输入：
  * 模拟输入是模拟信号的输入，adc检测的io需配置为模拟输入。

#### 输出模式

* 通过配置 0xf87xx 寄存器中的bit[5:0]配置为输出模式；3f：输出高	3e：输出低
* 特殊io有可配开漏输出的功能（IO 70/76/60/48/40/33/22/10）
  * 开漏输出: 0xf87e0[0] 置1配置开漏输出使能

### 复用功能说明

每个I/O引脚都有相应支持的复用功能，可通过GPIO_CFG(0xf87xx[5:0])寄存器配置为复用功能。

对应值的功能与可映射的 io范围说明。

| **寄存器值** | **功能**           | **备注**                                                                                                    |
| ------------ | ------------------ | ----------------------------------------------------------------------------------------------------------- |
| 0            | Input(float)       |                                                                                                             |
| 1            | **预留**           |                                                                                                             |
| 2            | QSPI_NCS           | **可映射范围**GPIO[32:0]**和GPIO[76:56]**                                                                   |
| 3            | QSPI_SCK           | **可映射范围**GPIO[32:0]**和GPIO[76:56]**                                                                   |
| 4            | QSPI_IO0           | **可映射范围**GPIO[32:0]**和GPIO[76:56]**                                                                   |
| 5            | QSPI_IO1           | **可映射范围**GPIO[32:0]**和GPIO[76:56]**                                                                   |
| 6            | QSPI_IO2           | **可映射范围**GPIO[32:0]**和GPIO[76:56]**                                                                   |
| 7            | QSPI_IO3           | **可映射范围**GPIO[32:0]**和GPIO[76:56]**                                                                   |
| 8            | UART0_TXD          |                                                                                                             |
| 9            | UART0_RXD          |                                                                                                             |
| 10           | UART0_RTS          |                                                                                                             |
| 11           | UART0_CTS          |                                                                                                             |
| 12           | UART1_TXD          | **可映射范围**GPIO[63:0]                                                                                    |
| 13           | UART1_RXD          | **可映射范围**GPIO[63:0]                                                                                    |
| 14           | UART1_RTS          | **可映射范围**GPIO[63:0]                                                                                    |
| 15           | UART1_CTS          | **可映射范围**GPIO[63:0]                                                                                    |
| 16           | PWM_OUT0~7         | **每8个**GPIO**为一组，分别映射到**PWM0~7                                                                   |
| 17           | PWM8               | **可映射范围**GPIO[79:48]                                                                                   |
| 18           | DCMI_PCLK          |                                                                                                             |
| 19           | DCMI_VSYNC         |                                                                                                             |
| 20           | DCMI_HSYNC         |                                                                                                             |
| 21           | DCMI_D0            |                                                                                                             |
| 22           | DCMI_D1            |                                                                                                             |
| 23           | DCMI_D0_D13        | GPIO[7]到GPIO[76]，每14个为一组，分别映射为D**13**到**D0**                                                  |
| 24           | SPI0_NCS           | 可映射范围GPIO[79:0]                                                                                        |
| 25           | SPI0_SCK           | 可映射范围GPIO[79:0]                                                                                        |
| 26           | SPI0_MOSI          | 可映射范围GPIO[79:0]                                                                                        |
| 27           | SPI_SDIO           | **每**3**个**GPIO**为一组，分别映射为**SPI0_SDIO/SPI1_SDIO/SPI2_SDIO                                        |
| 28           | SPI0_MISO          | 可映射范围GPIO[79:0]                                                                                        |
| 29           | SPI_SLV_IN         | **每**6**个**GPIO**为一组，分别映射为**SPI0~2**的**NCS_IN/SCK_IN                                            |
| 30           | XTAL32K            | [15:0]**和**[79:48]                                                                                         |
| 31           | SPI2_NCS           | **新增高速**SPI**接口，可映射为所有引脚**                                                                   |
| 32           | SPI2_SCK           | **新增高速**SPI**接口，可映射为所有引脚**                                                                   |
| 33           | SPI2_MOSI          | **新增高速**SPI**接口，可映射为所有引脚**                                                                   |
| 34           | SPI2_MISO          | **新增高速**SPI**接口，可映射为所有引脚**                                                                   |
| 35           | DAC                | **每**2**个**GPIO**为一组，分别映射为**out_p/out_n                                                          |
| 36           | UART2_TXD          | **可映射范围**GPIO[79:20]                                                                                   |
| 37           | UART2_RXD          | **可映射范围**GPIO[79:20]                                                                                   |
| 38           | UART2_RTS          | **可映射范围**GPIO[79:20]                                                                                   |
| 39           | UART2_CTS          | **可映射范围**GPIO[79:20]                                                                                   |
| 40           | PWM01              | GPIO_INDEX%8**不为0和1**的引脚，每两个为一组，分别映射为**PWM0/PWM1**                                       |
| 41           | PWM23              | GPIO_INDEX%8**不为**2**和3的引脚，每两个为一组，分别映射为**PWM2/PWM3                                       |
| 42           | SDIO               | **每**6**个**GPIO**为一组，分别映射为**SDIO_CLK/SDIO_CMD/DAT0/DAT1/DAT2/DAT3                                |
| 43           | PSRAM_NCS          | QSPI2                                                                                                       |
| 44           | PSRAM_SCK          | QSPI2                                                                                                       |
| 45           | PSRAM_DATA         | QSPI2,**每**4**个**GPIO**为一组，分别映射为**D0/D1/D2/D3                                                    |
| 46           | JTAG_RV            | **每**4**个**GPIO**为一组，分别映射为**TCK/TMS/TDI/TDO                                                      |
| 47           | IIC1               | **每**2**个**GPIO**为一组，分别映射为**SCL/SDA                                                              |
| 48           | SPI1_NCS           | 可映射范围GPIO[79:0]                                                                                        |
| 49           | SPI1_SCK           | 可映射范围GPIO[79:0]                                                                                        |
| 50           | SPI1_MOSI          | 可映射范围GPIO[79:0]                                                                                        |
| 51           | GPCFG_SCI7816_2_IO | SCI7816_2                                                                                                   |
| 52           | SPI1_MISO          | 可映射范围GPIO[79:0]                                                                                        |
| 53           | UART3              | **可映射范围**GPIO[60:20]``每**4**个**GPIO**为一组，**GPIO[20]**到**GPIO[60]**分别映射为**CTS/TXD/RXD/RTS** |
| 54           |                    |                                                                                                             |
| 55           | GPIO_CLK_OUT       | **偶数引脚对应**gpio0_clk_sel**，奇数引脚对应**gpio1_clk_sel                                                |
| 56           | GPCFG_SCI7816_IO   |                                                                                                             |
| 57           | GPCFG_ICE          |                                                                                                             |
| 58           | IIC0_SCL           |                                                                                                             |
| 59           | IIC0_SDA           |                                                                                                             |
| 60           | JTAG_M0_SWCLK      | **前**16**个引脚，映射为SWCLK，后48个引脚每**2**个**GPIO**为一组，分别映射为**SWCLK/SWDIO                   |
| 61           | JTAG_M0_SWDIO      | **前**16**个引脚，映射为**SWDIO                                                                             |
| 62           | GPIO_OUTPUT_LOW    |                                                                                                             |
| 63           | GPIO_ OUTPUT_HIGH  |                                                                                                             |
| 64           | PU                 |                                                                                                             |
| 128          | PD                 |                                                                                                             |
| 192          | ANALOG             | IE**为**0                                                                                                   |

### 功能配置

#### io的时钟配置

* gpio的时钟源来自于 clk_ahb, 使能开关0xf840c[11] ,置0为en，置1为off。芯片上电默认使能，gpio clk 不使能操作gpio相关寄存器会失败。

#### io的输出配置

* 任意io的推挽输出： GPIO_CONFIG 0xf8700 ~0xf877f，每个byte控制一个IO，配置值可为上表的值。
* 特殊io的开漏输出：
  * 开漏使能：GPIO_OD 0xf87e0 (bit7~4依次对应GPIO 70/76/60/48) (bit3 ~0依次对应GPIO 40/33/22/10）
  * 输出：GPIO_CONFIG 0xf8700 ~0xf877f 特殊IObyte 配置为 输出 即写63

#### io的输入状态获取

* GPIO_IN 0xf87d0~0xf87d9  每个bit对应一个IO，1为高电平，0为低电平

#### io的中断配置

* 使能：0xf8780~0xf8789 每1bit对应1个gpio，置1使能
* 触发模式：
  * 高低电平触发：GPIO_LTS 0xf8790 ~0xf8799 启动电平触发模式，每个bit控制一个IO，0为高电平中断，1为低电平中断（当GPIO_RTS和GPIO_FTS同时为0时有效）
  * 上升沿触发：GPIO_RTS 0xf87a0 ~0xf87a9 GPIO上升沿中断选择，每个bit控制一个IO（当此比特为1时，GPIO_LTS无效）
  * 下降沿触发：GPIO_FTS 0xf87b0 ~0xf87b9 GPIO下降沿中断选择，每个bit控制一个IO  (当此比特为1时，GPIO_LTS无效)

#### io的中断状态获取

* GPIO_ISR 0xf87c0 ~0xf87c9 GPIO中断标志状态，每一比特对应一个GPIO，对应比特写1清除对应中断标志。
* GPIO_IRQ_INDEX	0xf87cf  GPIO所有中断中，编号最小的GPIO序号

### 典型示例

```
//gpio0 输出高电平
*(volatile uint8_t*)(0xf8700) = 0x3f;

```

### FEQ常见问题

#### GPIO_Config是不是不能用这个配置上拉？试了不起作用；得用GPIO_Init？

* 能不能配置上拉最终看寄存器0xf8700~0xf877f的值。
* GPIO_Config 配置函数传参的宏枚举与GPIO_Init 枚举参数不一样。
