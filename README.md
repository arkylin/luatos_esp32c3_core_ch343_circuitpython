最近在淘宝上面购买了一个``Luatos ESP32 C3 Core`` 带 ``ch343`` 芯片的一块开发板

想刷个``micropython``发现刷进去之后无法连接到串口

之后看见在``circuitpython``上面有人发过这个板子的固件，于是刷个``circuitpython``，发现还是失败

最后找了一圈才发现这个板子分为两个版本，也就是在淘宝上面的``12.9元``的和``9.9元``的版本，一个带``usb``转串口``ch343``芯片一个不带，本来以为他们两个之间差别不大，于是就买了``12.9``的图省事，想直接连``type-c``，结果才发现掉进了大坑。

现在给大家分享一下如何为``Luatos-ESP32-C3-Core（经典版）``编译安装``CircuitPython``

编译环境：

Windows Wsl Archlinux

### 安装git和python环境

```shell
yay -S git
yay -S python-pip
pip install --upgrade -r requirements-dev.txt
pip install --upgrade -r requirements-doc.txt
```

### 拉取8.2版本

```shell
git clone -b 8.2.x https://github.com/adafruit/circuitpython.git
```

### 拉取子模块

不需要拉取全部模块，大样子太大了，只需要拉取esp方面的模块即可

```shell
cd ports/espressif
make fetch-port-submodules
```

![QQ截图20230827111805.png](https://www.ioioi.cn/usr/uploads/2023/08/237826333.png)

### 编译mpy-cross

```shell
cd ../..
make -C mpy-cross
```

### 安装ESP-IDF

如果上一步能够顺利拉下来的话，在ports/espressif下会有esp-idf目录

```shell
cd ports/espressif/esp-idf
./install.sh
```

### 上一步会有报错的

#### 1、libusb

![QQ截图20230827122453.png](https://www.ioioi.cn/usr/uploads/2023/08/2274932234.png)

需要安装``libusb``软件包

![QQ截图20230827122436.png](https://www.ioioi.cn/usr/uploads/2023/08/637175367.png)

安装完后，需要初始化ESP编译环境

```
. export.sh
```

### 开始编译

**由于``8.2.x``版本的``CircuitPython``暂时没有这个型号的开发板，于是在 [Github](https://github.com/adafruit/circuitpython/tree/main/ports/espressif/boards/luatos_core_esp32c3_ch343) 中找到上周提交的文件，将上面的文件夹手动复制到板子目录中**

```shell
cd ports/espressif
make BOARD=luatos_core_esp32c3_ch343 V=2 -j17
```

``V=2``开启日志显示、``-j17``使用多线程编译
然后就是等，我用的11代i7的CPU编译整整4个小时

### 编译报错解决

#### 1、cmake

![QQ截图20230827122513.png](https://www.ioioi.cn/usr/uploads/2023/08/389539361.png)

安装``cmake``

#### 2、glibc

![QQ截图20230827122551.png](https://www.ioioi.cn/usr/uploads/2023/08/3500035909.png)

安装``glibc``

#### 3、gcc

![QQ截图20230827123144.png](https://www.ioioi.cn/usr/uploads/2023/08/3117902083.png)

安装``gcc``

#### 4、ninja

![QQ截图20230827130029.png](https://www.ioioi.cn/usr/uploads/2023/08/1542043427.png)

安装``ninja``

### 编译完成

![QQ截图20230827152312.png](https://www.ioioi.cn/usr/uploads/2023/08/122707087.png)

进入``ports/espressif/build-luatos_core_esp32c3_ch343/``目录

将``firmware.bin``从0x0000开始刷写，即可成功

### 结束

![QQ截图20230827153642.png](https://www.ioioi.cn/usr/uploads/2023/08/1443564105.png)

已经将``luatos_core_esp32c3_ch343``板子的文件以及编译好的固件上传至 [我的Github](https://github.com/arkylin/luatos_esp32c3_core_ch343_circuitpython)

> 参考：
> [CSDN-applebomb](https://blog.csdn.net/applebomb/article/details/131797832)
> [CircuitPython-Docs](https://docs.circuitpython.org/en/latest/BUILDING.html)

