# Developer Guide: Getting Started with ESP32-S3 JC3248W535C (3.5" IPS)

> [!CAUTION]
> **PLEASE NOTE:** This module is **not compatible** with "ESP32 Marauder" or other common hacker tools/firmwares found online. Those tools are designed for cheaper SPI displays (ST7796/ILI9488). 
> The **JC3248W535C** uses a rare **QSPI AXS15231B** controller. If you flash standard firmwares, the screen will remain black. This guide shows how to handle this unique hardware correctly.

If you've just bought the **JC3248W535C** (often labeled as Guition or DIYmalls 3.5"), you've probably noticed that standard libraries like `TFT_eSPI` don't work out of the box. Here is why and how to fix it.

## The Technical Challenge
Unlike common SPI displays, this module uses a **QSPI (Quad-SPI)** interface for the **AXS15231B** controller. This allows for high-speed data transfer but requires precise pin mapping and specialized drivers.

## Prerequisites
1.  **Library:** Install **"GFX Library for Arduino"** by Moon On Our Nation (v1.6.5+).
2.  **Hardware Setting:** You **MUST** enable **OPI PSRAM**. Without it, you cannot allocate a large enough buffer for the 480x320 display.

## Step-by-Step Solution

### 1. Initialize the QSPI Bus
The display uses 6 pins for data. Standard SPI libraries won't handle this. You need to use the `Arduino_ESP32QSPI` class.

### 2. Use the Correct Driver
The AXS15231B requires specific initialization sequences. In `Arduino_GFX`, use the `Arduino_AXS15231B` driver.

### 3. The "Canvas" Secret (Crucial!)
To avoid "tearing" or white screens, do not draw directly to the display object.
*   Create an `Arduino_Canvas` object.
*   This creates a frame buffer in **PSRAM**.
*   Draw everything to the canvas, then call `gfx->flush()` to push the entire frame to the screen at once.

## Arduino CLI / IDE Settings
When compiling, use these flags (crucial for PSRAM and Flash size):
`--fqbn esp32:esp32:esp32s3:CDCOnBoot=cdc,USBMode=hwcdc,PSRAM=opi,FlashSize=16M`

## Pin Mapping Summary
*   **Display QSPI:** CS: 45, SCK: 47, D0: 21, D1: 48, D2: 40, D3: 39
*   **Backlight:** GPIO 1 (Set to HIGH to turn on)
*   **Touch I2C:** SDA: 4, SCL: 8, RST: 38, INT: 3 (Address: 0x3B)

## Example Code snippet
```cpp
Arduino_DataBus *bus = new Arduino_ESP32QSPI(45, 47, 21, 48, 40, 39);
Arduino_GFX *g = new Arduino_AXS15231B(bus, -1, 0, false, 320, 480);
Arduino_Canvas *gfx = new Arduino_Canvas(320, 480, g);

void setup() {
  pinMode(1, OUTPUT); digitalWrite(1, HIGH); // Backlight
  gfx->begin();
  gfx->fillScreen(BLACK);
  gfx->flush();
}
```
