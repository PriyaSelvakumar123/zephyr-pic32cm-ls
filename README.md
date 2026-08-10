PIC32CM LS00 Zephyr RTOS Support

Initial Zephyr RTOS support for the Microchip PIC32CM LS00 family, targeting the PIC32CM5164LS00048 MCU and PIC32CM LS00 Curiosity Nano development board.

Status: Initial port — Devicetree, SoC configuration, board definition, and basic build support are implemented. samples/hello_world builds successfully.

Target Device

MCU: PIC32CM5164LS00048
Family: PIC32CM LS00
CPU: ARM Cortex-M23
Architecture: ARMv8-M Baseline
Maximum CPU Frequency: 48 MHz
Flash: 512 KB
SRAM: 64 KB
Package: 48-pin

Development Board

PIC32CM LS00 Curiosity Nano

Target board:

pic32cm_ls00_cnano

Target qualifier:

pic32cm5164ls00048
Project Objective

The objective of this work is to add initial support for the PIC32CM LS00 family to Zephyr RTOS.

The port includes:

PIC32CM LS common SoC support
PIC32CM5164LS00048 device support
Curiosity Nano board support
ARM Cortex-M23 configuration
Devicetree definitions
Flash and SRAM memory definitions
Kconfig configuration
SoC CMake integration
Basic Zephyr application build verification
Repository Structure

The main files added or modified are:

boards/
└── microchip/
    └── pic32c/
        └── pic32cm_ls00_cnano/
            ├── pic32cm_ls00_cnano.dts
            └── pic32cm_ls00_cnano_defconfig

dts/
└── arm/
    └── microchip/
        └── pic32c/
            └── pic32cm_ls/
                ├── common/
                │   └── pic32cm_ls.dtsi
                └── pic32cm_ls00/
                    └── pic32cm5164ls00048.dtsi

soc/
└── microchip/
    └── pic32c/
        └── pic32cm_ls/
            ├── CMakeLists.txt
            ├── Kconfig.soc
            ├── common/
            └── pic32cm_ls00/
                ├── CMakeLists.txt
                └── Kconfig.soc
Memory Configuration

The PIC32CM5164LS00048 memory configuration used in the port is:

Flash:
0x00000000 - 0x0007FFFF
Total: 512 KB

SRAM:
0x20000000 - 0x2000FFFF
Total: 64 KB

The application flash region is configured separately from the boot/ROM region in the final linker memory layout.

The successful build reports:

ROMSTART_REGION:   628 B / 16 KB
FLASH:            8088 B / 496 KB
RAM:              3816 B / 64 KB
Important Changes
1. CPU Configuration

The common PIC32CM LS Devicetree defines the Cortex-M23 CPU:

cpu0: cpu@0 {
    device_type = "cpu";
    compatible = "arm,cortex-m23";
    reg = <0>;
    clock-frequency = <48000000>;
};
2. SRAM

The device contains 64 KB SRAM:

sram0: memory@20000000 {
    compatible = "mmio-sram";
    reg = <0x20000000 0x10000>;
};
3. Flash

The PIC32CM5164LS00048 device Devicetree defines the 512 KB flash space:

flash0: flash@0 {
    compatible = "soc-nv-flash";
    reg = <0x0 0x80000>;
};
4. Clock

The initial port configures the CPU clock at:

48 MHz

This was necessary because Zephyr initially reported:

SYS_CLOCK_HW_CYCLES_PER_SEC must be non-zero!

The CPU clock-frequency and corresponding SoC configuration were added to resolve this issue.

Build Environment

This work was developed using:

Zephyr:       4.4.1
West:         1.5.0
Zephyr SDK:   1.0.1
CMake:        4.2.3
DTC:          1.7.2
Python:       3.14.4
Compiler:     ARM Zephyr GCC 14.3.0

Host environment:

Ubuntu / WSL
Building the Sample

From the Zephyr workspace:

cd ~/zephyrproject/zephyr-new

Build the Hello World sample:

west build \
  -b pic32cm_ls00_cnano \
  -d build \
  samples/hello_world \
  --pristine

A successful build should finish with:

[126/126] Linking C executable zephyr/zephyr.elf

and a memory report similar to:

ROMSTART_REGION:   628 B
FLASH:            8088 B
RAM:              3816 B
Build Verification

The initial port has successfully passed the following stage:

Devicetree generation       PASS
Kconfig configuration       PASS
C compilation               PASS
Assembly compilation        PASS
Linking                     PASS
zephyr.elf generation      PASS

Therefore, the current implementation has reached the successful build stage.

Development Challenges

During the porting process, several issues were encountered.

1. Devicetree CPU reg Error

Initially, the CPU node had an incorrect reg configuration, resulting in errors related to:

reg property ... has length 4

and:

node has a unit name, but no reg or ranges property

The CPU Devicetree structure was corrected to match the parent #address-cells and #size-cells configuration.

2. System Clock Error

The build initially failed with:

#error "SYS_CLOCK_HW_CYCLES_PER_SEC must be non-zero!"

This indicated that the Zephyr system clock configuration was incomplete.

The CPU clock was defined as:

48 MHz

and the SoC configuration was updated accordingly.

3. GPIO Driver Warning

The build currently displays:

No SOURCES given to Zephyr library: drivers__gpio
Excluding target from build.

This means GPIO driver support has not yet been implemented for the PIC32CM LS port.

The current successful build is therefore a basic SoC/board build, not yet a complete peripheral implementation.

4. Git Repository Size

The initial attempt to push the complete Zephyr source tree resulted in a very large Git operation because the working directory was based on the full Zephyr repository.

The actual PIC32CM changes are represented by the new commits on the development branch.

Current Status
Completed
 PIC32CM LS SoC directory
 PIC32CM LS common Devicetree
 PIC32CM5164LS00048 Devicetree
 Cortex-M23 configuration
 48 MHz CPU configuration
 64 KB SRAM definition
 512 KB flash definition
 SoC Kconfig
 SoC CMake integration
 PIC32CM LS00 Curiosity Nano board definition
 Board defconfig
 samples/hello_world build
 zephyr.elf generation
Not Yet Completed
 PIC32CM LS GPIO driver
 LED blink verification
 UART driver verification
 Timer/clock hardware verification
 Interrupt/peripheral validation
 Flashing to physical Curiosity Nano
 Hardware hello_world verification
 Additional peripheral support
 Zephyr upstream review and cleanup
Next Steps

The recommended development sequence is:

1. Verify flashing
        ↓
2. Implement GPIO driver
        ↓
3. Test onboard LED
        ↓
4. Implement/verify UART
        ↓
5. Test Hello World on hardware
        ↓
6. Verify system timer
        ↓
7. Add remaining peripherals
        ↓
8. Add documentation
        ↓
9. Run Zephyr checks
        ↓
10. Prepare upstream contribution
Hardware Validation

The current implementation has been build-tested, but successful compilation does not by itself prove that the firmware runs correctly on the physical PIC32CM LS00 board.

The next important milestone is:

Build → Flash → Boot → UART/LED verification
Git Branch

The development branch used for this work is:

pic32cm-ls

The original Zephyr release used as the starting point is:

v4.4.1
Repository

GitHub repository:

zephyr-pic32cm-ls

Disclaimer

This repository contains an initial development port of PIC32CM LS00 support for Zephyr RTOS. It is not yet intended to represent complete production-ready support.

Hardware validation and peripheral driver development are ongoing.

Author

Priya Selvakumar

Electronics and Communication Engineering
Embedded Systems / Zephyr RTOS Development

License

This work follows the licensing conventions of the Zephyr Project. Individual files should retain the appropriate SPDX license identifier.

SPDX-License-Identifier: Apache-2.0
