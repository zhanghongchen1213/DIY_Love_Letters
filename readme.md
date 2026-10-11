# DIY Love Letters

基于 Raspberry Pi Pico SDK 与 FreeRTOS 的互动装置固件。源码在 `pico_book/`，CMake 工程名仍是 `pico_examples`，可执行目标名为 `main`。

仓库地址：<https://github.com/zhanghongchen1213/DIY_Love_Letters>

## 功能

上电后初始化外设并启动 FreeRTOS 调度器。各任务通过事件组和心率队列协作：

- 光敏电阻超过阈值时点亮 LED 灯组，并通知氛围灯切换。
- MAX3010x 通过 I2C 轮询检测手指与心跳，计算滑动平均心率，写入队列。GP6 已初始化并使能中断，处理分支在 `#if 0` 中，当前不会响应。
- 两路触摸输入通过 GPIO 中断改变 WS2812 效果。
- WS2812 任务按事件切换彩虹、闪烁、心跳和呼吸灯。
- 板载 LED 以固定周期闪烁。
- 语音播报任务和 OLED 显示任务已创建，事件分支里的播放与刷屏代码仍是空的。`hardware_init()` 里的 `ssd1306_begin()` 也被注释掉了。

## 硬件连接

CMake 没有写死 `PICO_BOARD`，默认按 Pico SDK 的 `pico`（RP2040）构建。引脚来自源码宏；心率传感器使用板级默认 I2C 引脚，官方 Pico 板对应 GP4 / GP5。

| 功能 | 接口 | 引脚 |
| --- | --- | --- |
| 板载 LED | GPIO | GP25 |
| 触摸 A / 触摸 B | GPIO 输入，上拉 | GP16 / GP17 |
| 心率中断（当前未处理） | GPIO | GP6 |
| LED 灯组 | GPIO 输出 | GP18、GP19、GP20、GP21 |
| 独立 LED | GPIO 输出 | GP15 |
| WS2812 | PIO0 | GP22，共 60 颗 |
| 光敏电阻 | ADC | GP26 |
| MP3 模块 | UART1，9600 8N1 | TX GP8，RX GP9 |
| SSD1306 128×32 | I2C1，地址 0x3C | SDA GP2，SCL GP3 |
| MAX30102 / MAX30105 | I2C0，400 kHz | SDK 默认 SDA / SCL |

光敏开灯阈值是 `1500`，关灯阈值是 `100`。

## 目录

```text
CMakeLists.txt              根工程，要求 Pico SDK 2.0.0 或更高
pico_sdk_import.cmake       定位 Pico SDK
pico_book/                  本装置固件
  CMakeLists.txt            未设置 FREERTOS_KERNEL_PATH 时直接跳过
  FreeRTOSConfig.h          FreeRTOS 配置
  src/                      任务与驱动，目标名为 main
build/                      本地构建目录，不入库
```

`pico_book/src/ws2812.pio.h` 由 `pico_generate_pio_header` 写到源码目录，需要保留。`build/` 只含编译器目标文件和依赖记录，删除后重新配置即可恢复。

## 构建

需要本机已安装 CMake、Ninja 或 Make，以及 ARM GCC。Pico SDK 版本至少为 2.0.0。

```bash
export PICO_SDK_PATH=/path/to/pico-sdk
export FREERTOS_KERNEL_PATH=/path/to/FreeRTOS-Kernel

cmake -S . -B build
cmake --build build
```

未设置 `FREERTOS_KERNEL_PATH` 时，`pico_book` 子目录会被跳过，不会生成固件。

烧录文件：

```text
build/pico_book/src/main.uf2
```

按住 Pico 的 BOOTSEL 再上电，把该 UF2 复制到出现的 U 盘即可。

## 许可

`LICENSE.TXT` 是 Raspberry Pi Pico 示例仓库带入的 BSD-3-Clause 许可。`CONTRIBUTING.md` 仍指向 `raspberrypi/pico-examples`，不代表本仓库的贡献流程。
