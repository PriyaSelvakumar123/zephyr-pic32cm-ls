# PIC32CM LS00 Zephyr RTOS Support

Initial Zephyr RTOS support for the **Microchip PIC32CM LS00** family, targeting the **PIC32CM5164LS00048** MCU and the **PIC32CM LS00 Curiosity Nano** development board.

> **Status:** Initial development port — SoC configuration, Devicetree, board definition, Kconfig, CMake integration, and basic build support are implemented. `samples/hello_world` builds successfully.

---

## Target Device

- **MCU:** PIC32CM5164LS00048
- **Family:** PIC32CM LS00
- **CPU:** ARM Cortex-M23
- **Architecture:** ARMv8-M Baseline
- **Maximum CPU Frequency:** 48 MHz
- **Flash:** 512 KB
- **SRAM:** 64 KB
- **Package:** 48-pin

## Development Board

**PIC32CM LS00 Curiosity Nano**

Zephyr board:

```text
pic32cm_ls00_cnano
