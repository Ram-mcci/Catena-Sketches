# Catena 4551 Test Sketch

<!-- TOC depthFrom:2 updateOnSave:true -->

- [Introduction](#introduction)
- [Getting Started](#getting-started)
    - [Clone this repository into a suitable directory on your system](#clone-this-repository-into-a-suitable-directory-on-your-system)
    - [Install the MCCI STM32 board support library](#install-the-mcci-stm32-board-support-library)
    - [Select your desired band](#select-your-desired-band)
    - [Installing the required libraries](#installing-the-required-libraries)
        - [List of required libraries](#list-of-required-libraries)
    - [Build and Download](#build-and-download)
    - [Load the sketch into the Catena](#load-the-sketch-into-the-catena)
- [Provision your Catena 4551](#provision-your-catena-4551)
- [Notes](#notes)
    - [Setting up DFU on a Linux or Windows PC](#setting-up-dfu-on-a-linux-or-windows-pc)
    - [Data Format](#data-format)
    - [Unplugging the USB Cable while running on batteries](#unplugging-the-usb-cable-while-running-on-batteries)
    - [Deep sleep and USB](#deep-sleep-and-usb)

<!-- /TOC -->

## Introduction

This sketch demonstrates the MCCI Catena&reg; 4551 as a remote temperature/humidity/light sensor using a LoRaWAN&reg;-technology network to transmit to a remote server.

The Catena 4551 is a single-board LoRaWAN-enabled sensor device with the following integrated sensors:

- BME280 temperature, humidity, and pressure sensor
- Silicon Labs Si1133 light sensor
- Runs from USB or a primary battery source.

Documents on the MCCI Catena 4551 are at https://github.com/mcci-catena/HW-Designs/tree/master/Boards/Catena-4551.

* [Catena 4551 User Manual](https://github.com/mcci-catena/HW-Designs/blob/master/Boards/Catena-4551/How-to-Get-Started-with-Catena4551-r2.pdf).
* [Catena 4551 Pin Mapping Diagram](https://github.com/mcci-catena/HW-Designs/blob/master/Boards/Catena-4551/Catena-4551-Rev-C-Pinout.png)

## Getting Started

In order to use this code, you must do several things:

1. Clone this repository into a suitable directory on your system.
2. Install the MCCI Arduino board support package (BSP).
3. Install the required Arduino libraries using `git`.
4. Build the sketch and download to your Catena 4551.

After you have loaded the firmware, you have to set up the Catena 4551.

This sketch uses the Catena-Arduino-Platform library to store critical information on the integrated FRAM. There are several kinds of information. Some things only have to be programmed once in the life of the board; other things must be programmed whenever you change network connections. Entering this information this involves entering USB commands via the Arduino serial monitor.

- We call information about the 4551 that (theoretically) never changes "identity".
- We call information about the LoRaWAN "provisioning".

### Clone this repository into a suitable directory on your system

This is best done from a command line. You can use a number of techniques, but since you'll need a working git shell, we recommend using the command line.

On Windows, we strongly recommend use of "git bash", available from [git-scm.org](https://git-scm.com/download/win). Then use the "git bash" command line system that's installed by the download.

At the end of this process, you'll have a directory called `{somewhere}/Catena-Sketches`. You get to choose `{somewhere}`. Everyone has their own convention; the author typically has a directory in his home directory called `sandbox`, and then puts projects there.

Once you have a suitable command line open, you can enter the following commands. In the following, change `{somewhere}` to the directory path where you want to put `Catena-Sketches`.

```console
$ cd {somewhere}
$ git clone https://github.com/mcci-catena/Catena-Sketches
Cloning into 'Catena-Sketches'...
remote: Counting objects: 729, done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 729 (delta 15), reused 20 (delta 9), pack-reused 703
Receiving objects: 100% (729/729), 714.26 KiB | 1.25 MiB/s, done.
Resolving deltas: 100% (396/396), done.

$ # get to the right subdirectory
$ cd Catena-Sketches/catena4551_test01

$ # confirm that you're in the right place.
$ ls
catena4551_test01.ino  git-repos.dat  README.md
```

### Install the MCCI STM32 board support library

Open the Arduino IDE. Go to `File>Preferences>Settings`. Add `https://github.com/mcci-catena/arduino-boards/raw/master/BoardManagerFiles/package_mcci_index.json` to the list in `Additional Boards Manager URLs`.

If you already have entries in that list, use a comma (`,`) to separate the entry you're adding from the entries that are already there.

Next, open the board manager. `Tools>Board:...`, and get up to the top of the menu that pops out -- it will give you a list of boards. Search for `MCCI` in the search box and select `MCCI Catena STM32 Boards`. An `[Install]` button will appear to the right; click it.

Then go to `Tools>Board:...` and scroll to the bottom. You should see `MCCI Catena 4551`; select that.

### Select your desired band

When you select a board, the default LoRaWAN region is set to US-915, which is used in North America and much of South America. If you're elsewhere, you need to select your target region. You can do it in the IDE:

![Select Band plan](../extra/assets/select-band-plan.gif)

As the animation shows, use `Tools>LoRaWAN Region...` and choose the appropriate entry from the menu.

### Installing the required libraries

This sketch uses several sensor libraries.

The script [`git-boot.sh`](https://github.com/mcci-catena/Catena-Sketches/blob/master/git-boot.sh) in the top directory of this repo will get all the things you need.

It's easy to run, provided you're on Windows, macOS, or Linux, and provided you have `git` installed. We tested on Windows with git bash from [git-scm.org](https://git-scm.org), on macOS 10.11.3 with the git and bash shipped by Apple, and on Ubuntu 16.0.4 LTS (64-bit) with the built-in bash and git from `apt-get install git`.

```console
$ cd Catena-Sketches/catena4551_test01
$ ../git-boot.sh
Cloning into 'Catena-Arduino-Platform'...
remote: Counting objects: 1201, done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 1201 (delta 27), reused 24 (delta 14), pack-reused 1151
Receiving objects: 100% (1201/1201), 275.99 KiB | 0 bytes/s, done.
Resolving deltas: 100% (900/900), done.
...

==== Summary =====
*** No repos with errors ***

*** No existing repos skipped ***

*** No existing repos were updated ***

New repos cloned:
Adafruit_BME280_Library                 Catena-mcciadk
MCCI_FRAM_I2C                           OneWire
Arduino-Temperature-Control-Library     arduino-lmic
Catena-Arduino-Platform                 arduino-lorawan
SHT1x
```

It has a number of advanced options; use `../git-boot.sh -h` to get help, or look at the source code [here](https://github.com/mcci-catena/Catena-Sketches/blob/master/git-boot.sh).

#### List of required libraries

This sketch depends on the following libraries.

* [`github.com/mcci-catena/Catena-Arduino-Library`](https://github.com/mcci-catena/Catena-Arduino-Library)
* [`github.com/mcci-catena/arduino-lorawan`](https://github.com/mcci-catena/arduino-lorawan)
* [`github.com/mcci-catena/Catena-mcciadk`](https://github.com/mcci-catena/Catena-mcciadk)
* [`github.com/mcci-catena/arduino-lmic`](https://github.com/mcci-catena/arduino-lmic)
* [`github.com/mcci-catena/MCCI_FRAM_I2C`](https://github.com/mcci-catena/MCCI_FRAM_I2C)
* [`github.com/mcci-catena/Adafruit_BME280_Library`](https://github.com/mcci-catena/Adafruit_BME280_Library)
* [`github.com/mcci-catena/Arduino-Temperature-Control-Library`](https://github.com/mcci-catena/Arduino-Temperature-Control-Library)
* [`github.com/mcci-catena/OneWire`](https://github.com/mcci-catena/OneWire)
* [`github.com/mcci-catena/SHT1x`](https://github.com/mcci-catena/SHT1x)

### Build and Download

Shutdown the Arduino IDE and restart it, just in case.

Ensure selected board is 'MCCI Catena 4551' (in the GUI, check that `Tools`>`Board "..."` says `"MCCI Catena 4551"`.

In the IDE, use File>Open to load the `catena4551_test01.ino` sketch. (Remember, in step 1 you cloned `Catena-Sketches` -- find that, and navigate to `{somewhere}/Catena-Sketches/catena4551_test01`.

Follow normal Arduino IDE procedures to build the sketch: `Sketch`>`Verify/Compile`. If there are no errors, go to the next step.

### Load the sketch into the Catena

Make sure the correct port is selected in `Tools`>`Port`.

Load the sketch into the Catena using `Sketch`>`Upload` and move on to provisioning.

## Provision your Catena 4551

In order to provision the Catena, refer the following document: [How-To-Provision-Your-Catena-Device](https://github.com/mcci-catena/Catena-Sketches/blob/master/extra/How-To-Provision-Your-Catena-Device.md).

## Notes

### Setting up DFU on a Linux or Windows PC

Early versions of the MCCI BSP do not include an INF file (Windows) or sample rules (Linux) to teach your system what to do. The procedures posted [here](https://github.com/vpowar/LoRaWAN_SensorNetworks-Catena#uploading-code) show how to set things up manually.

You may also refer to the detailed procedures that are part of the user manual. Please see:

* [Catena 4551 User Manual](https://github.com/mcci-catena/HW-Designs/blob/master/Boards/Catena-4551/How-to-Get-Started-with-Catena4551-r2.pdf).

### Data Format

Refer to the [Protocol Description](../extra/catena-message-port3-format.md) in the `extras` directory for information on how data is encoded.

### Unplugging the USB Cable while running on batteries

The Catena 4551 dynamically uses power from the USB cable if it's available, even if a battery is connected. This allows you to unplug the USB cable after booting the Catena 4551 without causing the Catena 4551 to restart.

Unfortunately, the Arduino USB drivers for the Catena 4551 do not distinguish between cable unplug and USB suspend. Any `Serial.print()` operation referring to the USB port will hang if the cable is unplugged after being used during a boot. The easiest work-around is to reboot the Catena after unplugging the USB cable. You can avoid this by using the Arduino UI to turn off DTR before unplugging the cable... but then you must remember to turn DTR back on. This is very fragile in practice.

### Deep sleep and USB

When the Catena 4551 is in deep sleep, the USB port will not respond to cable attaches. When the 4551 wakes up, it will connect to the PC while it is doing its work, then disconnect to go back to sleep.

While disconnected, you won't be able to select the COM port for the board from the Arduino UI. And depending on the various `operatingflags` settings, even after reset, you may have trouble catching the board to download a sketch before it goes to sleep.

The workaround is use DFU boot mode to download the catena-hello sketch from [Catena-Arduino-Platform](https://github.com/mcci-catena/Catena-Arduino-Platform), and use the serial port to reset any required flags so you can get control after boot.
