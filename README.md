# rpi3serbtldr

Raspberry Pi 3 Serial Bootloader In Rust

## About
This project is part of Richard's Adventures in Rust Embedded.

**FIXME:**

 * rx module has a sad and isn't ready for public release yet.
 * Add checksum verification to protocol.

The repo contains two parts of a bootloader intended to assist in bare metal development on a Raspberry Pi 3. The first part is a receiver (rx) which runs on the Raspberry Pi as a bare metal application. The second part is a transmitter (tx) which runs on a PC. The bootloader uses the RPi's serial port and a simple protocol to upload a binary ARM executable file from the PC into the RPi's memory where it will be run.

The bootloader started out as a raspbootin clone described here:

 * [https://wiki.osdev.org/ARM_RaspberryPi#Boot-from-serial_kernel]
 * [https://github.com/rust-embedded/rust-raspi3-OS-tutorials/tree/master/06_raspbootin64] 

There were protocol features I felt were missing from raspbootin so rpi3serbtldr emerged.

## Installation

### Clone

Clone the repository into a local directory:

```
$ git clone https://github.com/rahealy/rpi3serbtldr.git
```

### Build

I haven't figured out how to get cargo to only build the tx/rx subcrates from the top level. For now just run the build process from each directory. 

```
$ cd rpiserbtldr/tx
$ cargo build
$ cd rpiserbtldr/rx
$ cargo build
```

### Set Up SD Card

The Broadcom chip which the RPi3 is based on was designed in a way that makes the booting process complicated. I use an SD card that has been set up with the RPi foundations official Raspbian Linux and then make alterations to run the rpiserbtldr_rx code instead of the Linux kernel code. An extra bonus of doing it this way is that booting Raspbian Linux is a matter of renaming files.

**Install Raspian:**

 * Install Raspian Linux per instructions here: [https://www.raspberrypi.org/downloads/raspbian/]
 * Boot Raspbian Linux on the RPi to verify everything works.

**Disable booting Raspbian Linux by changing kernel file names:**

 * rename "/boot/kernel.img" to "/boot/kernel.img.nope"
 * rename "/boot/kernel7.img" to "/boot/kernel7.img.nope"

**Set RPi to enable UART for serial communication on boot:**

 * edit "/boot/config.txt" and add a line containing:
 
   ```
   enable_uart=1
   ```

**Replace kernel with receiver (rx):**

 * Copy rx program from build directory to "/boot/kernel8.img"

**Boot Raspbian Linux Instead**

 * rename "/boot/kernel.img.nope" to "/boot/kernel.img"
 * rename "/boot/kernel7.img.nope" to "/boot/kernel7.img"
 * rename "/boot/kernel8.img" to "/boot/kernel8.img.nope"


## Example Setup

In this example I use an Arduino Uno board with the microcontroller removed as a USB->Serial converter to let the host computer transmit the binaries to the Raspberry Pi 3.

<img src="serial_setup.jpg" alt="Connections between the Raspberry Pi 3 and the Arduino Uno board" height="700" width="933"/>

### Arduino Board

USB-Serial converter per suggestion to remove the ATMEL chip as described here:

[https://create.arduino.cc/projecthub/PatelDarshil/ways-to-use-arduino-as-usb-to-ttl-converter-475533]

### Arduino Connector Notes:

 * Connector labelled TX is actually RX
 * Connector labelled RX is actually TX

### RPI3 Connector Notes:

 * Connector pin 6 is GND
 * Connector pin 8 is TX
 * Connector pin 10 is RX

### Resistor Divider Notes:

RPi UART0 uses 3.3v logic while the Arduino uses 5.0v logic requiring a passive resistor divider between the Arduino connector (labelled RX) and RPi connector pin 10 (RX). The Arduino seems to be able to handle the 3.3v signal from the RPi connector pin 8 (TX) just fine.

### Divider (Pictured):

 * A 1k Ohm resistor connects between the Arduino connector labelled RX and RPi connector pin 10 (RX).
 * A 2.2k Ohm resistor connects between RPi connector pin 10 (RX) and GND.
 * A 0 Ohm resistor connects between Arduino connector labelled TX and RPI connector pin 8 (TX)
 * The RPi Connector pin 6 (GND) and the Arduino connector labelled GND are connected.
 
## Example Session

```
rpi3serbtldr/tx/target/debug$ ./rpi3serbtldr_tx -b 115200 -p "/dev/ttyACM0" -f "kernel8.img" -t 8000

rpi3serbtldr_tx
---------------
File: kernel8.img
Port: "/dev/ttyACM0"
Baud: 115200
Timeout(ms): 8000

Begin...
Timed out while trying to read port. 10 retries left.
Timed out while trying to read port. 9 retries left.
Receieved break signal.
Sent file size: 1448.
Got OK signal.
Sending file.
Got OK signal.
File sent successfully. Done.

rpi3serbtldr/tx/target/debug$
```
