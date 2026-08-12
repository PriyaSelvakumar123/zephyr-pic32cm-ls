# PIC32CM LS00 Zephyr RTOS Support

Initial Zephyr RTOS support for the **Microchip PIC32CM LS00 family**, targeting the **PIC32CM5164LS00048 MCU** and the **PIC32CM LS00 Curiosity Nano development board**.

> **Status:** Initial development port. Devicetree, SoC configuration, board definition, Kconfig, CMake integration, and basic build support are implemented. `samples/hello_world` builds successfully.

---

## Target Device

| Parameter                 | Details            |
| ------------------------- | ------------------ |
| **MCU**                   | PIC32CM5164LS00048 |
| **Family**                | PIC32CM LS00       |
| **CPU**                   | ARM Cortex-M23     |
| **Architecture**          | ARMv8-M Baseline   |
| **Maximum CPU Frequency** | 48 MHz             |
| **Flash**                 | 512 KB             |
| **SRAM**                  | 64 KB              |
| **Package**               | 48-pin             |

## Development Board

**PIC32CM LS00 Curiosity Nano**

* **Zephyr Board:** `pic32cm_ls00_cnano`
* **Board Qualifier:** `pic32cm5164ls00048`

---

## Project Objective

The objective of this project is to add initial **PIC32CM LS00 support to Zephyr RTOS**.

The implementation provides the basic platform infrastructure required to build Zephyr applications for the PIC32CM LS00 device.

### Implemented

* PIC32CM LS common SoC support
* PIC32CM5164LS00048 device support
* PIC32CM LS00 Curiosity Nano board support
* ARM Cortex-M23 configuration
* 48 MHz CPU configuration
* 64 KB SRAM definition
* 512 KB Flash definition
* Devicetree configuration
* SoC Kconfig configuration
* SoC CMake integration
* Board Kconfig/defconfig
* Basic Zephyr application build support
* `samples/hello_world` build verification
* `zephyr.elf` generation

---

## Repository Structure

```text
zephyr-pic32cm-ls/
│
├── boards/
│   └── microchip/
│       └── pic32c/
│           └── pic32cm_ls00_cnano/
│               ├── pic32cm_ls00_cnano.dts
│               └── pic32cm_ls00_cnano_defconfig
│
├── dts/
│   └── arm/
│       └── microchip/
│           └── pic32c/
│               └── pic32cm_ls/
│                   ├── common/
│                   │   └── pic32cm_ls.dtsi
│                   │
│                   └── pic32cm_ls00/
│                       └── pic32cm5164ls00048.dtsi
│
└── soc/
    └── microchip/
        └── pic32c/
            └── pic32cm_ls/
                ├── CMakeLists.txt
                ├── Kconfig.soc
                │
                └── pic32cm_ls00/
                    ├── CMakeLists.txt
                    └── Kconfig.soc
```

---

## Memory Configuration

The PIC32CM5164LS00048 is configured with the following memory layout.

### Flash

```text
Start Address : 0x00000000
Total Size    : 512 KB
End Address   : 0x0007FFFF
```

### SRAM

```text
Start Address : 0x20000000
Total Size    : 64 KB
End Address   : 0x2000FFFF
```

The Zephyr linker configuration separates the boot/ROM region from the application Flash region.

```text
ROMSTART_REGION : 16 KB
FLASH           : 496 KB
RAM             : 64 KB
```

---

## CPU Configuration

The PIC32CM LS common Devicetree defines the Cortex-M23 CPU:

```dts
cpu0: cpu@0 {
    device_type = "cpu";
    compatible = "arm,cortex-m23";
    reg = <0>;
    clock-frequency = <48000000>;
};
```

### CPU Frequency

```text
48 MHz
```

---

## SRAM Configuration

The 64 KB SRAM is defined as:

```dts
sram0: memory@20000000 {
    compatible = "mmio-sram";
    reg = <0x20000000 0x10000>;
};
```

---

## Flash Configuration

The PIC32CM5164LS00048 provides **512 KB of Flash**.

```text
Flash Start : 0x00000000
Flash Size  : 0x80000
Flash Total : 512 KB
```

The final linker layout provides:

```text
ROMSTART_REGION : 16 KB
FLASH           : 496 KB
RAM             : 64 KB
```

---

## Build Environment

The port was developed and tested using:

| Tool           | Version               |
| -------------- | --------------------- |
| **Zephyr**     | 4.4.1                 |
| **West**       | 1.5.0                 |
| **Zephyr SDK** | 1.0.1                 |
| **CMake**      | 4.2.3                 |
| **DTC**        | 1.7.2                 |
| **Python**     | 3.14.4                |
| **GCC**        | ARM Zephyr GCC 14.3.0 |
| **Host**       | Ubuntu / WSL          |

---

## Building the Sample

Enter the Zephyr workspace:

```bash
cd ~/zephyrproject/zephyr-new
```

Build the `hello_world` sample:

```bash
west build \
  -b pic32cm_ls00_cnano \
  -d build \
  samples/hello_world \
  --pristine
```

A successful build ends with:

```text
[126/126] Linking C executable zephyr/zephyr.elf
```

### Example Memory Usage

```text
Memory region         Used Size  Region Size  %age Used
ROMSTART_REGION:         628 B        16 KB      3.83%
FLASH:                  8088 B       496 KB      1.59%
RAM:                    3816 B        64 KB      5.82%
IDT_LIST:                  0 B        32 KB      0.00%
```

---

## Build Verification

The current implementation successfully passes the basic Zephyr build stages:

```text
Devicetree generation
        ↓
Kconfig configuration
        ↓
C compilation
        ↓
Assembly compilation
        ↓
Linking
        ↓
zephyr.elf generation
```

Therefore, the current port has reached the **successful build stage**.

---

## Development Challenges

### 1. Devicetree CPU `reg` Configuration

During development, the CPU Devicetree node initially produced errors related to the `reg` property.

Errors included:

```text
reg property ... has length 4
```

and:

```text
node has a unit name, but no reg or ranges property
```

The CPU node and its `#address-cells` / `#size-cells` configuration were corrected to produce a valid Devicetree.

---

### 2. System Clock Configuration

The initial build failed with:

```text
#error "SYS_CLOCK_HW_CYCLES_PER_SEC must be non-zero!"
```

This indicated that the Zephyr system clock configuration was incomplete.

The CPU clock frequency was defined as:

```text
48 MHz
```

and the required SoC configuration was updated.

After this change, the project successfully compiled and linked.

---

### 3. GPIO Driver

The current build produces the following warning:

```text
No SOURCES given to Zephyr library: drivers__gpio
Excluding target from build.
```

This indicates that a **PIC32CM LS GPIO driver has not yet been implemented**.

Therefore, the current implementation provides basic SoC and board build support, but GPIO functionality has not yet been validated.

---

### 4. Hardware Validation

Successful compilation does not confirm that the generated firmware executes correctly on the physical board.

The following hardware-level validation is still required:

```text
Build
  ↓
Flash
  ↓
Boot
  ↓
UART / LED verification
  ↓
Peripheral verification
```

---

## Current Status

### Completed

* [x] PIC32CM LS common SoC structure
* [x] PIC32CM5164LS00048 device support
* [x] Cortex-M23 configuration
* [x] 48 MHz CPU configuration
* [x] 64 KB SRAM definition
* [x] 512 KB Flash definition
* [x] Devicetree configuration
* [x] SoC Kconfig
* [x] SoC CMake integration
* [x] PIC32CM LS00 Curiosity Nano board definition
* [x] Board defconfig
* [x] `samples/hello_world` build
* [x] `zephyr.elf` generation

### Not Yet Completed

* [ ] PIC32CM LS GPIO driver
* [ ] On-board LED verification
* [ ] UART driver verification
* [ ] Hardware timer verification
* [ ] Interrupt/peripheral validation
* [ ] Flashing to physical Curiosity Nano
* [ ] Hardware `hello_world` verification
* [ ] Additional peripheral drivers
* [ ] Full hardware validation
* [ ] Zephyr upstream review and cleanup

---

## Next Steps

The planned development sequence is:

```text
1. Verify flashing
       ↓
2. Implement GPIO driver
       ↓
3. Test onboard LED
       ↓
4. Implement / verify UART
       ↓
5. Run hello_world on hardware
       ↓
6. Verify system timer
       ↓
7. Add additional peripherals
       ↓
8. Run Zephyr checks
       ↓
9. Improve documentation
       ↓
10. Prepare upstream contribution
```

---

## Hardware Validation

The current implementation has been **build-tested successfully**.

Hardware validation is still required for:

* Firmware flashing
* CPU boot verification
* On-board LED control
* UART output
* System timer operation
* Interrupt handling
* Peripheral functionality

### Next Major Milestone

```text
Build → Flash → Boot → UART / LED Verification
```

---

## Git Branch

Development branch:

```text
pic32cm-ls
```

Base Zephyr version:

```text
v4.4.1
```

---

## Project Status

```text
PIC32CM LS00 Zephyr Port
        │
        ├── SoC Support          ✓
        ├── Device Support       ✓
        ├── Board Support        ✓
        ├── Devicetree           ✓
        ├── Kconfig              ✓
        ├── CMake Integration    ✓
        ├── Build Verification   ✓
        │
        ├── GPIO Driver          Pending
        ├── UART Validation      Pending
        ├── LED Validation       Pending
        ├── Hardware Flashing    Pending
        └── Upstream Submission  Pending
```

---

## Repository

**GitHub Repository:**
https://github.com/PriyaSelvakumar123/zephyr-pic32cm-ls

---

## Author

**Priya Selvakumar**

Electronics and Communication Engineering

**Embedded Systems | Zephyr RTOS | Embedded C**

---
## License

This work follows the licensing conventions of the **Zephyr Project**.

Source files use the appropriate SPDX license identifier:

```text
SPDX-License-Identifier: Apache-2.0
```

This repository represents an **initial development port of PIC32CM LS00 support for Zephyr RTOS**. It is not yet intended to represent complete production-ready support.

Hardware validation and peripheral driver development are ongoing.
