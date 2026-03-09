# ESP32-S3 Finger Painting (JC3248W535C)

Projekt testowy dla modułu **JC3248W535C** (ESP32-S3-WROOM-1 z ekranem 3.5" IPS 480x320 px). Program umożliwia rysowanie palcem po ekranie z wyborem kolorów i funkcją czyszczenia.

## Specyfikacja Techniczna
*   **Moduł:** JC3248W535C
*   **Wyświetlacz:** 3.5" IPS, 480x320 px (Sterownik AXS15231B QSPI)
*   **Dotyk:** Pojemnościowy (I2C AXS15231B)
*   **Pamięć:** 16MB Flash, 8MB PSRAM (OPI)

## Pinologia
### Wyświetlacz (QSPI)
*   **CS:** 45, **SCLK:** 47
*   **D0:** 21, **D1:** 48, **D2:** 40, **D3:** 39
*   **Backlight:** GPIO 1

### Dotyk (I2C)
*   **SDA:** 4, **SCL:** 8
*   **Reset:** 38, **INT:** 3
*   **Adres I2C:** 0x3B

## Kompilacja (Arduino CLI)
Aby projekt działał poprawnie, należy skompilować go z obsługą **OPI PSRAM**:
```powershell
arduino-cli compile --fqbn esp32:esp32:esp32s3:CDCOnBoot=cdc,USBMode=hwcdc,PSRAM=opi,FlashSize=16M
```

## Funkcje
*   **Rysowanie:** Dotknij ekranu, aby malować.
*   **Paleta kolorów:** 5 kolorów na dole ekranu (Czerwony, Zielony, Niebieski, Żółty, Biały).
*   **Clear:** Przycisk u góry czyści cały obszar roboczy (wymaga przytrzymania ~200ms).
*   **Stabilizacja:** Filtr błędnych odczytów dotyku (0,0) dla płynniejszego rysowania.

## Biblioteki
Wymagana biblioteka: **GFX Library for Arduino** (v1.6.5 lub nowsza).
