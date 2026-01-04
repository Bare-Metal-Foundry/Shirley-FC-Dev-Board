# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a custom flight controller hardware design project centered around the STM32H743ZIT6 microcontroller. The repository contains hardware schematics, PCB layouts, firmware configuration, and comprehensive documentation for building a high-performance flight controller.

## Repository Structure

```
├── hardware/
│   ├── KiCad/FC_v1.0/          # KiCad 9.0 schematic and PCB files
│   │   ├── FC-proto.kicad_sch  # Main schematic (hierarchical)
│   │   ├── FC-power-section.kicad_sch
│   │   ├── FC-interfaces_sch.kicad_sch
│   │   ├── FC-Sensors.kicad_sch
│   │   └── FC-proto.kicad_pcb
│   └── cad-layout/             # Custom footprints for specific components
├── firmware/
│   └── STM32 Cube IDE/         # STM32CubeMX .ioc configuration
├── config/
│   ├── pinout.yaml             # STM32H743 peripheral pinout 
│   └── power.yaml              # Power rail specifications
├── docs/
│   ├── hardware/               # Hardware subsystem documentation
│   │   ├── overview.md
│   │   ├── power-architecture.md
│   │   ├── design-decisions.md
│   │   └── stm32-subsystem/
│   └── README.md               # Atomic documentation philosophy
└── resources/
    ├── datasheets/             # Organized by component type (BAR, CAN, IMU, etc.)
    └── manufacturer-notes/
```

## Hardware Architecture

### Core MCU
- **STM32H743ZIT6** (480 MHz Cortex-M7, FPU, DSP)
- All peripheral assignments defined in `config/pinout.yaml`
- Hierarchical schematic design with separate sheets for power, interfaces, and sensors

### Key Sensors
- **ICM-42688-P** (Primary IMU): Low-noise 6-axis IMU with up to 32 kHz ODR, connected via SPI
- **MMC5983MA** (Magnetometer): 18-bit high-precision magnetometer with built-in degaussing
- See `docs/hardware/design-decisions.md` for sensor selection rationale

### Power Architecture
The power system uses a hierarchical rail structure:
- **5V_SYS**: 2.5A (via TPS2113A ideal-diode MUX with priority: EXT_5V > USB)
- **3V3_DIG**: 2A (buck regulator TPS62130RGTR for digital logic)
- **3V3_ANA**: 300 mA (ferrite bead filter from 3V3_DIG for analog/sensor clean power)

Critical note: VDD33_USB must be supplied 3.3V even if USB is not actively used.

### Peripheral Pinout
(from `config/pinout.yaml` v0.7 - 2025-01-04)

#### Serial Communication
- **UART4** (Telemetry): TX: PA0, RX: PA1, CTS: PB0, RTS: PB14
- **UART5** (Mini Pad Out UART): TX: PB13, RX: PB12
- **UART8** (GPS): TX: PE1, RX: PE0
- **USART2** (Pad Out USART): TX: PA2, RX: PA3
- **USART6** (RC Receiver): TX: PC6, RX: PC7

#### SPI Buses
- **SPI4** (Magnetometer): SCK: PE2, MISO: PE5, MOSI: PE6
- **SPI5** (IMU): SCK: PF7, MISO: PF8, MOSI: PF9

#### I2C Buses
- **I2C1** (I2C Connector - External Compass): SDA: PB7, SCL: PB6
- **I2C2** (Barometer): SDA: PF0, SCL: PF1
- **I2C4** (Pad Out I2C): SDA: PF15, SCL: PF14

#### CAN Bus
- **FDCAN1** (GPS CAN Bus): RX: PD0, TX: PB9

#### Analog Inputs
- **ADC1** (Battery Voltage/Current Sensor): INP2: PF11 (Voltage), INP3: PA6 (Current)
- **ADC2** (Pad Out ADCs): INP2: PF13, INP4: PC4, INP5: PB1

#### PWM/Timer Outputs
- **TIM1** (Motors ESC PWM/DShot): CH1: PE9, CH2: PE11, CH3: PE13, CH4: PE14
- **TIM4** (RGB LED): CH1: PD12 (Red), CH2: PD13 (Green), CH3: PD14 (Blue)

#### Storage & USB
- **SDMMC1** (SD Card): D0: PC8, D1: PC9, D2: PC10, D3: PC11, CK: PC12, CMD: PD2, CDS: PD3
- **USB_OTG_FS** (USB-C): DM: PA11, DP: PA12, VBUS: PA9

#### System GPIOs
- **SYS_LED** (System Status LED): PD6
- **EXT_SWITCH** (External System Switch): PE3
- **PWR_GOOD** (Power Good Signal - Buck 3V3 rail): PD9
- **BUZZER** (External Active Buzzer): PC3_C
- **EXTERNAL_GPIOS** (Pad Out GPIOs): GPIO_68: PE15, GPIO_69: PB10, GPIO_70: PB11

#### Debug Interface
- **SWD**: SWDIO: PA13, SWCLK: PA14
- **SWO**: PB3 (JTDO/TRACESWO)
- **HSE** (External oscillator): OSC_IN: PH0, OSC_OUT: PH1

## Working with Hardware Files

### KiCad Schematics
- Project uses **KiCad 9.0** format
- Main schematic: `hardware/KiCad/FC_v1.0/FC-proto.kicad_sch`
- Hierarchical design with subsystem sheets:
  - Power section
  - Interface subsystem (UART, CAN, USB, SD card)
  - Sensor subsystem
- Custom footprints in `hardware/cad-layout/` for components like:
  - STM32H743ZIT6, ICM-42688-P, MMC5983MA
  - Power ICs (TPS2113A, TPS62130RGTR)
  - Connectors (SM03B-GHS-TB, FTSH-105-01-F-DV-K)

### Firmware Configuration
- STM32CubeMX project: `firmware/STM32 Cube IDE/FC-proto-El.ioc`
- Open this file with STM32CubeMX to regenerate initialization code
- Pin configuration should match `config/pinout.yaml`

## Configuration Management

All configurations are stored in YAML format in `config/`:

### pinout.yaml
Defines all STM32H743 peripheral pin assignments. When modifying hardware:
1. The user update source files (KiCad, STM32CubeIDE)
2. this pinout file is updated to match source files


### power.yaml
Power rail specifications (currently empty template). Should contain:
- Voltage rail definitions
- Current budgets
- Regulator specifications

## Workflow and File Modification Guidelines

This project follows a strict workflow hierarchy to maintain consistency across hardware, configuration, and documentation:

### Source Files (User-Modified Only)
The following files should **only be modified by the user** using their dedicated software tools:
- **KiCad files** (`hardware/KiCad/FC_v1.0/*.kicad_sch`, `*.kicad_pcb`): Must be edited in KiCad
- **STM32 Cube IDE files** (`firmware/STM32 Cube IDE/*.ioc`): Must be edited in STM32CubeMX/CubeIDE

**Claude should never directly modify these files.**

### Configuration Files (Claude-Modifiable)
Files in `/config` can be modified and should be updated to reflect changes made in the source files:
- **`config/pinout.yaml`**: Update this after making pin assignment changes in STM32CubeMX or KiCad schematics
- **`config/power.yaml`**: Update this after modifying power architecture in KiCad schematics

### Documentation Files (Claude-Modifiable)
Files in `/docs` should be updated according to changes in `/config`:
- Documentation should reflect the current state defined in YAML configuration files
- When configurations change, corresponding documentation must be updated to match

### Update Flow
```
[User modifies KiCad/STM32CubeMX] → [Update /config/*.yaml] → [Update /docs/*.md]
```

This ensures a single source of truth: the user-controlled hardware design files, with derived configuration and documentation that can be maintained programmatically.

## Documentation Philosophy

### Core Principles
From `docs/README.md`:
- **Atomic documentation**: Each subsystem documented independently
- **YAML configs**: All configurations in YAML format
- **LLM-optimized**: Documentation structured for AI consumption
- **Conciseness**: Keep documentation as concise as possible
- **No duplication**: Use references and links instead of duplicating information across `/docs`

### Content Organization
- **Design decisions**: All design rationale goes in `docs/hardware/design-decisions.md`
- **Future development ideas**: All forward-looking plans go in `docs/future-developments.md`
- **Component documentation**: Focus on implementation details, not feature catalogs
  - Example: `buck-converter.md` should describe how the buck converter is implemented in the PCB design, not list all component features

### Adding New Subsystems
When documenting new hardware blocks:
1. Create separate markdown files for each subsystem
2. Store component specifications in YAML where possible
3. Add datasheets to `resources/datasheets/` in the appropriate category
4. Focus on implementation specifics rather than component feature lists

## Component Datasheets

Organized by category in `resources/datasheets/`:
- BAR (barometer), CAN, CONNECTOR, CRYSTAL, ESD, FUSE
- IMU, LED, MAG (magnetometer), POWER, SD_CARD
- STM32, SWITCH, USB-C

When referencing components, datasheets are always available in these subdirectories.

## Version Control

From `.gitignore`:
- Reference designs excluded: `resources/reference-designs/`
- KiCad lock files (e.g., `~FC-proto.kicad_sch.lck`) should not be committed

## Design Intent

This is a **first revision prototype** focused on:
- High-quality sensor data with minimal noise
- Robust power architecture with clean analog rails
- Low-risk component selection (well-characterized parts)
- Future expansion capability (room for additional IMUs, magnetometers)

The goal is to establish a stable hardware foundation for firmware and control algorithm development, not to maximize features in the first iteration.
