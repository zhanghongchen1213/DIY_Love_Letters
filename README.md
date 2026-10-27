<p align="center">
  <img src="https://image-osb.obs.cn-east-3.myhuaweicloud.com/blog/2024-11-23/love-letter/r02/cover/cover-wechat-6c2dd6241a14.webp" alt="打开的活页手帐，左页是树莓派 Pico、心率模块和小屏，右页是纸艺摩天轮，书页边缘绕着亮起的灯带" width="100%">
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-111111?style=flat-square" alt="MIT"></a>
  <img src="https://img.shields.io/badge/Pico%20SDK-2-111111?style=flat-square&logo=raspberrypi&logoColor=white" alt="Pico SDK 2">
  <img src="https://img.shields.io/badge/C%2B%2B-17-111111?style=flat-square&logo=cplusplus&logoColor=white" alt="C++ 17">
  <img src="https://img.shields.io/badge/CMake-3-111111?style=flat-square&logo=cmake&logoColor=white" alt="CMake 3">
  <img src="https://img.shields.io/badge/FreeRTOS-Kernel-111111?style=flat-square" alt="FreeRTOS Kernel">
</p>

<p align="center">
  <b>树莓派 Pico 上的电子情书固件。</b><br>
  改好引脚、灯珠数量和光敏阈值，重新编译烧录，就能在自己的 Pico 上跑这套灯效和传感。
</p>

<p align="center">
  <a href="#效果演示">效果演示</a> ·
  <a href="#功能">功能</a> ·
  <a href="#跑起来">跑起来</a> ·
  <a href="#目录">目录</a> ·
  <a href="#改成你的">改成你的</a> ·
  <a href="#交流与贡献">交流与贡献</a>
</p>

## 效果演示

成品是一本 6 孔活页手帐。翻开封面灯带亮起，碰一下触摸键灯光换色，手指按上传感器灯带变红、小屏显示心率，翻到最后一页灯板亮起，照着那封手写信。

翻开封面，插袋里是树莓派 Pico 和带喇叭的 MP3 模块。书一打开，边沿的 WS2812 就开始跑彩虹。

![翻开封面，内封板边沿的灯带亮起彩虹色](https://image-osb.obs.cn-east-3.myhuaweicloud.com/blog/2024-11-23/love-letter/r02/001.webp)

再往下翻，纸艺摩天轮跟着页面立起来，灯带在后面替它打光。

![纸艺照片摩天轮随页面立起，身后的灯带透出紫粉色的光](https://image-osb.obs.cn-east-3.myhuaweicloud.com/blog/2024-11-23/love-letter/r02/003.webp)

信的旁边有一颗圆形触摸键。手指轻轻一碰，灯带就换一种颜色，或者闪起来。

![手写信卡旁的圆形触摸键亮着蓝光，三根线顺着活页环走线](https://image-osb.obs.cn-east-3.myhuaweicloud.com/blog/2024-11-23/love-letter/r02/006.webp)

手指按到心率传感器上，整圈灯带变成红色，一闪一闪地跳。屏幕上的数字先是 0，等一会儿跳成实测心率。

![手指按在心率传感器上，整圈灯带变成红色](https://image-osb.obs.cn-east-3.myhuaweicloud.com/blog/2024-11-23/love-letter/r02/007.webp)

![OLED 屏上显示 Heart Rate 53](https://image-osb.obs.cn-east-3.myhuaweicloud.com/blog/2024-11-23/love-letter/r02/008.webp)

翻到最后一页，光照进来，灯板上的四颗 LED 一起亮起，照着下面那封手写信。

![底封内板中央的 LED 灯板打出红黄白光柱，四周围着一圈灯带](https://image-osb.obs.cn-east-3.myhuaweicloud.com/blog/2024-11-23/love-letter/r02/013.webp)

![最后一页是一封黑卡白字的手写信，下方的灯板和灯带亮着](https://image-osb.obs.cn-east-3.myhuaweicloud.com/blog/2024-11-23/love-letter/r02/014.webp)

## 这是什么

这份固件跑在树莓派 Pico 上，用 FreeRTOS 同时驱动灯带、触摸、心率、光敏末页灯和 MP3 模块。适合想按同一套互动，把这本电子手帐做出来的人。

仓库交付的是 C/C++ 源码和 CMake 工程。编译前要另外准备 Pico SDK 2.0.0 或更新版本，以及 FreeRTOS Kernel。原理图、电路板和 MP3 音频文件不在本仓库里。

手帐怎么装、每一页在成品里做什么，写在文末的配套文章里。

## 功能

| 能力 | 结果 |
| --- | --- |
| **上电灯带** | 60 颗 WS2812 从 GPIO 22 开始跑彩虹 |
| **触摸 A** | 按下点亮 GPIO 15 的独立灯，松开后关掉，灯带改为闪烁 |
| **触摸 B** | 按下后灯带回到彩虹 |
| **心率** | 手指放上后灯带改为红色心跳，拿开后改为粉色呼吸。BPM 送进队列 |
| **末页灯** | 光敏达到 1500 时 GPIO 18–21 点亮，灯带改为橙色呼吸；回到 100 及以下时这四路灯关掉 |
| **板载灯** | GPIO 25 每 200 毫秒翻转一次 |
| **屏幕与语音** | OLED 初始化被注释掉，显示任务收到心率后没有往屏幕上画。MP3 只做了串口初始化，播放分支是空的 |

## 跑起来

需要 CMake 3.12 或更高、C11、C++17，以及 Pico SDK 2.0.0 或更高。用 `PICO_SDK_FETCH_FROM_GIT` 在线拉取 SDK 时，CMake 需要 3.17 或更高。编译器使用 Pico SDK 所要求的 Arm GNU 工具链，本仓库没有单独锁定小版本。

FreeRTOS Kernel 不在仓库里。把它放到本机，并设置 `FREERTOS_KERNEL_PATH`。未设置这个路径时，`pico_book` 不会加入构建，工程里也就没有 `main`。

```bash
git clone https://github.com/zhanghongchen1213/DIY_Love_Letters.git
cd DIY_Love_Letters
```

```bash
export PICO_SDK_PATH=/path/to/pico-sdk
export FREERTOS_KERNEL_PATH=/path/to/FreeRTOS-Kernel
cmake -S . -B build
cmake --build build
```

需要 USB 调试输出时，在 `cmake` 配置命令里加上 `-DPICO_STDIO_USB=1`。加上之后，等待 USB 连接的超时默认是 3000 毫秒，这个默认值写在根目录 `CMakeLists.txt`。

可执行目标名是 `main`。`pico_add_extra_outputs` 会生成 `.uf2`，位于 `build/pico_book/src/main.uf2`。按住板载 BOOTSEL 再插入 USB，把该文件拷进新出现的磁盘即可。仓库里没有烧录脚本。

本固件不链接 pico-extras。只有本机设置了 `PICO_EXTRAS_PATH`，或 SDK 同级目录里存在 `pico-extras` 时，CMake 才会把它加进工程。

## 目录

根目录 CMake 工程名是 `pico_examples`，可执行目标是 `main`。

```text
.
├── CMakeLists.txt
├── LICENSE
├── pico_extras_import_optional.cmake
├── pico_sdk_import.cmake
└── pico_book/
    ├── CMakeLists.txt
    ├── FreeRTOSConfig.h
    ├── FreeRTOSConfig_examples_common.h
    ├── FreeRTOS_Kernel_import.cmake
    └── src/
        ├── CMakeLists.txt
        ├── main.cpp
        ├── data_global.c / data_global.h
        ├── irq.cpp / irq.h
        ├── led.cpp / led.h
        ├── lightR.cpp / lightR.h
        ├── ws2812.cpp / ws2812.h / ws2812.pio / ws2812.pio.h
        ├── max30102.cpp / max30102.h
        ├── heartRate.cpp / heartRate.h
        ├── Mp3.cpp / Mp3.h
        ├── ssd1306_i2c.c / ssd1306_i2c.h
        ├── ssd1306_font.h
        ├── raspberry26x32.h
        └── debug.h
```

`main.cpp` 负责任务和硬件初始化，引脚宏在各自的头文件里。

## 改成你的

下表是当前接线和阈值。改完后重新执行 `cmake --build build`，确认仍能生成 `main.uf2`。

| 要改的内容 | 当前值 | 位置 |
| --- | --- | --- |
| **灯带数据脚** | GPIO 22 | `pico_book/src/ws2812.h` 的 `WS2812_PIN` |
| **灯珠数量** | 60 | `pico_book/src/data_global.h` 的 `REG_LED_SUM` |
| **触摸 A / B** | GPIO 16 / 17 | `pico_book/src/irq.h` |
| **独立灯 / 末页四路灯** | GPIO 15 / 18–21 | `pico_book/src/led.h` |
| **光敏** | GPIO 26；开灯 1500，关灯 100 | `pico_book/src/lightR.h` 的 `ADC_PIN`；`data_global.h` 的 `BRIGHTNESS`、`CLOSEBTIGHTNESS` |
| **OLED** | I2C1，SDA GPIO 2，SCL GPIO 3，地址 0x3C | `pico_book/src/ssd1306_i2c.h` |
| **心率** | I2C0，地址 0x57；SDA/SCL 用板级默认引脚 | `pico_book/src/max30102.h` 的 `MAX30105::begin` |
| **MP3** | UART1，TX GPIO 8，RX GPIO 9，9600 | `pico_book/src/Mp3.h` |

树莓派 Pico 的板级默认 I2C 引脚是 GP4（SDA）和 GP5（SCL）。心率数据就绪中断接在 GPIO 6，对应处理包在 `#if 0` 里，当前心率靠轮询红外值，手指判定阈值是 `irValue > 100000`。

驱动文件名是 `max30102`，类名和器件 ID 按 MAX30105。板上丝印如果是其中一款，改驱动时两处一起看。

## 遇到问题时

| 现象 | 先检查 |
| --- | --- |
| **配置完成，却没有 `main` 目标** | `FREERTOS_KERNEL_PATH` 是否指向 FreeRTOS-Kernel。未设置时 `pico_book/CMakeLists.txt` 会跳过固件 |
| **CMake 提示 Pico SDK 版本不够** | 本地 SDK 是否为 2.0.0 或更新。检查写在根目录 `CMakeLists.txt` |
| **串口反复打印 `MAX30105 was not found`** | 心率模块的供电和 I2C 接线。`begin()` 失败后程序停在 `pico_book/src/main.cpp` 的死循环 |
| **灯带长度或引脚不对** | `REG_LED_SUM` 与 `WS2812_PIN` 是否和灯带一致 |

## 交流与贡献

部署和使用时遇到问题，可以到 [Issues](https://github.com/zhanghongchen1213/DIY_Love_Letters/issues) 提问。用这套源码做出自己的成果后，也欢迎在 Issue 里留下地址。

发现 Bug 或有明确的改动建议，请 [提交 Issue](https://github.com/zhanghongchen1213/DIY_Love_Letters/issues/new)。本仓库没有单独的贡献说明和安全政策，改代码前先用 Issue 说明改动范围。不要在公开 Issue 里贴密钥、令牌或环境变量文件。

**本文资料 · 接着动手**

**鸿尘客公众号**  
扫码关注鸿尘客公众号，回复 **“查看原文”**，获取本文开源资料与配套内容。

![鸿尘客公众号二维码](https://image-osb.obs.cn-east-3.myhuaweicloud.com/wemedia/wxlogo.webp)

**鸿尘客独立站**

点击下方获取本文**开源资料**与**配套内容**  
[开源资料：https://zhcmqtt.top/article/love-letter](https://zhcmqtt.top/article/love-letter)

![官网](https://image-osb.obs.cn-east-3.myhuaweicloud.com/wemedia/honchic_logo.webp)

## 许可

本仓库以 **MIT** 授权。SPDX 标识符为 `MIT`，版权人是鸿尘客（Honchic），版权年份为 2024。具有法律效力的许可全文在仓库根目录的 [LICENSE](LICENSE)。

在所有副本或实质性部分中保留上述版权声明和许可声明的前提下，可以免费使用、复制、修改、合并、发布、分发、再许可或销售本软件及其配套文档。软件按「原样」提供，不包含适销性、特定用途适用性、不侵权等任何明示或暗示担保。作者与版权人不对因使用或无法使用本软件引起的索赔、损害或其他责任负责。

本授权覆盖本仓库中的源代码、配置、脚本和文档。第三方依赖保留各自许可证，不因本仓库采用 MIT 而改变。

`pico_book/src/ssd1306_font.h` 的文件头是 Raspberry Pi (Trading) Ltd. 的 BSD-3-Clause。`pico_book/FreeRTOSConfig_examples_common.h` 的文件头是 Amazon.com, Inc. 的 MIT 许可。这两份声明留在原文件里。
