# rpi3serbtldr
Raspberry Pi 3 Serial Bootloader In Rust

FIXME: rx not made public yet.

This project contains two parts of a bootloader intended to assist in bare metal development on a Raspberry Pi 3. The first part is a receiver (rx) which runs on the Raspberry Pi as a bare metal application. The second part is a transmitter (tx) which runs on a PC. The bootloader uses the RPi's serial port and a simple protocol to upload a binary ARM executable file from the PC into the RPi's memory where it will be run.
