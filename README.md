# STM32F103C8-ADS1292R-Sensor

A minimal STM32Cube-based firmware for STM32F103C8 ("Blue Pill") that interfaces with the TI ADS1292R (2‑channel ECG/respiration AFE) over SPI and streams raw samples over USB CDC as a virtual COM port, built with PlatformIO.

## Build requirements

- PlatformIO Core (or VS Code + PlatformIO extension)
- ST‑LINK drivers _for ST-LINK flashing/debugging (the default option)_
- STM32CubeMX _if you wish to adapt to a different MCU or layout_

## Changing configuration

- `Core/Src/ads1292r.c`: ADS1292R settings (sample rate, gains, inputs, test signal)
  - Default settings are for ECG on CH1 (gain 4) and respiration on CH2 (gain 8) at 500 SPS.
- `Core/Src/main.c`: USB CDC transmit logic, DRDY EXTI handler
- `ADS1292R.ioc`: CubeMX project file for pin/config changes, or if porting to another STM32 MCU.

## USB data format

- Stream: 8 bytes per sample, little‑endian
- Layout: two signed 32‑bit integers `<int32 ch1, int32 ch2>`
  - Each is a sign‑extended 24‑bit reading from ADS1292R

### Minimal Python reader (pyserial)

```python
import serial
PORT = 'COM7'  # change to your port
ser = serial.Serial(PORT, 115200, timeout=0.01)
while True:
    ch1 = ser.read(4)
    ch2 = ser.read(4)
    ch1_int = int.from_bytes(ch1, byteorder='little', signed=True)
    ch2_int = int.from_bytes(ch2, byteorder='little', signed=True)
    print(f'CH1: {ch1_int}, CH2: {ch2_int}')
```

## Acknowledgments

- Code for ADS1292R SPI interface adapted from [sjZhu1020/STM32-HAL-ADS1292R](https://github.com/sjZhu1020/STM32-HAL-ADS1292R)
