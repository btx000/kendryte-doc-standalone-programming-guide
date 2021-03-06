# 集成电路内置总线(I²C)

## 概述

I2C 总线用于和多个外部设备进行通信。多个外部设备可以共用一个 I2C 总线。

## 功能描述

I2C 模块具有以下功能：

- 独立的 I2C 设备封装外设相关参数
- 自动处理多设备总线争用

## API参考

对应的头文件 `i2c.h`

为用户提供以下接口

- i2c\_init

- i2c\_init\_as\_slave

- i2c\_send\_data

- i2c\_send\_data\_dma

- i2c\_recv\_data

- i2c\_recv\_data\_dma

### i2c\_init

#### 描述

配置 I²C 器件从地址、寄存器位宽度和 I²C 速率。

#### 函数原型

```c
void i2c_init(i2c_device_number_t i2c_num, uint32_t slave_address, uint32_t address_width, uint32_t i2c_clk)
```

#### 参数

| 参数名称     |   描述     |  输入输出  |
| :--------   | :-----     | :----:     |
| i2c\_num | I²C号 | 输入 |
| slave\_address | I²C 器件从地址 | 输入|
| address\_width | I²C 器件寄存器宽度(7或10) | 输入
| i2c\_clk | I²C 速率 (Hz) | 输入 |

#### 返回值

无。

### i2c\_init\_as\_slave

#### 描述

配置 I²C 为从模式。

#### 函数原型

```c
void i2c_init_as_slave(i2c_device_number_t i2c_num, uint32_t slave_address, uint32_t address_width, const i2c_slave_handler_t *handler)
```

#### 参数

| 参数名称     |   描述     |  输入输出  |
| :--------   | :-----     | :----:     |
| i2c\_num | I²C号 | 输入 |
| slave\_address | I²C 从模式的地址 | 输入|
| address\_width | I²C 器件寄存器宽度(7或10) | 输入
| handler | I²C 从模式的中断处理函数 | 输入 |

#### 返回值

无。

### i2c\_send\_data

#### 描述

写数据。

#### 函数原型

```c
int i2c_send_data(i2c_device_number_t i2c_num, const uint8_t *send_buf, size_t send_buf_len)
```

#### 参数

| 参数名称     |   描述     |  输入输出  |
| :--------:   | :-----     | :----:     |
| i2c\_num | I²C号 | 输入 |
| send\_buf | 待传输数据 | 输入 |
| send\_buf\_len | 待传输数据长度 | 输入 |

#### 返回值

| 返回值 | 描述 |
| :----  | :----|
| 0      | 成功 |
| 非0    | 失败 |

### i2c\_send\_data\_dma

#### 描述

通过DMA写数据。

#### 函数原型

```c
void i2c_send_data_dma(dmac_channel_number_t dma_channel_num, i2c_device_number_t i2c_num, const uint8_t *send_buf, size_t send_buf_len)
```

#### 参数

| 参数名称         |   描述         |  输入输出  |
| :-------------- | :------------- | :-------- |
| dma\_channel\_num | 使用的dma通道号 | 输入       |
| i2c\_num        | I²C号          | 输入       |
| send\_buf       | 待传输数据      | 输入       |
| send\_buf\_len  | 待传输数据长度  | 输入       |

#### 返回值

无

### i2c\_recv\_data

#### 描述

通过CPU读数据。

#### 函数原型

```c
int i2c_recv_data(i2c_device_number_t i2c_num, const uint8_t *send_buf, size_t send_buf_len, uint8_t *receive_buf, size_t receive_buf_len)
```

#### 参数

| 参数名称               |   描述       |  输入输出  |
| :-------------------- | :----------- | :-------- |
| i2c\_num              | I²C 总线号    | 输入      |
| send\_buf             | 待传输数据，一般情况是i2c外设的寄存器，如果没有设置为NULL | 输入 |
| send\_buf\_len        | 待传输数据长度，如果没有则写0 | 输入 |
| receive\_buf          | 接收数据内存   | 输出      |
| receive\_buf\_len     | 接收数据的长度 | 输入      |

#### 返回值

| 返回值 | 描述 |
| :---- | :----|
| 0     | 成功 |
| 非0   | 失败 |

### i2c\_recv\_data\_dma

#### 描述

通过dma读数据。

#### 函数原型

```c
void i2c_recv_data_dma(dmac_channel_number_t dma_send_channel_num, dmac_channel_number_t dma_receive_channel_num,
    i2c_device_number_t i2c_num, const uint8_t *send_buf, size_t send_buf_len, uint8_t *receive_buf, size_t receive_buf_len)
```

#### 参数

| 参数名称                 |   描述              | 输入输出  |
| :---------------------- | :------------------ | :------- |
| dma\_send\_channel\_num    | 发送数据使用的dma通道 | 输入     |
| dma\_receive\_channel\_num | 接收数据使用的dma通道 | 输入     |
| i2c\_num                | I²C 总线号           | 输入     |
| send\_buf               | 待传输数据，一般情况是i2c外设的寄存器，如果没有设置为NULL | 输入 |
| send\_buf\_len          | 待传输数据长度，如果没有则写0 | 输入 |
| receive\_buf            | 接收数据内存         | 输出     |
| receive\_buf\_len       | 接收数据的长度       | 输入      |

#### 返回值

无

### 举例

```c
/* i2c外设地址是0x32, 7位地址，速率200K */
i2c_init(I2C_DEVICE_0, 0x32, 7, 200000);
uint8_t reg = 0;
uint8_t data_buf[2] = {0x00,0x01}
data_buf[0] = reg;
/* 向0寄存器写0x01 */
i2c_send_data(I2C_DEVICE_0, data_buf, 2);
i2c_send_data_dma(DMAC_CHANNEL0, I2C_DEVICE_0, data_buf, 4);
/* 从0寄存器读取1字节数据 */
i2c_receive_data(I2C_DEVICE_0, &reg, 1, data_buf, 1);
i2c_receive_data_dma(DMAC_CHANNEL0, DMAC_CHANNEL1, I2C_DEVICE_0,&reg, 1, data_buf, 1);
```

## 数据类型

相关数据类型、数据结构定义如下：

- i2c\_device\_number\_t：i2c号。

- i2c\_slave\_handler\_t：i2c从模式的中断处理函数句柄

### i2c\_device\_number_t

#### 描述

i2c编号。

#### 定义

```c
typedef enum _i2c_device_number
{
    I2C_DEVICE_0,
    I2C_DEVICE_1,
    I2C_DEVICE_2,
    I2C_DEVICE_MAX,
} i2c_device_number_t;
```

### i2c\_slave\_handler\_t

#### 描述

i2c从模式的中断处理函数句柄。根据不同的中断状态执行相应的函数操作。

#### 定义

```c
typedef struct _i2c_slave_handler
{
    void(*on_receive)(uint32_t data);
    uint32_t(*on_transmit)();
    void(*on_event)(i2c_event_t event);
} i2c_slave_handler_t;
```

#### 成员

| 成员名称 | 描述 |
| :----- | :--- |
| I2C\_DEVICE\_0 | I2C 0 |
| I2C\_DEVICE\_1 | I2C 1 |
| I2C\_DEVICE\_2 | I2C 2 |
