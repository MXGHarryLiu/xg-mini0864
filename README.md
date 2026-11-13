# xg-mini0864-firmware

Custom firmware for the XiangGuo (橡果工坊) **XG-MINI0864** WiFi pixel clock.

- MCU: ESP32-C3 (4 MB flash), native USB Serial/JTAG
- Display: 8x64 addressable LED matrix (to be confirmed)
- Stock firmware: Arduino-ESP32 (PlatformIO `lolin_c3_mini`), software v1.1

Status: reverse engineering in progress.

## Stock firmware package
`package/v1.1/xg-mini0864_stock_v1.1_flash_4MB.bin` is a full 4 MB flash image of a stock unit, with NVS, WiFi settings and old config copies removed (details in meta.txt). It has not yet been boot-tested on hardware.
Checksum, chip and partition details are in `package/v1.1/meta.txt`. Flash it with:

    esptool --chip esp32c3 write-flash 0x0 package/v1.1/xg-mini0864_stock_v1.1_flash_4MB.bin

Flash encryption and secure boot are disabled on this chip, so restoring is always possible.
