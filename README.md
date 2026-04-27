# M5stickC-IDF

> ESP-IDF component to work with M5StickC  
> inf: This item is only available for ESP IDF versions below 3.3  
> Compiler Environment:esp-idf-v3.2.3  
> TFT lib base [loboris TFT library ](https://github.com/loboris/ESP32_TFT_library)

---
## Local setup notes

This project is a legacy ESP-IDF project. Use ESP-IDF v3.2.3 and the matching
GCC 5.2.0 Xtensa toolchain; modern ESP-IDF releases do not build this code
without porting old driver/event/TFT APIs.

The setup below was tested on Arch Linux with the project checked out at:

```sh
~/workspace/M5StickC-IDF
```

### System packages

Install the legacy ESP-IDF build prerequisites:

```sh
sudo pacman -S --needed gcc git make ncurses flex bison gperf
```

### ESP-IDF v3.2.3

Clone ESP-IDF v3.2.3 beside the project:

```sh
mkdir -p ~/workspace/esp
git clone -b v3.2.3 --recursive https://github.com/espressif/esp-idf.git ~/workspace/esp/esp-idf-v3.2.3
```

On modern GCC, the old ESP-IDF ncurses probe can fail with a misleading
``ncurses`` error. Patch its generated test program to use an explicit return
type:

```sh
sed -i 's/main() {}/int main(void) { return 0; }/' \
  ~/workspace/esp/esp-idf-v3.2.3/tools/kconfig/lxdialog/check-lxdialog.sh
```

### ESP32 toolchain

Download and extract the matching ESP32 toolchain:

```sh
cd ~/workspace/esp
curl -L https://dl.espressif.com/dl/xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz \
  -o xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz
tar -xzf xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz
```

Verify the expected compiler:

```sh
export PATH=~/workspace/esp/xtensa-esp32-elf/bin:$PATH
xtensa-esp32-elf-gcc --version
```

Expected version:

```text
xtensa-esp32-elf-gcc (crosstool-NG crosstool-ng-1.22.0-80-g6c4433a) 5.2.0
```

### Python packages

Install the Python packages expected by ESP-IDF v3.2.3:

```sh
python -m pip install -r ~/workspace/esp/esp-idf-v3.2.3/requirements.txt
```

If `pkg_resources` is missing after installing requirements, pin setuptools to
a version that still ships it:

```sh
python -m pip install 'setuptools<81'
```

### Build

Use the legacy IDF and toolchain for every build shell:

```sh
cd ~/workspace/M5StickC-IDF
export IDF_PATH=~/workspace/esp/esp-idf-v3.2.3
export PATH=~/workspace/esp/xtensa-esp32-elf/bin:$PATH
make -j4
```

### Flash and monitor

Plug in the M5StickC or M5StickC Plus and check the serial port:

```sh
ls -l /dev/ttyUSB* /dev/ttyACM*
```

Flash at a conservative baud rate:

```sh
make flash ESPPORT=/dev/ttyUSB0 ESPBAUD=115200
```

Open the serial monitor:

```sh
make monitor ESPPORT=/dev/ttyUSB0
```

Exit the monitor with:

```text
Ctrl+]
```

### M5StickC Plus display note

The original M5StickC display is 80 x 160. M5StickC Plus uses a 135 x 240
ST7789v2 display, so code with hardcoded dimensions may need adjustment before
the UI fills the Plus screen correctly.

---
## File Tree
```
.
├── components
│   └── m5stickc-idf
│       ├── include
│       │   ├── button.h
│       │   ├── i2smic.h
│       │   └── m5stickc.h
│       ├── util
│       │   ├── font
│       │   │   ├── DefaultFont.c
│       │   │   ├── DejaVuSans18.c
│       │   │   ├── DejaVuSans24.c
│       │   │   ├── SmallFont.c
│       │   │   ├── Ubuntu16.c
│       │   │   ├── comic24.c
│       │   │   ├── def_small.c
│       │   │   ├── default_tiny_9pt.c
│       │   │   ├── minya24.c
│       │   │   └── tooney32.c
│       │   ├── include
│       │   │   ├── AXP192.h
│       │   │   ├── MPU6886.h
│       │   │   ├── axp192_reg.h
│       │   │   ├── spi_master_lobo.h
│       │   │   ├── stmpe610.h
│       │   │   ├── tft.h
│       │   │   ├── tftspi.h
│       │   │   └── wire.h
│       │   ├── AXP192.c
│       │   ├── MPU6886.c
│       │   ├── i2smic.c
│       │   ├── spi_master_lobo.c
│       │   ├── tft.c
│       │   ├── tftspi.c
│       │   └── wire.c
│       ├── CMakeLists.txt
│       ├── LICENSE
│       ├── button.c
│       ├── component.mk
│       └── m5stickc.c
├── main
│   ├── CMakeLists.txt
│   ├── Kconfig.projbuild
│   ├── component.mk
│   └── main.c
├── CMakeLists.txt
├── Makefile
└── sdkconfig
```

## Versions
* Version 0.0.1 2019-10-21@Hades
	* Creat this project
	* Add LCD Device Library
	* Add Wire(I2C) Library
	* Add AXP192 Library
	* Add MPU6886 Library
---
* Version 0.0.2 2019-10-22@Hades
	* Edit AXP192 Library 
	```C
	float AXP192GetVinVoltage(wire_t *wire)
	```

	* Edit main-Examples 
		* Add Demo for LCD
		* Add Demo for AXP192
		* Add Demo for MPU6886
		* Add Demo for Button
---
* Version 1.0.1 2019-10-23@Hades

	* remove power.c & power.h
	* remove event.c & event.h
	* remove display.c & display.h

	* Edit I2S micophone Examles
	```C
	void MicRecordTest()
	```
	* edit m5stick.c 
