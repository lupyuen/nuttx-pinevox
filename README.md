![PineVox Smart Speaker with 64-bit RISC-V BL606P on Apache NuttX RTOS](https://lupyuen.github.io/images/pinevox-title.jpg)

# PineVox Smart Speaker with 64-bit RISC-V BL606P on Apache NuttX RTOS

Let's boot Apache NuttX RTOS on PineVox!
- BL606P is the newer, scaled-down variant of BL808 (minus the Low-Power Core)
- NuttX already supports Ox64 BL808
- So NuttX might boot on PineVox BL606P!

BL606P looks something like this (minus the Low-Power Core)...

![BL808 SoC](https://lupyuen.github.io/images/ox64-cores.jpg)

_What's inside PineVox?_

According to the PineVox Schematic...

- __Mainboard PCB__ contains Bouffalo Lab BL606P SoC, Serial Console, Flash Memory, USB-C Port, Power Mgmt

- __Sideboard PCB__ contains Directional Microphone, Buttons, LEDs

- Connected by a 20-pin Ribbon Cable

USB-C Port connects to a Special USB Cable for PineVox that exposes (pic below)...

- __Boot Switch__ (GPIO 29)

  1: Boot from UART (Flashing Mode?)

  0: Boot from Flash

- __UART TX / RX__ (GPIO 20 / 21)

  (We assume it's for Flashing via BLDevCube)

- __USB Data__ (+ve and -ve)

  (For USB Peripherals? Maybe DFU?)

(Schematics will be published soon by PINE64)

![PineVox USB Cable](https://lupyuen.github.io/images/pinevox-usb.jpg)

_How to flash PineVox with OpenSBI and U-Boot Bootloader?_

Let's try flashing PineVox with BLDevCube...

- [__m0_lowload__](https://github.com/openbouffalo/OBLFR/tree/master/apps/m0_lowload) for M0 Wireless Core (32-bit):

  Firmware that forwards __Peripheral Interrupts__ to the D0 Multimedia Core.

  [(Like __SD Card__ and __GPIO Interrupts__)](https://lupyuen.github.io/articles/ox64#appendix-peripheral-interrupts)

- [__d0_lowload__](https://github.com/openbouffalo/OBLFR/tree/master/apps/d0_lowload) for D0 Multimedia Core (64-bit):

  Basic Bootloader that loads __OpenSBI, U-Boot Bootloader__ and Device Tree into RAM.

- As explained here: ["Flash OpenSBI and U-Boot"](https://lupyuen.github.io/articles/ox64#flash-opensbi-and-u-boot)

TODO: Flash OpenSBI and U-Boot Bootloader

_How to boot NuttX on PineVox?_

We have a problem...

- NuttX boots on Ox64 via microSD. But PineVox doesn't have a microSD Port! (Though BL606P supports microSD)

- Ox64 supports booting over Ethernet (with U-Boot Bootloader). But BL606P doesn't support Ethernet!

- We need a way to load NuttX Image into RAM at 0x5020_0000

- Maybe the M0 Wireless Core (32-bit) will load NuttX Image into RAM? Over WiFi?

_Why use U-Boot Bootloader? Why not flash NuttX directly as Firmware?_

- U-Boot might be simpler for now? Today NuttX boots in RISC-V Supervisor Mode, so we don't need to tweak it to boot in Machine Mode.

- Isolate the Toolchain: BL606P needs a special T-Head C906 Toolchain plus Bouffalo Lab SDK. It's better to isolate OpenSBI + U-Boot, compile them with the special toolchain.

  We'll keep them separate from NuttX, which compiles with the popular GCC Toolchain.

- Support Direct Firmware Update (DFU): In future we might support DFU to upgrade the NuttX Image wirelessly. We'll implement DFU with U-Boot (or something similar).

TODO: Load NuttX Image into RAM
