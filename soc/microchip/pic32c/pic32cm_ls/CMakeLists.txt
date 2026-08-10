# Copyright (c) 2026 Microchip Technology Inc.

# SPDX-License-Identifier: Apache-2.0

add_subdirectory(common)

zephyr_include_directories(${SOC_SERIES})

zephyr_include_directories(
    "/mnt/c/Program Files/Microchip/MPLABX/v6.30/packs/Microchip/PIC32CM-LS_DFP/1.3.278/PIC32CM-LS00/include"
)
