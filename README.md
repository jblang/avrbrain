# AVRBrain

AVRbrain is an [AVR128DB64](https://www.microchip.com/en-us/product/avr128db64) dev board.  The AVR Dx series are modern AVRs with many desireable features over the classic ATmega AVRs, such as multi-voltage I/O, configurable logic, advanced analog features, and a single-wire programming and debug interface.

![3D Render](avrbrain-front.png)

## Arduino Compatibility

AVRbrain is compatible with the [DxCore](https://github.com/SpenceKonde/DxCore) Arduino Core. I have tested the AVR128DB28 on a breadboard with the same programming circuit I intend to use on the finished board, and confirmed that it works.

Once flashed in UPDI programming mode, the Optiboot_Dx bootloader can be used to reprogram the board in serial comms mode, and there is an auto-reset circuit connected to the DTR line (possible to disable with a jumper).

## PlatformIO Compatibility

AVRbrain is compatible with [PlatformIO](https://platformio.org/) using the [Atmel megaAVR platform](https://docs.platformio.org/en/latest/platforms/atmelmegaavr.html).  PlatformIO supports Arduino development using DxCore, or native development using [avr-libc](https://www.nongnu.org/avr-libc/).  

Here's the `platformio.ini` that I used to build a simple non-Arduino led blinking example for the AVR128DB64:
```
[env:AVR128DB28]
platform = atmelmegaavr
platform_packages = platformio/toolchain-atmelavr@^3.70300.220127
board = AVR128DB28
upload_speed = 115200
upload_flags = 
    --tool
    uart
    --device
    avr128db28
    --uart
    $UPLOAD_PORT
    --clk
    $UPLOAD_SPEED
upload_command = pymcuprog write --erase $UPLOAD_FLAGS --filename $SOURCE
```

The above example can be made to work with DxCore just by adding the line `framework = arduino` to this file.

## Hardware Features
- Onboard CH340C USB-to-serial converter with a micro-USB connector, and a switch to select between USB communication or programming via SerialUPDI.  
- Onboard 3.3V regulator for MVIO. The board is configured to have 3.3V on PORTC and 5V on the other ports.
- SD card holder connected to 3.3V SPI Bus, and an I2C RTC (PCF8563T) with battery backup via CR 1612 coin cell.
- [UEXT port](https://www.olimex.com/Products/Modules/) that exports 3.3V UART, I2C, and SPI buses.  Many peripheral modules are available from Olimex.
- [Qwiic I2C port](https://www.sparkfun.com/qwiic) to work with the Qwiic and Stemma QT ecosystems. Many peripheral modules are available from Sparkfun and Adafruit.
- Headers breaking out all pins of the 8-bit ports A-G, except pin 7 of port F which is UPDI. Pin sockets can be used for stackable shield-style add-on boards, or pin headers can be used to connect to breadboards or elsewhere using IDC cables.
- Port F is used for UPDI, Reset, the USB UART, SD card chip select (level shifted), and optionally the RTC's interrupt and 32 kHz clock signals.
- The RTC's interrupt and 32 kHz clock signals can be connected to port F by adding a solder bridge on the surface mount jumper. The RTC can be disconnected from the I2C bus by cutting the traces in the SDA and SCL jumper pads in case of a conflict with other RTC devices.
- UPDI header for use with a [PicKit](https://www.microchip.com/en-us/development-tool/pg164150) and [MPLAB X](https://www.microchip.com/en-us/tools-resources/develop/mplab-x-ide) for debugging.
- Same size as a Raspberry Pi Model B, though it is not mechanically or electrically compatible. The right-side mounting holes are on the corners instead of toward the middle of the board, and no attempt has been made to reproduce the GPIO header pinout used on the Pi.

## Relationship to z80ctrl NG

[z80ctrl NG](https://github.com/jblang/z80ctrl/tree/master/hardware/nextgen) is the next-generation z80ctrl board I am designing concurrently with AVRbrain.  It uses all the same hardware components, including the AVR128DB64, the USB chip, RTC, voltage regulator, sd card slot, etc.  The primary difference is the pinout. The z80ctrl NG is intended to interface with the [RCBus](https://smallcomputercentral.com/rcbus/) parallel bus used primarily by Z80-based retrocomputers, whereas AVRbrain is intended as a general-purpose dev board with stacking shield-style modules or IDC cables.  z80ctrl also includes a 512K SRAM chip with optional banking that can be initialized by the AVR and then used by the 8-bit processor that z80ctrl shares a bus with.
