# xg-mini0864

Custom firmware for the XiangGuo (橡果工坊) **XG-MINI0864** WiFi pixel clock.

- Stock firmware: Arduino-ESP32 (PlatformIO `lolin_c3_mini`), software v1.1

## Hardware

- ESP32-C3 (rev v0.4, 160 MHz), 4 MB flash, native USB Serial/JTAG (VID 303A, PID 1001)
- WiFi (station plus AP, web UI with OTA) and BLE 5 (unused by the stock firmware)
- 8x64 addressable LED matrix (Arduino_GFX, 64x16 canvas over a 64x8 output)
- I2S microphone, 3 touch buttons, passive buzzer, photoresistor, RTC, AHT20 temperature/humidity sensor

Stock features: clock with NTP (aliyun), AMap weather, 5 alarms, countdown/count-up timer, brightness (manual or auto), music spectrum.

## GPIO map

| GPIO | Function | Notes |
|---|---|---|
| 0 | Light sensor (photoresistor) | ADC1_CH0, about 30 dark, 480 room, 2900 flashlight. The stock I2S config names GPIO0 as mic MCLK, but it is not wired to the mic |
| 1 | Microphone data in | I2S |
| 2 | Touch button, right | active-low |
| 3 | Buzzer | passive, drive with a tone |
| 4 | I2C SDA | RTC at 0x32, AHT20 at 0x38 |
| 5 | I2C SCL | |
| 6 | Microphone WS | I2S |
| 7 | LED matrix data | WS2812-style, GRB order, 800 kHz. A 512-pixel stream lit the whole screen |
| 8 | Touch button, left | active-low, strapping pin |
| 9 | Touch button, middle | active-low, strapping pin |
| 10 | Microphone BCLK | I2S |
| 18, 19 | USB D-, D+ | native USB Serial/JTAG |

Not mapped yet: GPIO20 and GPIO21 (the UART0 pins, probably unused).

## LED matrix layout

64 columns by 8 rows, 512 pixels on one data line (GPIO7). Pixel 0 is the bottom-left corner. Every column runs bottom to top (no zigzag), and columns advance left to right, so the last pixel (511) is top-right.

    index = x * 8 + (7 - y)        x = 0..63 from the left, y = 0..7 from the top

Tested at brightness 8 of 255. Even 16 is already quite bright for green on this panel.

## I2C devices

- 0x32: RTC, BCD time registers from 0x00 (sec, min, hour, weekday, day, month, year), 24 h mode flag at 0x0E. Layout fits an Epson RX8025 family chip (part number unconfirmed).
- 0x38: AHT20 temperature and humidity sensor (status 0x18, measure with `AC 33 00`). It reads a few degrees above room temperature because it sits next to the ESP32 and LEDs.

Nothing else answered on the bus, so there is no I2C accelerometer.

## Stock firmware package
`package/v1.1/xg-mini0864_stock_v1.1_flash_4MB.bin` is a full 4 MB flash image of a stock unit, with NVS, WiFi settings and old config copies removed (details in meta.txt). It has not yet been boot-tested on hardware.
Checksum, chip and partition details are in `package/v1.1/meta.txt`. Flash it with:

    esptool --chip esp32c3 write-flash 0x0 package/v1.1/xg-mini0864_stock_v1.1_flash_4MB.bin

Flash encryption and secure boot are disabled on this chip, so restoring is always possible.
