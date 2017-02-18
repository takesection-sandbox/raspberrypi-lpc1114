Raspberry Pi から LPC1114 への書き込み
---

# lpc21isp のコンパイル

## Download

* [lpc21isp](https://sourceforge.net/projects/lpc21isp/)

## Build

```
$ make CFLAGS="-Wall -DGPIO_ISP=23 -DGPIO_RST=18"
```

# Raspberry Pi の GPIO のセットアップ

```
#!/bin/sh

# Configure -ISP signal
echo 23 >/sys/class/gpio/export
echo out >/sys/class/gpio/gpio23/direction
echo 1 >/sys/class/gpio/gpio23/value
chown root.gpio /sys/class/gpio/gpio23/value
chmod 660 /sys/class/gpio/gpio23/value

# Configure -RST signal
echo 18 >/sys/class/gpio/export
echo out >/sys/class/gpio/gpio18/direction
echo 1 >/sys/class/gpio/gpio18/value
chown root.gpio /sys/class/gpio/gpio18/value
chmod 660 /sys/class/gpio/gpio18/value
```

## プロジェクトの作成

[platformio](http://platformio.org/) を使ってビルドします

```
$ platformio init --board=lpc1114fn28
```

## コード

src/main.cpp

```cpp
#include "mbed.h"

DigitalOut myled(LED1);

int main() {
    while(1) {
        myled = 1;
        wait(0.5);
        myled = 0;
        wait(0.5);
    }
}
```

## コンパイル

```
$ platformio run
```

## 書き込み

```
lpc21isp -control -bin .pioenvs/lpc1114fn28/firmware.bin \
    /dev/ttyAMA0 115200 48000
```
