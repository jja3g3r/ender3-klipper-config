# Ender-3 Config Files + Modification Overview

These are the config files for my modded Ender-3.

### Hardware Modifications:
- BigTreeTech SKR V2.0 (32-bit board)
- Bed leveling probe
- Dual 5020 fans
- Slice Engineering Copperhead heat break
- Raspberry Pi Zero 2 W (Klipper)
- Step-down converter to power the Raspberry Pi (wired to the stock PSU)
- Silicone bed springs
- PEI build plate
- Oldham coupling + POM lead screw nut
- Screw-type X-axis belt tensioner
- Capricorn PTFE tube
- New pneumatic couplers
- Creality upgrade extruder

### Printed Modifications:
- Hero Me Gen 7
- Ball-bearing spool holder
- Side spool mount
- Improved Z-axis stepper motor mount
- Heated bed strain relief
- Raspberry Pi + converter cases
- Fan guards

### Still Planned:
- Bed linear rails (have some lying around)
- Extruder upgrade


# Klipper Setup — Raspberry Pi Zero + SKR Mini E3 V2.0

## 1. Install Mainsail OS

1. Install Mainsail OS to an SD card using Raspberry Pi Imager.
2. Insert the SD card into the Raspberry Pi Zero.
3. Connect the Pi to the network and power it on.


## 2. SSH into the Raspberry Pi

From Windows PowerShell:

```bash
ssh ender3@ender3
```

## 3. Increase Swap Memory

Create a 2 GB swap file:
```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

Check that the swap is active:
```bash
free -h
```


## 4. Configure Klipper Firmware

Go to the Klipper directory:
```bash
cd ~/klipper
```
Open the Klipper firmware configuration:
```bash
make menuconfig
```
### SKR Mini E3 V2.0 settings

Set:

    Micro-controller Architecture: STMicroelectronics STM32
    Processor model: STM32F103
    Bootloader offset: 28KiB bootloader
    Communication interface: USB
    GPIO pins to set at micro-controller startup: !PA14

Save and exit.

---

## 5. Compile the Klipper Firmware

Run:
```bash
make
```

The compiled firmware will be located at:

    ~/klipper/out/klipper.bin

---

## 6. Find the SKR USB Serial Name

Connect the SKR Mini E3 V2.0 to the Raspberry Pi using USB.

Run:
```bash
ls -l /dev/serial/by-id/
```
Example:

    usb-Klipper_stm32f103xe_35FFD6054246303221630757-if00

The complete path will look like:

    /dev/serial/by-id/usb-Klipper_stm32f103xe_35FFD6054246303221630757-if00

Use this path in printer.cfg:

    [mcu]
    serial: /dev/serial/by-id/usb-Klipper_stm32f103xe_35FFD6054246303221630757-if00

Use the /dev/serial/by-id/ path instead of /dev/ttyACM0 because the ttyACM number can change after reconnecting the board.

---

## 7. Copy the Firmware to Windows

From Windows PowerShell:
```bash
scp ender3@ender3:~/klipper/out/klipper.bin "$env:USERPROFILE\Downloads\klipper.bin"
```
The firmware will now be in:

    Downloads\klipper.bin

### Flash the SKR Mini E3 V2.0

1. Format an SD card as FAT32.
2. Copy klipper.bin onto the SD card.
3. Rename it to firmware.bin.
4. Turn off the printer.
5. Insert the SD card into the SKR Mini E3 V2.0.
6. Turn on the printer.
7. Wait for the firmware to flash.
8. Turn off the printer and remove the SD card.

---

## 8. Connect the SKR to the Raspberry Pi

Connect the SKR Mini E3 V2.0 to the Raspberry Pi using USB.

Check that the board is detected:
```bash
ls -l /dev/serial/by-id/
```
You should see something similar to:

    usb-Klipper_stm32f103xe_35FFD6054246303221630757-if00

---

## 9. Open Mainsail

Open this in your browser:

    http://ender3.local

---

## 10. Add printer.cfg

In Mainsail, go to:

    Machine -> printer.cfg

Paste your printer configuration into printer.cfg.

Make sure the MCU section contains your actual serial path:

    [mcu]
    serial: /dev/serial/by-id/usb-Klipper_stm32f103xe_35FFD6054246303221630757-if00

---

## 11. Add Macros

Add your Klipper macros to macro.cfg.

Then include it in printer.cfg:

    [include macros.cfg]

---

## 12. Restart Klipper

SAVE AND RESTART

---

# Quick Command Reference

## SSH into the Pi

    ssh ender3@ender3

## Go to Klipper

    cd ~/klipper

## Configure firmware

    make menuconfig

## Compile firmware

    make

## Find SKR USB serial

    ls -l /dev/serial/by-id/

## Check swap

    free -h

## Copy firmware to Windows

    scp ender3@ender3:~/klipper/out/klipper.bin "$env:USERPROFILE\Downloads\klipper.bin"

## Restart Klipper

    sudo systemctl restart klipper

## Check Klipper status

    sudo systemctl status klipper --no-pager

## Open Mainsail

    http://ender3.local