# ESP32-S3 with 3.5" Screen (JC3248W535C)

## Technical Specifications

* **Module:** JC3248W535C (ESP32-S3-WROOM-1).
* **Display:** 3.5" IPS, 480x320 px.
* **Driver:** AXS15231B (QSPI).
* **Memory:** 16MB Flash, 8MB PSRAM (OPI).

## Arduino CLI Configuration (Critical)

To ensure the code functions correctly, the compilation MUST include the PSRAM flag and correct Flash settings:

```powershell
arduino-cli compile --fqbn esp32:esp32:esp32s3:CDCOnBoot=cdc,USBMode=hwcdc,PSRAM=opi,FlashSize=16M

```

## Display Pinout (QSPI)

* **CS:** 45
* **SCLK:** 47
* **D0:** 21, **D1:** 48, **D2:** 40, **D3:** 39
* **Backlight:** GPIO 1

## Visual Troubleshooting (Know-How)

1. **Canvas Mode:** The AXS15231B driver via QSPI **requires** the use of `Arduino_Canvas`. Drawing directly to the screen object will cause synchronization errors (white screen or artifacts).
2. **PSRAM:** The Canvas buffer must be allocated in PSRAM (hence the `PSRAM=opi` flag).
3. **Flush:** Every screen update requires calling `gfx->flush()` to push data from the buffer to the display controller.
4. **Colors:** If colors appear inverted, toggle the `IPS` parameter in the `Arduino_AXS15231B` constructor (currently set to `false`).

## Initialization Code (Stable)

```cpp
Arduino_DataBus *bus = new Arduino_ESP32QSPI(45, 47, 21, 48, 40, 39);
Arduino_GFX *g = new Arduino_AXS15231B(bus, -1, 0, false, 320, 480);
Arduino_Canvas *gfx = new Arduino_Canvas(320, 480, g);

void setup() {
  pinMode(1, OUTPUT); digitalWrite(1, HIGH); // Backlight
  gfx->begin();
  gfx->fillScreen(0x0000);
  gfx->flush();
}